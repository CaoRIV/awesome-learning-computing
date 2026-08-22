# 5. Inheritance, Polymorphism và Dynamic Dispatch

> **Thời lượng:** 60–75 phút  
> **Prerequisites:** [Module 3 — Encapsulation và Abstraction](03-encapsulation-abstraction.md), [Module 4 — Object Relationships và Composition](04-object-relationships-composition.md)  
> **Status:** Reviewed

## Overview

Tính kế thừa (**Inheritance**) cho phép derived class nhận base interface/implementation. Tính đa hình (**Polymorphism**) cho phép client dùng một contract trong khi behavior cụ thể phụ thuộc object thực tế. Trong C++, virtual functions cung cấp runtime subtype polymorphism qua dynamic dispatch.

Mục tiêu của module không phải xây hierarchy sâu. Mục tiêu là nhận biết khi public inheritance có nghĩa, dự đoán dispatch và tránh object slicing/destruction bug.

## Learning Outcomes

Sau module này, người học có thể:

- phân biệt overloading, overriding và runtime polymorphism;
- giải thích static type, dynamic type và virtual dispatch;
- thiết kế polymorphic base có virtual destructor phù hợp;
- phát hiện object slicing và hierarchy vi phạm substitutability;
- chọn composition khi inheritance chỉ phục vụ reuse.

## 1. Mental Model

```text
Base reference/pointer ──virtual call──> Implementation của dynamic object
```

Biến/reference có **static type** được compiler biết. Object được tham chiếu có **dynamic type** tại runtime. Với virtual call, implementation được chọn theo dynamic type.

Public inheritance nên đọc là:

> Mọi `Derived` đều có thể được dùng ở nơi contract yêu cầu `Base`.

Đây là behavioral claim, không chỉ là quan hệ từ vựng “X là một Y”.

## 2. Core Concepts

### Inheritance và Subtyping

Inheritance là language mechanism; subtyping là relationship cho phép substitution. Public inheritance trong C++ thường biểu diễn subtype, nhưng hierarchy chỉ đúng khi behavior giữ contract.

### Overloading

Nhiều functions cùng tên nhưng khác parameter lists; compiler chọn ở compile time.

### Overriding

Derived class cung cấp implementation mới cho virtual operation có cùng signature. Dùng `override` để compiler kiểm tra intent.

### Abstract Class

Class có ít nhất một pure virtual function không thể instantiate trực tiếp. Một interface-style base thường không giữ mutable state và có virtual destructor.

### Dynamic Dispatch

Virtual call qua base reference/pointer chọn override của dynamic object. vtable/vptr là implementation model phổ biến, không phải representation C++ Standard bắt buộc.

### Object Slicing

Copy derived object vào base object by value chỉ giữ base subobject. Dynamic behavior/state của derived bị mất.

## 3. Focused C++ Example

```cpp
#include <iostream>
#include <string>
#include <string_view>

class NotificationChannel {
public:
    virtual ~NotificationChannel() = default;
    virtual void send(std::string_view recipient,
                      std::string_view message) const = 0;
};

class EmailChannel final : public NotificationChannel {
public:
    void send(std::string_view recipient,
              std::string_view message) const override {
        std::cout << "Email to " << recipient << ": " << message << '\n';
    }
};

class SmsChannel final : public NotificationChannel {
public:
    void send(std::string_view recipient,
              std::string_view message) const override {
        std::cout << "SMS to " << recipient << ": " << message << '\n';
    }
};

class NotificationService {
public:
    explicit NotificationService(const NotificationChannel& channel)
        : channel_{channel} {}

    void notify(std::string_view recipient,
                std::string_view message) const {
        channel_.send(recipient, message);
    }

private:
    const NotificationChannel& channel_;
};

int main() {
    EmailChannel email;
    NotificationService service{email};
    service.notify("student@example.com", "Review completed");
}
```

- `NotificationChannel` là runtime contract.
- `service` chỉ biết base interface; call được dispatch tới `EmailChannel`.
- Base destructor virtual để destruction qua base pointer an toàn nếu ownership model sau này cần.
- Service **mượn** channel; caller phải đảm bảo channel sống lâu hơn service.
- `final` nói các concrete channels không được thiết kế để tiếp tục derive.

## 4. Bad Design

Slicing xảy ra vì `deliver` nhận base object by value và `send` không virtual.

```cpp
#include <iostream>

class Channel {
public:
    void send() const { std::cout << "Generic channel\n"; }
};

class EmailChannel : public Channel {
public:
    void send() const { std::cout << "Email channel\n"; }
};

void deliver(Channel channel) {
    channel.send();
}

int main() {
    EmailChannel email;
    deliver(email);
}
```

Output là `Generic channel`. Root causes:

