# 4. Object Relationships và Composition

> **Thời lượng:** 45–60 phút  
> **Prerequisites:** [Module 2 — Class, Object và Object Lifecycle](02-class-object-lifecycle.md), [Module 3 — Encapsulation và Abstraction](03-encapsulation-abstraction.md)  
> **Status:** Reviewed

## Overview

Object hiếm khi làm việc một mình. Thiết kế OOP chủ yếu là phân bổ responsibility và làm rõ objects cộng tác, sở hữu hoặc phụ thuộc nhau như thế nào. Các nhãn Association, Dependency, Aggregation và Composition chỉ hữu ích khi chúng làm rõ cardinality, navigability, ownership và lifetime.

Module này đặt Composition và Delegation trước Inheritance để tách hai câu hỏi: “object dùng capability nào?” và “object có phải subtype của type khác không?”.

## Learning Outcomes

Sau module này, người học có thể:

- phân biệt Dependency, Association, Composition và Delegation;
- mô tả relationship kèm ownership và lifetime;
- nhận diện bidirectional association hoặc shared ownership không cần thiết;
- chọn Composition thay vì Inheritance khi mục tiêu là lắp ghép/reuse behavior.

## 1. Mental Model

Với mỗi edge giữa hai objects, hỏi năm câu:

1. Object A chỉ dùng B trong một operation hay giữ B lâu dài?
2. A có sở hữu B không?
3. B có thể sống lâu hơn A không?
4. Relationship bắt buộc hay optional?
5. A chỉ biết B, hay A delegated behavior cho B?

```text
Dependency:   A ----uses briefly----> B
Association:  A ----knows/links-----> B
Composition:  Whole ----owns-------> Part
Delegation:   A ----asks to do------> Collaborator
```

## 2. Core Concepts

### Dependency

A phụ thuộc B khi code của A cần B để compile hoặc thực hiện một operation. Parameter/local variable thường biểu diễn dependency ngắn hạn.

### Association

A giữ liên kết tới B trong một khoảng thời gian. Association không tự nói ai sở hữu ai; nó có thể được biểu diễn bằng ID, reference wrapper hoặc non-owning pointer tùy context.

### Composition

Whole sở hữu part như một phần cấu thành. Lifetime part thường nằm trong lifetime whole. Value member và `std::vector<Part>` là representations rõ nhất khi phù hợp.

### Aggregation

Aggregation thường diễn đạt whole–part nhưng part có thể sống độc lập. Thuật ngữ này dễ bị diễn giải khác nhau; trong code review nên nói trực tiếp ownership/lifetime thay vì chỉ nói “aggregation”.

### Delegation

A nhận request nhưng chuyển phần thực thi cho collaborator B. Delegation là cơ chế cốt lõi của composition-based design.

### Composition over Inheritance

Ưu tiên xem xét composition khi muốn reuse hoặc thay behavior. Dùng inheritance khi có behavioral subtype ổn định và client cần substitution qua base contract.

## 3. Focused C++ Example

`Order` sở hữu các `OrderLine`. `addProduct` chỉ phụ thuộc tạm thời vào `Product` rồi chụp lại dữ liệu cần thiết để order không phụ thuộc lifetime hoặc giá tương lai của catalog product.

```cpp
#include <cstdint>
#include <iostream>
#include <stdexcept>
#include <string>
#include <utility>
#include <vector>

struct Product {
    std::string sku;
    std::int64_t priceCents;
};

class OrderLine {
public:
    OrderLine(std::string sku, std::int64_t unitPriceCents, int quantity)
        : sku_{std::move(sku)},
          unitPriceCents_{unitPriceCents},
          quantity_{quantity} {
        if (sku_.empty() || unitPriceCents_ < 0 || quantity_ <= 0) {
            throw std::invalid_argument{"Invalid order line"};
        }
    }

    [[nodiscard]] std::int64_t subtotalCents() const noexcept {
        return unitPriceCents_ * quantity_;
    }

private:
    std::string sku_;
    std::int64_t unitPriceCents_;
    int quantity_;
};

class Order {
public:
    void addProduct(const Product& product, int quantity) {
        lines_.emplace_back(product.sku, product.priceCents, quantity);
    }

    [[nodiscard]] std::int64_t totalCents() const noexcept {
        std::int64_t total = 0;
        for (const OrderLine& line : lines_) {
            total += line.subtotalCents();
        }
        return total;
    }

private:
    std::vector<OrderLine> lines_;
};

int main() {
    const Product keyboard{"KB-01", 500'000};
    Order order;
    order.addProduct(keyboard, 2);
    std::cout << order.totalCents() << " cents\n";
}
```

- `Order` **composes** `OrderLine` values và kiểm soát lifetime của chúng.
- `addProduct` có **Dependency** vào `Product`, không giữ association.
- `Order` delegated subtotal calculation cho `OrderLine` thay vì đọc fields của line.
- Snapshot price là domain decision: order total không đổi khi catalog price đổi.

## 4. Bad Design

