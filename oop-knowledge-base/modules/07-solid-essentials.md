# 7. SOLID Essentials

> **Thời lượng:** 60–75 phút  
> **Prerequisites:** [Module 3 — Encapsulation và Abstraction](03-encapsulation-abstraction.md), [Module 4 — Object Relationships và Composition](04-object-relationships-composition.md), [Module 5 — Inheritance và Polymorphism](05-inheritance-polymorphism.md)  
> **Status:** Reviewed

## Overview

SOLID là năm design principles giúp phân tích responsibilities, contracts, variation và dependency direction. Chúng không phải luật cú pháp và không tạo điểm chất lượng theo số lượng interfaces. Một refactoring chỉ có giá trị khi nó giải quyết change pressure, correctness risk hoặc testability problem cụ thể.

Module này dùng một checkout scenario để kết nối năm principles và chỉ ra khi direct dependency đơn giản hơn abstraction.

## Learning Outcomes

Sau module này, người học có thể:

- giải thích từng principle bằng problem nó giải quyết;
- phân biệt SRP với “một method”, DIP với Dependency Injection;
- dùng LSP để review subtype contract;
- nhận diện SOLID overuse và speculative abstraction;
- refactor dependency boundary theo một change scenario thực tế.

## 1. Mental Model

SOLID tập trung vào **cost of change**:

```text
SRP → Những thay đổi vì lý do khác nhau có bị trộn không?
OCP → Variation đã biết có thể mở rộng mà không sửa stable policy không?
LSP → Subtype có giữ contract để substitution an toàn không?
ISP → Client có bị ép phụ thuộc operations không dùng không?
DIP → Policy có phụ thuộc trực tiếp volatile detail không?
```

Trước khi áp principle, luôn nêu change scenario. Không có scenario, abstraction dễ trở thành dự đoán.

## 2. Core Concepts

### S — Single Responsibility Principle

Một module/class nên có một reason to change theo một actor hoặc business concern. SRP nói về cohesion của responsibility, không nói về method count.

**Signal:** cùng một class thay đổi khi pricing rule, database schema và email provider thay đổi.

### O — Open/Closed Principle

Software entity nên mở cho extension nhưng đóng tương đối với modification tại **variation point được chọn**. Không có code nào đóng với mọi thay đổi.

**Signal:** mỗi payment provider mới buộc sửa một `switch` trung tâm đã ổn định.

### L — Liskov Substitution Principle

Subtype phải giữ behavioral contract của base: không siết precondition, không làm yếu postcondition, không phá invariant hoặc tạo exception behavior bất ngờ.

**Signal:** derived type override operation nhưng ném “not supported” cho behavior base đã hứa.

### I — Interface Segregation Principle

Client không nên phụ thuộc operations nó không dùng. Interface nên cohesive theo role/capability của client.

**Signal:** implementation phải để trống method hoặc ném exception vì interface quá rộng.

### D — Dependency Inversion Principle

High-level policy không nên phụ thuộc trực tiếp low-level volatile detail; cả hai phụ thuộc abstraction phù hợp với policy/client.

**Signal:** use-case code tự gọi SDK vendor, mở database và gửi SMTP nên khó test/thay provider.

### Dependency Injection không phải DIP

Dependency Injection (**DI**) là cách cung cấp dependency từ bên ngoài, ví dụ constructor injection. DI có thể hỗ trợ DIP nhưng inject concrete volatile detail vẫn chưa tạo useful abstraction; ngược lại DIP có thể được thực hiện bằng factory/template mà không cần DI container.

## 3. Focused C++ Example

Requirements cho phép nhiều pricing policies và payment gateways. Đây là variation có thật, nên hai abstractions có lý do tồn tại.