- API copy derived thành base object;
- base operation không virtual;
- hierarchy chưa định nghĩa polymorphic contract.

Chỉ thêm `virtual` nhưng vẫn pass by value không giải quyết slicing. Polymorphic APIs phải dùng reference/pointer với lifetime rõ.

## 5. Better Design

Focused example nhận `const NotificationChannel&`, dùng virtual operation và `override`. Tuy nhiên interface không phải lựa chọn duy nhất:

- Nếu channel được biết tại compile time, template hoặc function object có thể đơn giản/nhanh hơn.
- Nếu chỉ có một implementation, concrete dependency có thể đủ.
- Nếu behavior nhỏ, `std::function` có thể tránh hierarchy nhưng có type-erasure cost.

Runtime polymorphism đáng dùng khi implementation cần được chọn/thay ở runtime hoặc client phải tách khỏi external implementations.

## 6. Trade-offs

| Choice | Benefit | Cost | Use when |
|---|---|---|---|
| Virtual interface | Runtime substitution, stable client contract | Indirection, lifetime management, harder value semantics | Plugin/external boundary hoặc runtime choice |
| Template/static polymorphism | Inline/optimization, value-friendly | Code in headers, compile time và code size | Types biết ở compile time |
| Composition với concrete type | Đơn giản, explicit | Không runtime substitution | Một implementation ổn định |
| `std::variant` | Closed set, exhaustive handling | Thêm type mới phải sửa visitor | Variant set nhỏ và known |

## 7. Common Mistakes

- **Base destructor không virtual:** delete derived qua base pointer gây undefined behavior.
- **Quên `override`:** signature lệch tạo function mới thay vì override.
- **Pass polymorphic object by value:** gây slicing.
- **Gọi virtual operation trong constructor/destructor:** dispatch không tới derived part chưa/không còn sống.
- **Hierarchy để reuse vài dòng code:** contract yếu nhưng coupling giữa base/derived mạnh.

## 8. Misconceptions

- **“Overloading là compile-time polymorphism giống overriding.”** Cả hai được gọi polymorphism trong taxonomy rộng, nhưng selection mechanism khác hoàn toàn.
- **“Virtual call luôn chậm đáng kể.”** Có indirection và có thể cản inline, nhưng impact phải đo trong context.
- **“Abstract class không được có implementation.”** Nó có thể có data/concrete methods; interface-style base chỉ là convention.
- **“IS-A theo ngôn ngữ tự nhiên là đủ.”** Subtype cần giữ preconditions, postconditions và invariants của contract.

## 9. Quick Review Questions

1. Static type và dynamic type khác nhau thế nào?
2. Overloading và overriding được chọn vào thời điểm nào?
3. Vì sao polymorphic base thường cần virtual destructor?
4. Object slicing xảy ra khi nào?
5. Khi nào composition/concrete dependency tốt hơn virtual interface?

### Answer Key

1. Static type do compiler biết; dynamic type là type của object thực tế tại runtime.
2. Overloading được resolve compile time; overriding được dispatch runtime qua virtual call.
3. Để derived destructor chạy khi object bị hủy qua base pointer.
4. Khi derived object được copy/pass vào base object by value.
5. Khi không cần runtime substitution hoặc reuse behavior không tạo valid subtype.

## 10. Mini Exercise

### Problem

Thiết kế contract `TaxPolicy` có operation tính tax từ subtotal. Tạo `NoTaxPolicy` và `FixedRateTaxPolicy`, sau đó inject một policy vào `InvoiceCalculator`.

### Acceptance Criteria

- Base có virtual destructor và pure virtual operation `const`.
- Overrides dùng `override`.
- `InvoiceCalculator` không sở hữu policy; lifetime requirement được ghi rõ.
- Không pass policy by value.
- Nêu một alternative không dùng runtime polymorphism.

## 11. One-Minute Summary

Inheritance là mechanism; substitutability mới quyết định hierarchy có đúng không. Runtime polymorphism dùng virtual call qua reference/pointer, cần contract và lifetime rõ. Tránh slicing, thêm virtual destructor cho polymorphic deletion và ưu tiên composition khi không có true subtype.

Module tiếp theo làm rõ ownership/lifetime để các polymorphic relationships trong C++ an toàn.

## Module Checklist

- [x] Đọc trong thời lượng mục tiêu.
- [x] Không vượt depth limit của core OOP.
- [x] Có focused example và bad/better design.
- [x] Có trade-off và when-not-to-use.
- [x] Có ownership/lifetime analysis khi liên quan.
- [x] Có 3–5 review questions cùng answer key.
- [x] Mini exercise có acceptance criteria.
- [x] Code compile được.
- [x] Links hợp lệ.

