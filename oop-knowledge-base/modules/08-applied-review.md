# 8. Applied Review — Notification System

> **Thời lượng:** 60–90 phút  
> **Prerequisites:** [Module 1](01-oop-mental-model.md), [Module 2](02-class-object-lifecycle.md), [Module 3](03-encapsulation-abstraction.md), [Module 4](04-object-relationships-composition.md), [Module 5](05-inheritance-polymorphism.md), [Module 6](06-cpp-ownership-lifetime-raii.md), [Module 7](07-solid-essentials.md)  
> **Status:** Reviewed

## Overview

Module cuối kết hợp mental model, Encapsulation, Composition, Polymorphism, ownership và SOLID trong một vertical slice nhỏ. Mục tiêu không phải tạo production notification platform; mục tiêu là giải thích được từng class tồn tại vì requirement nào và từ chối abstraction không cần thiết.

Scenario ban đầu hỗ trợ Email và SMS. Change request thêm Push Notification mà không sửa orchestration chính.

## Learning Outcomes

Sau module này, người học có thể:

- chuyển requirement nhỏ thành objects, responsibilities và relationships;
- thiết kế contract runtime có ownership rõ;
- thêm implementation mới qua extension point hợp lý;
- review design bằng invariants, coupling và SOLID;
- nêu alternative đơn giản hơn và giới hạn của solution.

## 1. Mental Model và Requirements

### Functional requirements

1. Message có recipient và body không rỗng.
2. Service gửi cùng message qua mọi configured channel.
3. Ban đầu có Email và SMS.
4. Change request thêm Push Notification.

### Scope boundaries

- Không retry, queue, persistence hoặc networking thật.
- Không model user preferences hoặc template localization.
- Console output đại diện external provider.
- Failure policy là fail-fast: channel ném exception thì flow dừng.

### Responsibility map

| Type | Responsibility |
|---|---|
| `NotificationMessage` | Giữ valid recipient/body |
| `NotificationChannel` | Contract gửi qua một channel |
| Concrete channels | Adapter đơn giản cho provider behavior |
| `NotificationService` | Sở hữu channels và điều phối gửi |

## 2. Core Design

### Invariants

- Message recipient/body không rỗng.
- Service không chứa null channel.
- Mỗi concrete channel giữ contract `send` hoặc báo lỗi.

### Relationships

- Service **composes** nhiều channels bằng `vector<unique_ptr<NotificationChannel>>`.
- `sendAll` có Dependency ngắn hạn vào immutable message.
- Concrete channels là subtypes của `NotificationChannel`.
- Service delegated provider-specific behavior cho từng channel.

### Vì sao runtime polymorphism hợp lý

Configured channels có thể được chọn lúc runtime và service sở hữu heterogeneous objects. Nếu set channels cố định tại compile time, template/tuple hoặc direct calls có thể đơn giản hơn.

## 3. Focused C++ Example — Complete Implementation

```cpp
#include <iostream>
#include <memory>
#include <stdexcept>
#include <string>
#include <string_view>
#include <utility>
#include <vector>

class NotificationMessage {
public:
    NotificationMessage(std::string recipient, std::string body)
        : recipient_{std::move(recipient)}, body_{std::move(body)} {
        if (recipient_.empty()) {
            throw std::invalid_argument{"Recipient is required"};
        }
        if (body_.empty()) {
            throw std::invalid_argument{"Message body is required"};
        }
    }

    [[nodiscard]] std::string_view recipient() const noexcept {
        return recipient_;
    }

    [[nodiscard]] std::string_view body() const noexcept {
        return body_;
    }

private:
    std::string recipient_;
    std::string body_;
};

class NotificationChannel {
public:
    virtual ~NotificationChannel() = default;
    virtual void send(const NotificationMessage& message) = 0;
};

class EmailChannel final : public NotificationChannel {
public:
    void send(const NotificationMessage& message) override {
        std::cout << "[Email] " << message.recipient()
                  << ": " << message.body() << '\n';
    }
};

class SmsChannel final : public NotificationChannel {
public:
    void send(const NotificationMessage& message) override {
        std::cout << "[SMS] " << message.recipient()
                  << ": " << message.body() << '\n';
    }
};

class PushChannel final : public NotificationChannel {
public:
    void send(const NotificationMessage& message) override {
        std::cout << "[Push] " << message.recipient()
                  << ": " << message.body() << '\n';
    }
};

class NotificationService {
public:
    void addChannel(std::unique_ptr<NotificationChannel> channel) {
        if (!channel) {
            throw std::invalid_argument{"Channel cannot be null"};
        }
        channels_.push_back(std::move(channel));
    }

    void sendAll(const NotificationMessage& message) {
        for (const auto& channel : channels_) {
            channel->send(message);
        }
    }

    [[nodiscard]] std::size_t channelCount() const noexcept {
        return channels_.size();
    }

private:
    std::vector<std::unique_ptr<NotificationChannel>> channels_;
};

int main() {
    NotificationService service;
    service.addChannel(std::make_unique<EmailChannel>());
    service.addChannel(std::make_unique<SmsChannel>());

    const NotificationMessage message{
        "user-123", "Your order has been shipped"};
    service.sendAll(message);

    service.addChannel(std::make_unique<PushChannel>());
    service.sendAll(message);
}
```

