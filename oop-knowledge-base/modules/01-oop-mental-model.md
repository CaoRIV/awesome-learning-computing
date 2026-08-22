# 1. OOP Mental Model

> **Thời lượng:** 30–45 phút  
> **Prerequisites:** Biết function, struct/class và container C++ cơ bản  
> **Status:** Reviewed

## Overview

Lập trình hướng đối tượng (**Object-Oriented Programming — OOP**) là cách tổ chức chương trình quanh những object có state, behavior, responsibility và quan hệ cộng tác. Giá trị của OOP không nằm ở việc viết nhiều class, mà ở khả năng đặt state và các rule chi phối state vào một boundary dễ hiểu.

OOP chỉ là một trong nhiều programming paradigms. C++ là multi-paradigm language: cùng một chương trình có thể dùng object cho domain state, free function cho calculation thuần và generic programming cho algorithm dùng lại.

## Learning Outcomes

Sau module này, người học có thể:

- giải thích OOP bằng responsibility và boundary thay vì lặp định nghĩa “lập trình bằng object”;
- phân biệt procedural organization với object-oriented organization;
- nhận diện state, behavior và invariant của một object;
- quyết định khi nào class không mang lại giá trị so với function hoặc simple data type.

## 1. Mental Model

Một object hữu ích thường trả lời bốn câu hỏi:

1. **State nào thuộc về nó?**
2. **Behavior nào có quyền thay đổi hoặc quan sát state đó?**
3. **Rule nào phải luôn đúng?**
4. **Nó cộng tác với object nào để hoàn thành responsibility?**

Có thể hình dung class như một boundary bảo vệ một tập quy tắc:

```text
Client → Public Operations → Invariants → Internal Representation
```

Nếu class chỉ chứa public fields và getters/setters không có rule, nó có thể chỉ là data record. Data record không sai; vấn đề là giả vờ có encapsulation trong khi mọi client vẫn phải tự giữ object hợp lệ.

## 2. Core Concepts

### Procedural Programming

Procedural Programming tổ chức logic quanh procedures/functions. Data thường được truyền vào function, và flow của chương trình là trọng tâm. Cách này phù hợp với transformations tuyến tính, algorithms thuần hoặc chương trình nhỏ ít stateful collaboration.

### Object-Oriented Programming

OOP đặt behavior gần state mà behavior chịu trách nhiệm bảo vệ. Client gửi request qua public operations thay vì tùy ý sửa representation.

### Functional Programming

Functional style ưu tiên immutable data và pure functions. OOP và functional programming có thể bổ trợ nhau: object quản lý lifecycle/identity, còn calculation được tách thành pure function.

### Four pillars như một bản đồ

- **Encapsulation:** giữ state và rule trong một boundary có kiểm soát.
- **Abstraction:** chỉ lộ ra model/capability client cần.
- **Inheritance:** biểu diễn subtype relationship trong những trường hợp phù hợp.
- **Polymorphism:** cùng một contract có thể có nhiều implementation/behavior.

Inheritance không phải điều kiện để code được xem là OOP. Một design chỉ dùng encapsulated value objects và composition vẫn là object-oriented design.

## 3. Focused C++ Example

`Thermostat` sở hữu target temperature và chỉ cho phép thay đổi qua operation bảo vệ invariant.

```cpp
#include <iostream>
#include <stdexcept>

class Thermostat {
public:
    explicit Thermostat(double targetCelsius)
        : targetCelsius_{targetCelsius} {
        validate(targetCelsius_);
    }

    void setTarget(double targetCelsius) {
        validate(targetCelsius);
        targetCelsius_ = targetCelsius;
    }

    [[nodiscard]] double target() const noexcept {
        return targetCelsius_;
    }

    [[nodiscard]] bool shouldHeat(double currentCelsius) const noexcept {
        return currentCelsius < targetCelsius_;
    }

private:
    static void validate(double value) {
        if (value < 5.0 || value > 35.0) {
            throw std::out_of_range{"Target must be between 5 and 35 Celsius"};
        }
    }

    double targetCelsius_;
};

int main() {
    Thermostat thermostat{22.0};
    std::cout << std::boolalpha << thermostat.shouldHeat(18.5) << '\n';
}
```

- **State:** `targetCelsius_`.
- **Behavior:** thay đổi target và quyết định có cần sưởi hay không.
- **Invariant:** target luôn nằm trong `[5, 35]`.
- **Identity:** ví dụ này không cần identity đặc biệt; object hoạt động gần với value.
- **Lifetime:** automatic object, không cần dynamic allocation.

## 4. Bad Design

