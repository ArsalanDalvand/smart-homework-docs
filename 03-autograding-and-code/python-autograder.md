# داوری خودکار سوالات پایتون (Python Autograder)

سامانه قابلیت ارزیابی امن و هوشمند کدهای پایتون را با فریم‌ورک استاندارد `unittest` یا تست‌های تابعی فراهم کرده است.

---

## ۱. ساختار یک سوال کدنویسی پایتون

یک سوال داوری خودکار پایتون معمولاً شامل فایل‌های زیر است:

```text
questions/pythonFibonacci/
├── info.json
├── question.html
└── tests/
    ├── test.py             # تست‌کیس‌های ارزیابی کد دانشجو
    └── ans.py              # راه‌حل مرجع استاد (اختیاری)
```

---

## ۲. صورت سوال در `question.html`

```html
<pl-question-panel>
  <p>تابعی به نام <code>fibonacci(n)</code> بنویسید که عدد $n$-ام دنباله فیبوناچی را محاسبه و بازگرداند.</p>
  <p>مثال: <code>fibonacci(5)</code> باید عدد <code>5</code> را برگرداند.</p>
</pl-question-panel>

<pl-file-editor file-name="solution.py" ace-mode="ace/mode/python"></pl-file-editor>
<pl-submission-panel></pl-submission-panel>
```

---

## ۳. نگارش تست‌کیس‌ها در `tests/test.py`

```python
import unittest
from solution import fibonacci

class TestFibonacci(unittest.TestCase):
    def test_base_cases(self):
        """بررسی مقادیر اولیه فیبوناچی"""
        self.assertEqual(fibonacci(0), 0)
        self.assertEqual(fibonacci(1), 1)

    def test_larger_values(self):
        """بررسی مقادیر بزرگ‌تر"""
        self.assertEqual(fibonacci(7), 13)
        self.assertEqual(fibonacci(10), 55)

    def test_performance(self):
        """بررسی کارایی و پیچیدگی زمانی"""
        self.assertEqual(fibonacci(30), 832040)
```

---

## ۴. ویژگی‌های کلیدی داوری پایتون
1. **اجرا در کانتینر ایزوله**: دانشجو دسترسی به شبکه، فایل‌های سیستم یا کدهای سایر دانشجویان ندارد.
2. **سقف زمان و حافظه (Timeout & Memory Limit)**: اگر کد دانشجو وارد حلقه بی‌نهایت شود، پس از چند ثانیه متوقف شده و خطای Timeout گزارش می‌شود.
3. **تست‌های مخفی (Hidden Tests)**: استاد می‌تواند مشخص کند که نتیجه برخی تست‌ها تا پایان مهلت آزمون از دید دانشجو پنهان بماند تا از Hardcode کردن پاسخ‌ها جلوگیری شود.