### Expected behavior

Lần gọi đầu gửi qua Email và SMS. Sau change request, `PushChannel` được thêm mà `NotificationService::sendAll` không đổi.

### Ownership và lifetime

- Composition root (`main`) tạo channel và chuyển ownership vào service.
- Service là owner duy nhất; `unique_ptr` thể hiện contract này.
- Khi service bị hủy, vector hủy từng `unique_ptr`; virtual destructor bảo đảm concrete destructors chạy.
- Message được borrow dưới dạng `const&` trong duration của call.

## 4. Bad Design

```cpp
#include <iostream>
#include <stdexcept>
#include <string_view>

enum class ChannelType { email, sms, push };

class NotificationService {
public:
    void send(ChannelType type,
              std::string_view recipient,
              std::string_view body) {
        switch (type) {
        case ChannelType::email:
            std::cout << "[Email] " << recipient << ": " << body << '\n';
            break;
        case ChannelType::sms:
            std::cout << "[SMS] " << recipient << ": " << body << '\n';
            break;
        case ChannelType::push:
            std::cout << "[Push] " << recipient << ": " << body << '\n';
            break;
        default:
            throw std::invalid_argument{"Unknown channel"};
        }
    }
};

int main() {
    NotificationService service;
    service.send(ChannelType::email, "user-123", "Order shipped");
}
```

Design này không luôn sai. Với ba cases nhỏ và ổn định, nó có thể đơn giản hơn polymorphism. Nó trở thành problem khi mỗi channel có SDK, credentials, retry/error mapping riêng và mỗi channel mới buộc sửa/retest một service trung tâm.

Root cause trong expanded scenario là provider responsibilities và selection logic bị trộn vào orchestration.

## 5. Better Design Review

### Encapsulation và Abstraction

`NotificationMessage` bảo vệ invariant. `NotificationChannel` expose capability gửi, không expose provider configuration.

### Composition và Delegation

Service sở hữu channels và delegated operation. Nó không derive từ channel và không biết concrete types.

### Polymorphism

Virtual dispatch chọn concrete `send`. `override` và virtual destructor làm intent/lifecycle rõ.

### SOLID

- **SRP:** message validation, orchestration và provider behavior tách theo reason to change.
- **OCP:** channel mới extend contract mà không sửa send loop.
- **LSP:** mọi channel chấp nhận valid message và thực hiện send contract.
- **ISP:** contract chỉ có capability service cần.
- **DIP:** orchestration phụ thuộc channel abstraction.

### Những abstraction đã từ chối

- Không tạo `NotificationChannelFactory`: composition root đã tạo ba objects rõ ràng.
- Không tạo repository: không có persistence requirement.
- Không tạo retry strategy: failure policy hiện là fail-fast.
- Không dùng `shared_ptr`: chỉ service sở hữu channels.
- Không tạo event bus: direct call đủ cho single-process synchronous scope.

## 6. Trade-offs và Alternatives

| Design | Benefit | Cost | Suitable context |
|---|---|---|---|
| `switch` trong service | Ít types, flow nhìn một chỗ | Sửa trung tâm khi variants tăng | Cases nhỏ, stable |
| Virtual channel objects | Runtime extension, provider details tách | Heap, indirection, lifetime design | Heterogeneous runtime channels |
| Function callbacks | Ít hierarchy, test fake dễ | Contract/state có thể kém discoverable | Behavior nhỏ, stateless |
| Async event/queue | Failure isolation, retry/scaling | Operational và consistency complexity | Production cross-process delivery |

### Failure policy trade-off