```cpp
#include <cstdint>
#include <iostream>
#include <stdexcept>
#include <string>
#include <string_view>
#include <utility>

struct Order {
    std::string id;
    std::int64_t subtotalCents;
};

class PricingPolicy {
public:
    virtual ~PricingPolicy() = default;
    [[nodiscard]] virtual std::int64_t finalPriceCents(
        std::int64_t subtotalCents) const = 0;
};

class RegularPricing final : public PricingPolicy {
public:
    [[nodiscard]] std::int64_t finalPriceCents(
        std::int64_t subtotalCents) const override {
        if (subtotalCents < 0) {
            throw std::invalid_argument{"Subtotal cannot be negative"};
        }
        return subtotalCents;
    }
};

class PercentageDiscount final : public PricingPolicy {
public:
    explicit PercentageDiscount(int percent)
        : percent_{percent} {
        if (percent_ < 0 || percent_ > 100) {
            throw std::invalid_argument{"Discount must be from 0 to 100"};
        }
    }

    [[nodiscard]] std::int64_t finalPriceCents(
        std::int64_t subtotalCents) const override {
        if (subtotalCents < 0) {
            throw std::invalid_argument{"Subtotal cannot be negative"};
        }
        return subtotalCents * (100 - percent_) / 100;
    }

private:
    int percent_;
};

class PaymentGateway {
public:
    virtual ~PaymentGateway() = default;
    virtual void charge(std::string_view orderId,
                        std::int64_t amountCents) = 0;
};

class ConsolePaymentGateway final : public PaymentGateway {
public:
    void charge(std::string_view orderId,
                std::int64_t amountCents) override {
        std::cout << "Charged " << amountCents
                  << " cents for " << orderId << '\n';
    }
};

class CheckoutService {
public:
    CheckoutService(const PricingPolicy& pricing,
                    PaymentGateway& payment)
        : pricing_{pricing}, payment_{payment} {}

    [[nodiscard]] std::int64_t checkout(const Order& order) {
        if (order.id.empty()) {
            throw std::invalid_argument{"Order id is required"};
        }
        const auto finalPrice = pricing_.finalPriceCents(order.subtotalCents);
        payment_.charge(order.id, finalPrice);
        return finalPrice;
    }

private:
    const PricingPolicy& pricing_;
    PaymentGateway& payment_;
};

int main() {
    const PercentageDiscount pricing{10};
    ConsolePaymentGateway payment;
    CheckoutService checkout{pricing, payment};
    std::cout << "Final: " << checkout.checkout({"ORD-1", 100'000}) << '\n';
}
```

### Principle mapping

- **SRP:** pricing tính giá; gateway charge; checkout điều phối use case.
- **OCP:** thêm pricing policy/gateway mới không sửa flow `CheckoutService`.
- **LSP:** mọi policy giữ contract nhận subtotal không âm và trả price hợp lệ.
- **ISP:** pricing và payment là hai role interfaces riêng; implementation không bị ép cung cấp capability khác.
- **DIP:** checkout policy phụ thuộc abstractions của capability nó cần, không phụ thuộc SDK cụ thể.

Dependencies được borrow bằng references; caller chịu trách nhiệm lifetime.

## 4. Bad Design

```cpp
#include <cstdint>
#include <iostream>
#include <stdexcept>
#include <string>

class CheckoutService {
public:
    std::int64_t checkout(const std::string& orderId,
                          std::int64_t subtotalCents,
                          const std::string& customerType,
                          const std::string& paymentProvider) {
        std::int64_t total = subtotalCents;
        if (customerType == "vip") {
            total = subtotalCents * 90 / 100;
        }

        if (paymentProvider == "console") {
            std::cout << "Charge " << total << " for " << orderId << '\n';
        } else {
            throw std::runtime_error{"Unsupported payment provider"};
        }

        return total;
    }
};

int main() {
    CheckoutService service;
    service.checkout("ORD-1", 100'000, "vip", "console");
}
```

Một class biết pricing rules và provider selection. Mỗi variant thêm branch; strings không tạo contract; test phải đi qua concrete output. Root cause là independent variations bị trộn vào stable orchestration.

Không phải mọi `if` đều vi phạm OCP. Với hai cases ổn định và không có external provider, direct branch có thể là solution tốt hơn hierarchy.

