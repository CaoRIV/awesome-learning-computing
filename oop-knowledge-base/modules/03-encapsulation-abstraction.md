# 3. Encapsulation và Abstraction

> **Thời lượng:** 45–60 phút  
> **Prerequisites:** [Module 2 — Class, Object và Object Lifecycle](02-class-object-lifecycle.md)  
> **Status:** Reviewed

## Overview

Tính đóng gói (**Encapsulation**) kiểm soát cách state và implementation detail được truy cập để bảo vệ invariant và giới hạn impact của thay đổi. Tính trừu tượng (**Abstraction**) tạo một model đơn giản hơn, chỉ giữ những capability hoặc information client cần.

Hai concept hỗ trợ nhau nhưng không đồng nhất: abstraction nói **client nhìn thấy gì**; encapsulation nói **boundary bảo vệ và che giấu detail như thế nào**.

## Learning Outcomes

Sau module này, người học có thể:

- phân biệt Encapsulation, data hiding và Abstraction;
- thiết kế public API bảo vệ invariant;
- nhận diện getter/setter đang expose representation;
- chọn concrete class hoặc interface theo nhu cầu client và variation thực tế.

## 1. Mental Model

```text
Abstraction:    Client needs → Small meaningful contract
Encapsulation:  Public contract → Protected rules → Hidden representation
```

Ví dụ stack có abstraction “Last In, First Out”. Client cần `push`, `pop`, `top`, `empty`; client không cần biết implementation dùng `std::vector`, linked list hay fixed array.

## 2. Core Concepts

### Encapsulation

Encapsulation gom state và behavior liên quan vào boundary, đồng thời kiểm soát operations có thể làm state thay đổi. `private` là mechanism phổ biến nhưng API design mới quyết định encapsulation có thực sự tốt hay không.

### Data Hiding

Data hiding hạn chế truy cập trực tiếp representation. Nó giúp thay đổi implementation mà ít ảnh hưởng client, nhưng chỉ có giá trị khi public API không tái expose toàn bộ representation.

### Access Modifiers

- `public`: contract dành cho client.
- `private`: implementation detail của class.
- `protected`: detail dành cho derived classes; làm tăng coupling với hierarchy nên dùng thận trọng.

### Abstraction

Abstraction chọn những đặc tính relevant cho một purpose. Một concrete class vẫn có thể là abstraction tốt; không cần interface cho mọi type.

### Interface và Abstract Class

Trong C++, “interface” thường là abstract class chỉ chứa virtual operations và virtual destructor. Dùng nó khi nhiều implementations cần được thay thế qua một contract runtime, hoặc cần test seam/external boundary rõ.

## 3. Focused C++ Example

```cpp
#include <cstddef>
#include <iostream>
#include <stdexcept>
#include <utility>
#include <vector>

class BoundedStack {
public:
    explicit BoundedStack(std::size_t capacity)
        : capacity_{capacity} {
        if (capacity_ == 0) {
            throw std::invalid_argument{"Capacity must be positive"};
        }
        values_.reserve(capacity_);
    }

    void push(int value) {
        if (values_.size() == capacity_) {
            throw std::overflow_error{"Stack is full"};
        }
        values_.push_back(value);
    }

    int pop() {
        if (values_.empty()) {
            throw std::underflow_error{"Stack is empty"};
        }
        const int value = values_.back();
        values_.pop_back();
        return value;
    }

    [[nodiscard]] bool empty() const noexcept {
        return values_.empty();
    }

    [[nodiscard]] std::size_t size() const noexcept {
        return values_.size();
    }

private:
    std::size_t capacity_;
    std::vector<int> values_;
};

int main() {
    BoundedStack stack{2};
    stack.push(10);
    stack.push(20);
    std::cout << stack.pop() << '\n';
}
```

- **Abstraction:** bounded LIFO collection.
- **Encapsulation:** chỉ `push/pop` thay đổi internal vector.
- **Invariant:** `size <= capacity` và capacity dương.
- **Implementation hiding:** client không phụ thuộc `std::vector`.
- **Ownership:** stack sở hữu values bằng value member; không có dynamic ownership cần biểu diễn.

## 4. Bad Design

