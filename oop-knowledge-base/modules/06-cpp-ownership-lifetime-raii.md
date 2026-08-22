# 6. Ownership, Lifetime và RAII trong C++

> **Thời lượng:** 45–60 phút  
> **Prerequisites:** [Module 2 — Class, Object và Object Lifecycle](02-class-object-lifecycle.md), [Module 4 — Object Relationships và Composition](04-object-relationships-composition.md), [Module 5 — Inheritance và Polymorphism](05-inheritance-polymorphism.md)  
> **Status:** Reviewed

## Overview

Trong C++, object design không tách rời lifetime và resource ownership. Một relationship nhìn hợp lý trên class diagram vẫn có thể gây dangling pointer, leak hoặc double deletion nếu API không nói object nào sở hữu resource.

Resource Acquisition Is Initialization (**RAII**) gắn resource lifetime với object lifetime. Ưu tiên value members và Standard Library RAII types giúp phần lớn domain classes tuân Rule of 0.

## Learning Outcomes

Sau module này, người học có thể:

- phân biệt owner, borrower và observer;
- chọn value, reference, `unique_ptr`, `shared_ptr` hoặc `weak_ptr` theo lifetime;
- giải thích RAII và deterministic destruction;
- nhận diện raw owning pointer, ownership cycle và overuse `shared_ptr`;
- dùng Rule of 0 làm default cho class design.

## 1. Mental Model

```text
Ownership answers: Ai chịu trách nhiệm kết thúc resource lifetime?
Lifetime answers:  Object/reference còn hợp lệ đến khi nào?
RAII answers:      Làm sao cleanup xảy ra tự động trên mọi control path?
```

Chọn semantics trước syntax:

1. Nếu member là phần của object và có value semantics, lưu by value.
2. Nếu object sở hữu polymorphic/optional resource duy nhất, cân nhắc `unique_ptr`.
3. Chỉ dùng `shared_ptr` khi nhiều owners thật sự cùng quyết định lifetime.
4. Dùng reference/pointer cho borrowing khi lifetime guarantee rõ.

## 2. Core Concepts

### Lifetime và Storage Duration

Storage duration nói storage tồn tại bao lâu; object lifetime nói instance hợp lệ tồn tại trong storage bao lâu. Trong quick review, điều quan trọng là không giữ reference/pointer lâu hơn object đích.

### Owner, Borrower và Observer

- **Owner:** chịu trách nhiệm cleanup.
- **Borrower:** dùng object nhưng không kéo dài lifetime.
- **Observer:** theo dõi object có shared lifetime mà không sở hữu, thường dùng `weak_ptr`.

### RAII

Constructor lấy resource hoặc thiết lập ownership; destructor giải phóng. Stack unwinding sẽ gọi destructors của automatic objects, nên cleanup vẫn xảy ra khi exception được ném.

### Smart Pointers

- `unique_ptr<T>`: exclusive ownership, movable, không copyable.
- `shared_ptr<T>`: shared ownership qua reference count.
- `weak_ptr<T>`: non-owning observation của shared object; phải `lock()` trước khi dùng.

### Rule of 0

Nếu class chỉ chứa RAII members, hãy để compiler tạo destructor/copy/move thích hợp. Custom special member functions chỉ xuất hiện khi semantics thực sự yêu cầu.

## 3. Focused C++ Example

`ReportService` sở hữu một polymorphic exporter duy nhất. `FileExporter` dùng `std::ofstream`, bản thân stream là RAII object.

```cpp
#include <fstream>
#include <memory>
#include <stdexcept>
#include <string>
#include <string_view>
#include <utility>

class Exporter {
public:
    virtual ~Exporter() = default;
    virtual void exportText(std::string_view text) = 0;
};

class FileExporter final : public Exporter {
public:
    explicit FileExporter(std::string path)
        : output_{std::move(path)} {
        if (!output_) {
            throw std::runtime_error{"Cannot open output file"};
        }
    }

    void exportText(std::string_view text) override {
        output_ << text << '\n';
        if (!output_) {
            throw std::runtime_error{"Cannot write report"};
        }
    }

private:
    std::ofstream output_;
};

class ReportService {
public:
    explicit ReportService(std::unique_ptr<Exporter> exporter)
        : exporter_{std::move(exporter)} {
        if (!exporter_) {
            throw std::invalid_argument{"Exporter is required"};
        }
    }

    void generate(std::string_view content) {
        exporter_->exportText(content);
    }

private:
    std::unique_ptr<Exporter> exporter_;
};

int main() {
    auto exporter = std::make_unique<FileExporter>("report.txt");
    ReportService service{std::move(exporter)};
    service.generate("OOP review completed");
}
```

