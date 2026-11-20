# داوری خودکار کدهای C و ++C با `cgrader`

ارزیابی کدهای زبان‌های C و ++C از طریق کانتینر `prairielearn/grader-c` و فریم‌ورک اختصاصی `cgrader` انجام می‌گیرد. این محیط قادر است فرآیند کامپایل با GCC/Clang، بررسی نشت حافظه (Memory Leaks)، سرریز بافر با AddressSanitizer و اجرای تست‌های واحد را در کانتینری ایزوله انجام دهد.

---

## ۱. تنظیمات `info.json` برای زبان‌های کامپایلری

```json
{
  "uuid": "3d5f1a29-8472-4d2a-bc91-23e5904d12ab",
  "title": "پیاده‌سازی لیست پیوندی در C",
  "type": "v3",
  "singleVariant": true,
  "gradingMethod": "External",
  "externalGradingOptions": {
    "image": "prairielearn/grader-c",
    "entrypoint": "/grader/run.sh",
    "timeout": 15,
    "enableNetworking": false
  }
}
```

---

## ۲. منطق داوری با فریم‌ورک `cgrader` (`tests/test.py`)

منطق کامپایل و تست در فایل `tests/test.py` با ارث‌بری از کلاس `cgrader.CGrader` پیاده‌سازی می‌شود:

```python
import cgrader

class LinkedListGrader(cgrader.CGrader):
    def tests(self):
        # ۱. مرحله کامپایل کد دانشجو به همراه تست‌های استاد و ابزار AddressSanitizer
        self.compile_file(
            "student_linked_list.c",                  # فایل ارسالی دانشجو
            "test_bin",                              # نام فایل اجرایی خروجی
            add_c_file="/grade/tests/main_test.c",    # فایل تست استاد
            flags="-Wall -Werror -fsanitize=address,undefined -g",  # پرچم‌های شناسایی باگ و نشت حافظه
            points=2                                 # بارم اختصاصی به موفقیت در کامپایل
        )
        
        # ۲. اجرای تست ۱: درج در انتهای لیست
        self.run_command(
            "./test_bin insert_test", 
            name="تست ۱: درج ۱۰ گره در انتهای لیست پیوندی", 
            points=4,
            timeout=2
        )
        
        # ۳. اجرای تست ۲: حذف گره و بررسی آزادسازی حافظه (free)
        # در صورت بروز نشت حافظه یا دسترسی به خانه نامعتبر، AddressSanitizer برنامه را متوقف می‌کند
        self.run_command(
            "./test_bin delete_test", 
            name="تست ۲: حذف گره و بررسی آزادسازی کامل حافظه", 
            points=4,
            timeout=2
        )

g = LinkedListGrader()
g.start()
```

---

## ۳. فایل تست استاد (`tests/main_test.c`)

فایل `main_test.c` توابع دانشجو را فراخوانی کرده و در صورت صحت خروجی با کد `0` و در صورت بروز خطا با کد غیرصفر خارج می‌شود:

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// سربرگ توابع دانشجو
#include "/grade/student/student_linked_list.h"

int main(int argc, char* argv[]) {
    if (argc < 2) return 1;

    if (strcmp(argv[1], "insert_test") == 0) {
        Node* head = NULL;
        insert_end(&head, 10);
        insert_end(&head, 20);
        if (head == NULL || head->data != 10 || head->next->data != 20) {
            fprintf(stderr, "خطا در ترتیب درج عناصر.\n");
            return 1;
        }
        free_list(head);
        return 0; // موفقیت
    }

    if (strcmp(argv[1], "delete_test") == 0) {
        Node* head = NULL;
        insert_end(&head, 5);
        delete_node(&head, 5);
        if (head != NULL) {
            fprintf(stderr, "گره به درستی حذف نگردید.\n");
            return 1;
        }
        return 0; // موفقیت
    }

    return 2;
}
```

---

## ۴. خطاهای متداول و نحوه تفسیر آن‌ها توسط گرایدر

- **Segmentation Fault (SIGSEGV):** تلاش برای خواندن یا نوشتن روی اشاره‌گر تهی (`NULL pointer`) یا خارج از محدوده حافظه تخصیص‌یافته.
- **AddressSanitizer LeakSanitizer:** عدم استفاده از `free()` به ازای هر `malloc()` فراخوانی‌شده.
- **TimeLimitExceeded:** وجود حلقه `while` که شرط خروج آن هرگز برقرار نمی‌شود.