```cpp
#include <iostream>

struct ThermostatData {
    double targetCelsius;
};

bool shouldHeat(const ThermostatData& thermostat, double currentCelsius) {
    return currentCelsius < thermostat.targetCelsius;
}

int main() {
    ThermostatData thermostat{22.0};
    thermostat.targetCelsius = -100.0;
    std::cout << std::boolalpha << shouldHeat(thermostat, 18.5) << '\n';
}
```

Code compile và chạy được nhưng model cho phép state vô nghĩa. Mọi client phải nhớ range rule; khi rule thay đổi, validation bị phân tán. Root cause không phải “dùng struct”, mà là boundary không bảo vệ invariant.

## 5. Better Design

Class `Thermostat` ở focused example là refactoring vừa đủ: constructor và `setTarget` dùng chung validation, còn client chỉ yêu cầu behavior. Không cần base class, interface hoặc factory vì hiện chưa có variation hay external dependency.

Một alternative hợp lệ là immutable value: bỏ `setTarget` và tạo object mới khi target thay đổi. Nó đơn giản hóa reasoning nhưng có thể không khớp domain device có identity và mutable configuration.

## 6. Trade-offs

| Choice | Benefit | Cost | Use when |
|---|---|---|---|
| Free functions + simple data | Ít ceremony, dễ test calculations | Rule có thể phân tán nếu data mutable | Data transformation hoặc algorithm thuần |
| Encapsulated class | Giữ invariant và vocabulary gần state | Tăng API/design work | Stateful domain concept có rule rõ |
| Immutable value object | Dễ reasoning, không có invalid mutation | Mỗi update tạo value mới | Value nhỏ, identity không quan trọng |

## 7. Common Mistakes

- **Biến mọi noun thành class:** tạo nhiều wrapper không có behavior, làm navigation khó hơn.
- **Đưa mọi function vào class:** utility behavior không dùng state trở thành coupling không cần thiết.
- **Dùng inheritance để chứng minh đang viết OOP:** hierarchy xuất hiện trước khi có subtype contract.
- **Đồng nhất object với database row:** persistence representation bắt đầu điều khiển domain model.
- **Tin rằng private fields là đủ:** client vẫn có thể phá rule thông qua setters quá rộng.

## 8. Misconceptions

- **“OOP luôn dễ maintain hơn procedural code.”** Maintainability phụ thuộc responsibility, coupling và change context.
- **“Object bắt buộc có identity.”** Value object có thể được phân biệt hoàn toàn bằng giá trị.
- **“Behavior phải luôn là method.”** Pure calculation dùng free function có thể rõ và reusable hơn.
- **“Four pillars là checklist chất lượng.”** Chúng là vocabulary; code dùng đủ bốn vẫn có thể thiết kế kém.

## 9. Quick Review Questions

1. OOP tổ chức code quanh điều gì ngoài class syntax?
2. Khi nào free function phù hợp hơn member function?
3. Invariant khác validation rời rạc như thế nào?
4. Vì sao inheritance không phải điều kiện của OOP?
5. `Thermostat` example đang bảo vệ điều gì?

### Answer Key

1. Quanh state, behavior, responsibility, invariant và object collaboration.
2. Khi behavior không cần private state hoặc là calculation thuần dùng cho nhiều types.
3. Invariant là điều phải đúng trong toàn bộ observable lifetime; validation rời rạc chỉ kiểm tra ở một điểm và dễ bị bỏ sót.
4. Encapsulation, abstraction và composition có thể tạo object design hoàn chỉnh mà không cần subtype hierarchy.
5. Range hợp lệ của target temperature và quyền thay đổi state.

## 10. Mini Exercise

### Problem

Thiết kế `SpeedLimit` biểu diễn giới hạn từ 10 đến 130 km/h. Object phải trả lời một tốc độ hiện tại có vượt giới hạn hay không.

### Acceptance Criteria

- Không thể tạo `SpeedLimit` ngoài range.
- Client không sửa trực tiếp representation.
- Có operation `isExceededBy` hoặc tên tương đương.
- Không dùng inheritance hoặc dynamic allocation.
- Viết hai câu giải thích vì sao class mang lại giá trị hơn một biến `int` rời rạc trong context này.

## 11. One-Minute Summary

OOP là cách đặt state và các rule của state vào responsibility boundary. Class chỉ đáng có khi nó tạo vocabulary, bảo vệ invariant hoặc tổ chức collaboration. Hãy chọn object, function hoặc immutable value theo problem; đừng chọn theo khẩu hiệu.

Module tiếp theo đi sâu vào cách class tạo, duy trì và kết thúc lifetime của object.

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