Current fail-fast semantics dễ hiểu nhưng một SMS failure ngăn các channels sau. Alternatives:

- best-effort và thu thập errors;
- retry per channel;
- enqueue từng delivery độc lập.

Mỗi alternative thay đổi observable contract và cần requirement riêng; không tự thêm vào core example.

## 7. Common Mistakes

- **Channel giữ raw pointer tới service:** tạo back-reference không cần thiết và lifetime coupling.
- **Service sở hữu channel bằng `shared_ptr`:** shared ownership không có trong requirement.
- **Base interface không có virtual destructor:** polymorphic ownership trở nên không an toàn.
- **Tạo một interface cho từng provider SDK method:** leak detail thay vì abstraction theo client need.
- **Cho rằng thêm Push chứng minh mọi `switch` đều xấu:** complexity threshold phụ thuộc change frequency và provider behavior.

## 8. Misconceptions

- **“OCP nghĩa thêm channel không được sửa bất kỳ file nào.”** Cần tạo class mới và cập nhật composition root/configuration; stable orchestration mới là phần được giữ.
- **“DIP làm code không phụ thuộc gì.”** Code vẫn phụ thuộc `NotificationChannel` contract, nhưng không phụ thuộc concrete provider detail.
- **“Unique pointer chỉ là memory utility.”** Nó truyền đạt exclusive ownership trong API.
- **“Production system chỉ cần thêm retry vào class này.”** Network delivery cần persistence, idempotency, observability và async boundary; đó là scope khác.

## 9. Quick Review Questions

1. Vì sao service sở hữu channels nhưng chỉ borrow message?
2. Change request Push kiểm tra design property nào?
3. Khi nào `switch` solution vẫn tốt hơn hierarchy?
4. Virtual destructor tham gia destruction chain thế nào?
5. Abstraction nào đã bị từ chối và vì sao?

### Answer Key

1. Channels là configured parts sống cùng service; message chỉ cần trong một send call.
2. Variation point và khả năng extend provider behavior mà không sửa stable orchestration.
3. Khi cases ít, stable và behavior đơn giản/centralized dễ đọc hơn.
4. Delete qua base pointer gọi derived destructor rồi base destructor.
5. Factory/repository/retry/event bus/shared ownership vì requirements hiện tại không tạo value tương ứng.

## 10. Mini Exercise

### Problem

Thêm `RecordingChannel` phục vụ test. Channel lưu các messages đã nhận và cung cấp read-only count. Sau đó kiểm tra service gửi đúng một message cho channel.

### Acceptance Criteria

- `RecordingChannel` giữ contract `NotificationChannel`.
- Không sửa `NotificationService`.
- Test không phụ thuộc console output.
- Ownership của recording channel vẫn rõ; nếu test cần đọc state sau khi chuyển `unique_ptr`, giải thích cách giữ non-owning pointer an toàn trong scope test hoặc thay composition design.
- Nêu trade-off giữa test spy object và mock framework.

## 11. Final Review Checklist

Khi review một OOP design khác, hỏi theo thứ tự:

1. Requirement và scope là gì?
2. State, behavior, identity và invariants nằm ở đâu?
3. Public API có expose representation không?
4. Responsibilities thay đổi vì lý do nào?
5. Relationships, ownership và lifetime có rõ không?
6. Inheritance có giữ substitutability không?
7. Runtime polymorphism có variation thực tế không?
8. Error behavior và destruction path có an toàn không?
9. SOLID đang giải quyết pressure nào?
10. Có solution ít classes/indirection hơn không?

## 12. One-Minute Summary

Một OOP design tốt không được đánh giá bằng số class hoặc patterns. Nó được đánh giá bằng valid state, cohesive responsibilities, explicit relationships, safe lifetime, correct contracts và cost of change phù hợp với requirements.

Hoàn thành case study và giải thích được các trade-offs ở trên là đủ để kết thúc OOP Quick Review. Design Patterns, OOAD và Architecture là các track tiếp theo, không phải phần còn thiếu của core OOP.

## Module Checklist

- [x] Đọc trong thời lượng mục tiêu.
- [x] Không vượt depth limit của core OOP.
- [x] Có complete example và bad/better design.
- [x] Có trade-off và when-not-to-use.
- [x] Có ownership/lifetime analysis.
- [x] Có 3–5 review questions cùng answer key.
- [x] Mini exercise có acceptance criteria.
- [x] Code compile được.
- [x] Links hợp lệ.
