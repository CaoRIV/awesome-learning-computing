# 2. Class, Object và Object Lifecycle

> **Thời lượng:** 45–60 phút  
> **Prerequisites:** [Module 1 — OOP Mental Model](01-oop-mental-model.md)  
> **Status:** Reviewed

## Overview

Class định nghĩa type và operations; object là một instance sống trong một khoảng thời gian cụ thể. Thiết kế class tốt bắt đầu từ câu hỏi object phải hợp lệ như thế nào, không bắt đầu từ danh sách fields cần getter/setter.

Trong C++, constructor, destructor, copy và move ảnh hưởng trực tiếp tới nghĩa của type. Module này chỉ tập trung lifecycle cơ bản; ownership và RAII được ôn ở Module 6.

## Learning Outcomes

Sau module này, người học có thể:

- phân biệt class, object, state, behavior và identity;
- dùng constructor để thiết lập invariant ngay từ đầu;
- giải thích constructor/destructor và member initialization order cơ bản;
- nhận diện value object và entity để quyết định copy semantics hợp lý.

## 1. Mental Model

```text
Storage available → Construction → Valid observable lifetime → Destruction → Storage reusable
```

Constructor không “cấp phát object”; nó bắt đầu lifetime của object trong storage phù hợp và thiết lập valid state. Destructor kết thúc lifetime và giải phóng resource do object sở hữu.

Trong body constructor, data members đã được khởi tạo theo **thứ tự khai báo trong class**, không theo thứ tự xuất hiện trong initializer list.

## 2. Core Concepts

### Class và Object

Class là user-defined type mô tả representation và operations. Object là một vùng storage đang chứa một instance sống của type đó.

### State, Behavior và Identity

- **State:** dữ liệu quan sát được ảnh hưởng behavior.
- **Behavior:** operations object cung cấp.
- **Identity:** yếu tố khiến hai objects vẫn khác nhau dù state hiện tại giống nhau.

### Invariant

Invariant là điều phải đúng sau construction và trước/sau mọi public operation. Trong lúc một private operation đang cập nhật nhiều fields, invariant có thể tạm thời chưa đúng, nhưng trạng thái đó không được lộ cho client.

### Value Object và Entity

- **Value object:** equality dựa trên giá trị; copy thường hợp lý, ví dụ `Money` hoặc `DateRange`.
- **Entity:** có identity xuyên thời gian; copy có thể gây duplicate identity, ví dụ `BankAccount` hoặc `Order`.

Đây là design distinction, không phải keyword của C++.

## 3. Focused C++ Example

```cpp
#include <cstdint>
#include <iostream>
#include <stdexcept>
#include <string>
#include <utility>

class BankAccount {
public:
    BankAccount(std::string accountNumber, std::int64_t openingBalanceCents)
        : accountNumber_{std::move(accountNumber)},
          balanceCents_{openingBalanceCents} {
        if (accountNumber_.empty()) {
            throw std::invalid_argument{"Account number cannot be empty"};
        }
        if (balanceCents_ < 0) {
            throw std::invalid_argument{"Opening balance cannot be negative"};
        }
    }

    BankAccount(const BankAccount&) = delete;
    BankAccount& operator=(const BankAccount&) = delete;

    void deposit(std::int64_t amountCents) {
        requirePositive(amountCents);
        balanceCents_ += amountCents;
    }

    void withdraw(std::int64_t amountCents) {
        requirePositive(amountCents);
        if (amountCents > balanceCents_) {
            throw std::runtime_error{"Insufficient balance"};
        }
        balanceCents_ -= amountCents;
    }

    [[nodiscard]] const std::string& accountNumber() const noexcept {
        return accountNumber_;
    }

    [[nodiscard]] std::int64_t balanceCents() const noexcept {
        return balanceCents_;
    }

private:
    static void requirePositive(std::int64_t amountCents) {
        if (amountCents <= 0) {
            throw std::invalid_argument{"Amount must be positive"};
        }
    }

    std::string accountNumber_;
    std::int64_t balanceCents_;
};

int main() {
    BankAccount account{"VN-001", 100'000};
    account.deposit(50'000);
    account.withdraw(20'000);
    std::cout << account.accountNumber() << ": "
              << account.balanceCents() << " cents\n";
}
```

- Constructor tạo account hợp lệ hoặc không tạo object.
- Account number đóng vai trò identity trong model đơn giản này.
- `deposit` và `withdraw` diễn đạt domain operations, rõ hơn `setBalance`.
- Copy bị xóa vì duplicate account identity không có nghĩa trong context.
- Members là values nên destructor mặc định là đủ; class đang theo Rule of 0 cho resource management.

## 4. Bad Design