```cpp
#include <cstdint>
#include <iostream>
#include <string>
#include <vector>

struct Product {
    std::string sku;
    std::int64_t priceCents;
};

class Order {
public:
    void addProduct(const Product& product) {
        products_.push_back(&product);
    }

    [[nodiscard]] std::int64_t totalCents() const {
        std::int64_t total = 0;
        for (const Product* product : products_) {
            total += product->priceCents;
        }
        return total;
    }

private:
    std::vector<const Product*> products_;
};

int main() {
    Product keyboard{"KB-01", 500'000};
    Order order;
    order.addProduct(keyboard);
    keyboard.priceCents = 700'000;
    std::cout << order.totalCents() << '\n';
}
```

Order giữ non-owning pointers nhưng API không nói lifetime requirement. Total thay đổi theo catalog price và pointer có thể dangling nếu product bị hủy. Root cause là association được chọn mà không xác định domain semantics và ownership.

## 5. Better Design

Focused example dùng composition với snapshot. Đây không phải universal answer:

- Nếu order phải theo live price cho đến checkout, giữ product ID và hỏi pricing service tại thời điểm tính có thể đúng hơn.
- Nếu Product là immutable shared catalog entity có lifetime được đảm bảo bởi repository, association có thể hợp lệ.

Điểm cải thiện là relationship thể hiện đúng rule, không phải việc loại bỏ pointer bằng mọi giá.

## 6. Trade-offs

| Choice | Benefit | Cost | Use when |
|---|---|---|---|
| Value composition | Ownership/lifetime rõ, locality tốt | Copy state, whole chịu kích thước part | Part thuộc whole và nhỏ/movable |
| Non-owning association | Không copy entity, phản ánh liên kết domain | Cần lifetime guarantee, có dangling risk | Related object sống lâu hơn và owner rõ |
| ID-based association | Tách process/lifetime, serialize dễ | Cần lookup, lỗi not-found | Persistence/distributed boundary |
| Shared ownership | Object sống tới khi owner cuối cùng kết thúc | Ownership khó đọc, cycle/atomic overhead | Lifetime thực sự được chia sẻ |

## 7. Common Mistakes

- **Gọi mọi HAS-A là Composition:** không kiểm tra owner và lifetime.
- **Dùng `shared_ptr` để né câu hỏi ownership:** biến design uncertainty thành runtime reference counting.
- **Giữ bidirectional pointers mặc định:** hai phía phải đồng bộ và dễ dangling.
- **Composition nhưng expose mutable child:** whole không còn bảo vệ invariant tổng thể.
- **Chọn inheritance chỉ để gọi lại code:** tạo subtype contract giả và hierarchy cứng.

## 8. Misconceptions

- **“Composition luôn dùng `unique_ptr`.”** Value member thường là composition rõ hơn; pointer chỉ cần khi optionality, polymorphism hoặc lifetime yêu cầu.
- **“Association nghĩa là raw pointer.”** Association là model relation, representation có thể là ID/reference/pointer.
- **“Aggregation và Composition phân biệt hoàn toàn bằng UML diamond.”** Domain lifetime/ownership mới là phần cần giải thích.
- **“Composition over Inheritance cấm inheritance.”** Đây là preference cho reuse; valid subtype vẫn phù hợp với inheritance.

## 9. Quick Review Questions

1. Dependency khác Association ở duration/context nào?
2. Composition cần làm rõ điều gì ngoài HAS-A?
3. Vì sao `shared_ptr` không nên là default relationship representation?
4. Delegation đóng vai trò gì trong composition-based design?
5. Tại sao `OrderLine` lưu price snapshot trong example?

### Answer Key

1. Dependency thường dùng tạm trong operation; Association được giữ như state/link lâu hơn.
2. Ownership, lifetime, optionality và cardinality.
3. Nó che mờ owner, thêm overhead và có thể tạo cycle.
4. Whole chuyển responsibility thực thi cho composed collaborator.
5. Để order total ổn định, không phụ thuộc thay đổi catalog và lifetime Product.

## 10. Mini Exercise

### Problem

Mô hình hóa `Playlist`, `PlaylistEntry` và `Song`. Một song tồn tại độc lập và có thể nằm trong nhiều playlists; playlist entry thuộc đúng một playlist.

### Acceptance Criteria

- Ghi rõ relationship giữa từng cặp types.
- `Playlist` sở hữu entries bằng value composition.
- Quyết định entry giữ `SongId`, non-owning reference hay snapshot; nêu lifetime/trade-off.
- Không dùng bidirectional pointer nếu không có requirement điều hướng ngược.
- Thêm operation `totalDuration()` bằng delegation hợp lý.

## 11. One-Minute Summary

Relationship design không dừng ở IS-A/HAS-A. Hãy mô tả duration, cardinality, optionality, ownership, lifetime và delegation. Composition phù hợp để lắp ghép/reuse behavior; inheritance chỉ phù hợp khi relationship thực sự là subtype.

Module tiếp theo kiểm tra subtype bằng substitutability và giải thích runtime polymorphism.

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