- `ReportService` nhận ownership transfer bằng `unique_ptr` value parameter.
- `Exporter` có virtual destructor vì bị sở hữu qua base pointer.
- Destruction order: service → `unique_ptr` → concrete exporter → `ofstream` đóng file.
- Không class nào viết custom destructor; đây là Rule of 0.
- Nếu construction/write ném exception, RAII members đã tạo vẫn được cleanup.

## 4. Bad Design

```cpp
#include <string_view>

class Exporter {
public:
    virtual void exportText(std::string_view text) = 0;
};

class ReportService {
public:
    explicit ReportService(Exporter* exporter)
        : exporter_{exporter} {}

    ~ReportService() {
        delete exporter_;
    }

private:
    Exporter* exporter_;
};
```

API không nói pointer được borrow hay ownership transfer. Nếu caller truyền address của stack object, destructor sẽ `delete` invalid storage. Nếu caller cũng delete, double deletion xảy ra. Base còn thiếu virtual destructor. Root cause là ownership contract mơ hồ và manual cleanup.

## 5. Better Design

- Sở hữu duy nhất: nhận `std::unique_ptr<Exporter>` như focused example.
- Chỉ mượn: nhận `Exporter&`, không delete và ghi rõ borrower không được sống lâu hơn dependency.
- Shared lifetime thật: nhận `std::shared_ptr<Exporter>`, nhưng chỉ khi service và objects khác đều là owners.

Không chọn smart pointer dựa trên câu hỏi “có pointer không?”, mà dựa trên câu hỏi “ai quyết định lifetime?”.

## 6. Trade-offs

| Representation | Meaning | Cost/Risk | Use when |
|---|---|---|---|
| `T` value member | Part thuộc owner trực tiếp | Không runtime polymorphism, object size cố định | Default cho composition |
| `T&` / `T*` non-owning | Borrowed access | Dangling nếu lifetime contract sai | Dependency sống lâu hơn borrower |
| `unique_ptr<T>` | Exclusive dynamic ownership | Heap/indirection | Optional/polymorphic object có một owner |
| `shared_ptr<T>` | Shared lifetime ownership | Ref-count overhead, cycle risk | Nhiều owners thật sự |
| `weak_ptr<T>` | Observe shared object | Phải xử lý expired | Back-reference/cache/observer của shared graph |

## 7. Common Mistakes

- **`shared_ptr` everywhere:** dependency graph không còn owner rõ và có cycle.
- **Raw pointer vừa borrow vừa own tùy call site:** API không thể review an toàn.
- **Giữ reference tới temporary/local object:** reference dangling sau scope.
- **Manual `new/delete` trong domain class:** bỏ qua RAII facilities có sẵn.
- **Custom destructor nhưng quên copy semantics:** shallow copy tạo double deletion; ưu tiên Rule of 0.

## 8. Misconceptions

- **“Smart pointer tự động làm code an toàn.”** Sai ownership model vẫn gây cycle, retention hoặc dangling non-owning reference.
- **“`shared_ptr` an toàn hơn `unique_ptr`.”** Nó biểu diễn semantics khác, không phải mức an toàn cao hơn.
- **“RAII chỉ quản lý memory.”** File, lock, socket, transaction guard và nhiều resources khác đều áp dụng được.
- **“Rule of 0 nghĩa class không có destructor.”** Class có destructor do compiler sinh và members cleanup; ta không tự định nghĩa special members.

## 9. Quick Review Questions

1. Ownership khác access như thế nào?
2. Khi nào dùng value member thay vì `unique_ptr`?
3. Vì sao `shared_ptr` cycle không tự giải phóng?
4. Polymorphic base bị delete qua base pointer cần gì?
5. Rule of 0 giảm rủi ro nào?

### Answer Key

1. Access cho quyền dùng; ownership cho responsibility kết thúc lifetime.
2. Khi part luôn tồn tại cùng owner, có known concrete type và value semantics hợp lý.
3. Reference counts trong cycle không bao giờ về zero; cần phá ownership edge, thường bằng `weak_ptr`.
4. Virtual destructor.
5. Tránh manual resource cleanup và lỗi copy/move/destruction không nhất quán.

## 10. Mini Exercise

### Problem

Thiết kế `MediaPlayer` sở hữu một `Decoder` polymorphic. Player phải non-copyable nhưng movable thông qua members mặc định.

### Acceptance Criteria

- `Decoder` có virtual destructor.
- `MediaPlayer` nhận `unique_ptr<Decoder>` và reject null.
- Không dùng `new`, `delete` hoặc custom destructor.
- Giải thích destruction order.
- Nêu cách đổi design nếu player chỉ borrow decoder.

## 11. One-Minute Summary

Ownership là responsibility kết thúc lifetime; access không đồng nghĩa ownership. Ưu tiên value members và Rule of 0, dùng `unique_ptr` cho exclusive ownership, `shared_ptr` chỉ cho shared lifetime thật và RAII cho mọi resource.

Module tiếp theo dùng mental model responsibility/dependency này để ôn SOLID mà không over-engineer.

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