## 5. Better Design

Focused example extract đúng hai variation có requirement. Nó **không** tạo `OrderValidator`, `CheckoutLogger`, `Clock`, repository hoặc event bus vì scenario chưa cần.

Một refactoring an toàn nên đi theo thứ tự:

1. Viết tests cho current behavior.
2. Tách pure pricing calculation.
3. Đặt external payment call sau contract nhỏ.
4. Inject dependencies từ composition root.
5. Chỉ thêm implementation khi requirement xuất hiện.

## 6. Trade-offs

| Choice | Benefit | Cost | Use when |
|---|---|---|---|
| Direct concrete code | Dễ đọc, ít indirection | Khó thay nếu dependency volatile | Một implementation ổn định |
| Small role interface | Test seam, runtime substitution | Lifetime/virtual complexity | External provider hoặc nhiều implementations |
| Strategy/function object | Variation cô lập, có thể compile-time | Generic API hoặc type erasure trade-off | Algorithm/policy nhỏ |
| DI container | Wire graph lớn tự động | Hidden configuration/runtime errors | Application lớn có nhiều object graphs |

## 7. Common Mistakes

- **Tách class theo từng method để “đúng SRP”:** cohesion giảm và flow bị phân mảnh.
- **Interface cho mọi concrete class:** không có client need hoặc variation, chỉ thêm indirection.
- **Dùng OCP để cấm sửa code:** bug fix và requirement mới vẫn cần modification.
- **Kiểm tra LSP bằng compiler:** type compatibility không chứng minh behavioral compatibility.
- **Dùng DI container như service locator:** dependencies lại bị ẩn trong runtime lookup.

## 8. Misconceptions

- **“SRP là một class chỉ làm một việc.”** “Việc” quá mơ hồ; hãy dùng reason to change/actor.
- **“DIP yêu cầu mọi dependency có interface.”** Stable value/concrete type có thể là dependency hoàn toàn hợp lý.
- **“OCP đạt được khi không sửa class cũ.”** Chỉ đóng tương đối với variation đã chọn; abstraction cũng cần evolve.
- **“SOLID càng nhiều càng tốt.”** Overuse tạo class explosion, navigation cost và speculative flexibility.

## 9. Quick Review Questions

1. Vì sao SOLID cần change scenario?
2. SRP khác method-count rule thế nào?
3. LSP kiểm tra điều gì ngoài signature?
4. DIP và constructor injection khác nhau thế nào?
5. Khi nào không nên extract interface?

### Answer Key

1. Principles tối ưu cost of change; không có scenario thì không biết variation/responsibility nào cần tách.
2. SRP nói về cohesive reason to change, một responsibility có thể cần nhiều methods.
3. Preconditions, postconditions, invariants và observable behavior.
4. DIP là dependency direction principle; constructor injection là mechanism cung cấp dependency.
5. Khi concrete dependency ổn định, không có substitution/test seam/external boundary cần thiết.

## 10. Mini Exercise

### Problem

Một `ReportService` đang vừa lấy dữ liệu, format CSV/PDF và ghi file. Refactor theo requirement: format sẽ tăng, data source hiện chỉ có một và ổn định.

### Acceptance Criteria

- Chỉ extract variation point cho formatting.
- Không tạo data-source interface khi chưa có requirement/test seam cần thiết.
- Formatter interfaces không chứa file-writing operations.
- Nêu principle hỗ trợ từng quyết định và một cost của design mới.
- Thêm HTML formatter mà không sửa report orchestration.

## 11. One-Minute Summary

SOLID là bộ câu hỏi về responsibility, variation, contract, client need và dependency direction. Áp dụng theo evidence, không theo số interface. Refactoring tốt giải quyết một pressure cụ thể và vẫn để solution đơn giản nhất có thể.

Module cuối dùng tất cả concepts để thiết kế, thay đổi và review một hệ thống notification nhỏ.

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