```cpp
#include <cstdint>
#include <string>

class BankAccount {
public:
    BankAccount() = default;

    void setAccountNumber(std::string value) { accountNumber_ = value; }
    void setBalance(std::int64_t value) { balanceCents_ = value; }

    [[nodiscard]] std::int64_t balance() const { return balanceCents_; }

private:
    std::string accountNumber_;
    std::int64_t balanceCents_{0};
};

int main() {
    BankAccount account;
    account.setBalance(-500);
}
```

Object tồn tại trong trạng thái thiếu account number, setter cho phép balance âm và API không nói deposit/withdraw có nghĩa gì. Root cause là lifecycle cho phép **construct first, validate later** nhưng không enforce bước later.

## 5. Better Design

Focused example dùng constructor nhận toàn bộ required state và operations theo vocabulary domain. Đây là improvement đủ dùng; chưa cần `AccountFactory` vì construction không có external dependency hoặc nhiều bước phức tạp.

Nếu account number được cấp từ database, có thể truyền một typed `AccountId` vào constructor. Factory chỉ có ích khi nó thực sự chịu trách nhiệm tạo ID, load policy hoặc phối hợp nhiều dependencies.

## 6. Trade-offs

| Choice | Benefit | Cost | Use when |
|---|---|---|---|
| Default constructor + setters | Dễ dùng với một số serializer/framework | Có partially initialized state | Framework bắt buộc và validation được kiểm soát ở boundary |
| Validating constructor | Invalid object khó tồn tại | Constructor có thể fail, nhiều parameters nếu model lớn | Required state nhỏ và invariant rõ |
| Named factory | Tên creation intent rõ, có thể điều phối dependency | Thêm API/indirection | Có nhiều creation modes hoặc construction phức tạp |
| Delete copy | Bảo vệ entity identity | Hạn chế dùng trong containers/algorithms cần copy | Copy entity không có nghĩa domain |

## 7. Common Mistakes

- **Không dùng initializer list:** member bị default-construct rồi assign, hoặc không thể khởi tạo `const`/reference member.
- **Tin initializer list quyết định order:** thực tế declaration order quyết định; mismatch dễ tạo bug dependency giữa members.
- **Constructor nhận object rồi giữ reference không rõ lifetime:** object có thể dangling sau construction.
- **Cho entity copy mặc định:** tạo hai objects cùng identity mà không định nghĩa semantics.
- **Destructor tự quản memory dù member đã là RAII type:** tăng nguy cơ double free và phá Rule of 0.

## 8. Misconceptions

- **“Constructor luôn nên làm mọi validation.”** Nó nên thiết lập class invariant; validation cần I/O hoặc business workflow lớn có thể thuộc service/factory.
- **“Destructor chỉ cần khi dùng `new`.”** Destructor kết thúc mọi resource lifetime; RAII members tự làm việc đó qua destructor mặc định.
- **“Hai objects có cùng fields là cùng object.”** Đúng với value semantics, không nhất thiết đúng với entity identity.
- **“Mọi class phải copyable.”** Copyability là quyết định semantic.

## 9. Quick Review Questions

1. Class khác object như thế nào?
2. Invariant phải đúng tại những observable point nào?
3. Vì sao member initialization order không theo initializer list?
4. Khi nào nên xóa copy constructor của entity?
5. Vì sao `setBalance` yếu hơn `deposit` và `withdraw`?

### Answer Key

1. Class là type definition; object là một instance sống trong storage cụ thể.
2. Sau construction và trước/sau mọi public operation quan sát được.
3. C++ dùng declaration order để destruction có thứ tự đảo ngược xác định và nhất quán.
4. Khi copy tạo duplicate identity hoặc không có nghĩa trong domain.
5. Setter bỏ qua intent và business rules; domain operations có preconditions rõ.

## 10. Mini Exercise

### Problem

Thiết kế `DateRange` gồm start day và end day dưới dạng integer đơn giản. `start <= end` phải luôn đúng; object trả lời một day có nằm trong range không.

### Acceptance Criteria

- Constructor từ chối range đảo ngược.
- Không có setter làm invariant bị phá.
- `contains(day)` là `const` operation.
- Copy được phép vì đây là value object.
- Giải thích vì sao `DateRange` khác `Reservation` về identity.

## 11. One-Minute Summary

Class mô tả type; object có lifecycle cụ thể. Constructor thiết lập valid state, public operations duy trì invariant và destructor kết thúc lifetime. Copy/move phải phản ánh nghĩa của type, đặc biệt khi phân biệt value object và entity.

Module tiếp theo dùng lifecycle/invariant này để giải thích Encapsulation và Abstraction.

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