```cpp
#include <cstddef>
#include <vector>

struct BoundedStack {
    std::size_t capacity;
    std::vector<int> values;
};

int main() {
    BoundedStack stack{2, {}};
    stack.values.push_back(10);
    stack.values.push_back(20);
    stack.values.push_back(30);
    stack.capacity = 1;
}
```

Client phá cả `size <= capacity` và consistency giữa capacity với contents. Nếu sau này đổi vector thành fixed array, mọi client truy cập `values` đều phải sửa. Root cause là representation nằm trong public contract.

## 5. Better Design

Focused example chỉ expose operations mang nghĩa của stack. Nó không cung cấp `setValues` hoặc mutable reference tới vector. `size()` là query an toàn vì trả value, không cho client mutation access.

Nếu không cần capacity rule, `std::vector<int>` có thể đã đủ; wrapper class khi đó chỉ thêm ceremony. Abstraction mới nên tồn tại vì có vocabulary/rules riêng, không chỉ để đổi tên container.

## 6. Trade-offs

| Choice | Benefit | Cost | Use when |
|---|---|---|---|
| Public data record | Đơn giản, aggregate initialization thuận tiện | Không bảo vệ rule | Passive data không có invariant phức tạp |
| Encapsulated concrete class | API rõ, thay representation dễ | Cần thiết kế operations | Có state transition và invariant |
| Abstract interface | Runtime substitution, giảm dependency vào detail | Indirection, lifetime và testing complexity | Có nhiều implementations hoặc external boundary thật |
| Template/concept | Compile-time polymorphism, không virtual call | Implementation lộ trong headers, code generation tăng | Performance/generic reuse quan trọng |

## 7. Common Mistakes

- **Getter/setter cho mọi field:** public API phản chiếu storage thay vì domain operations.
- **Trả mutable reference tới private container:** client có quyền phá invariant như data public.
- **Dùng `protected` data để “hỗ trợ inheritance”:** derived class bị coupling vào representation.
- **Tạo interface cho class không có variation:** tăng indirection mà không giảm change cost.
- **Che giấu error condition:** `pop()` trên empty stack cần contract rõ, không trả magic value.

## 8. Misconceptions

- **“Encapsulation nghĩa là fields private.”** Private access chỉ là một phần; public API vẫn có thể expose representation.
- **“Abstraction nghĩa là abstract class.”** Abstract class là một mechanism; value type hoặc free function cũng có thể tạo abstraction.
- **“Getter luôn an toàn.”** Getter trả mutable reference/pointer có thể phá encapsulation; getter scalar vẫn có thể expose detail không cần thiết.
- **“Nhiều interfaces luôn giảm coupling.”** Client vẫn coupling vào contract; interface kém thiết kế chỉ di chuyển coupling.

## 9. Quick Review Questions

1. Abstraction và Encapsulation khác nhau ở câu hỏi trung tâm nào?
2. Vì sao private vector nhưng trả `std::vector<int>& values()` vẫn yếu?
3. Khi nào data record với public fields là hợp lý?
4. Khi nào concrete class tốt hơn abstract interface?
5. `BoundedStack` bảo vệ invariant nào?

### Answer Key

1. Abstraction chọn client thấy capability gì; Encapsulation kiểm soát detail/state bên trong boundary.
2. Client vẫn có mutable access và có thể phá mọi rule của class.
3. Khi data thực sự passive, không có invariant/state transitions cần bảo vệ.
4. Khi chỉ có một implementation và không có runtime substitution/external seam có giá trị.
5. Capacity dương và số phần tử không vượt capacity.

## 10. Mini Exercise

### Problem

Thiết kế `Percentage` biểu diễn giá trị từ 0 đến 100 và hỗ trợ `applyTo(double amount)`.

### Acceptance Criteria

- Không thể tạo percentage ngoài range.
- Không có public setter hoặc mutable reference.
- `applyTo` không thay đổi object.
- Representation có thể đổi từ `double` sang basis points mà client không đổi.
- Giải thích abstraction mà client đang sử dụng.

## 11. One-Minute Summary

Abstraction định nghĩa model/capability client cần; Encapsulation giữ invariant và implementation detail sau một API có kiểm soát. `private` không cứu được API phản chiếu toàn bộ storage, và abstract interface chỉ đáng dùng khi runtime substitution hoặc boundary thực sự tồn tại.

Module tiếp theo dùng các boundaries này để mô hình hóa relationship, ownership và delegation giữa objects.

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

