# داوری خودکار سوالات پایتون (Python Autograder)

داوری خودکار پایتون با کانتینر ایزوله `prairielearn/grader-python` اجرا می‌شود. کدهای ارسالی دانشجو درون یک محیط لینوکسی بدون دسترسی به شبکه و با محدودیت‌های شدید حافظه و زمان پردازش، ارزیابی می‌گردند.

---

## ۱. ساختار پوشه سوالات برنامه‌نویسی پایتون

```text
questions/python_matrix_transpose/
├── info.json
├── question.html
└── tests/
    └── test.py
```

---

## ۲. پیکربندی `info.json` برای ارزیابی خارجی

```json
{
  "uuid": "7a82b3d1-923f-42da-9f12-0f9c2a823e51",
  "title": "پیاده‌سازی ترانهاده ماتریس در پایتون",
  "type": "v3",
  "singleVariant": true,
  "gradingMethod": "External",
  "externalGradingOptions": {
    "image": "prairielearn/grader-python",
    "entrypoint": "/grader/run.sh",
    "timeout": 10,
    "enableNetworking": false
  }
}
```
- `enableNetworking: false`: مسدودسازی کامل اتصال به اینترنت برای جلوگیری از نشت اطلاعات یا ارسال درخواست به سرورهای خارجی.
- `timeout: 10`: قطع خودکار پروسه در صورت اجرای بیش از ۱۰ ثانیه (مهار حلقه‌های بی‌نهایت).

---

## ۳. طراحی رابط کاربری در `question.html`

```html
<pl-question-panel>
  <p>تابعی به نام <code>transpose(matrix)</code> بنویسید که یک ماتریس دوبعدی را دریافت کرده و ترانهاده آن را بازگرداند.</p>
</pl-question-panel>

<pl-file-editor 
    file-name="solution.py" 
    ace-mode="ace/mode/python" 
    source-file-name="starter_code.py">
</pl-file-editor>

<pl-submission-panel>
  <!-- نمایش جدول نتایج تست‌کیس‌ها و نمرات تفکیکی -->
  <pl-external-grader-results></pl-external-grader-results>
  
  <!-- نمایش پیش‌نمایش کد ارسال‌شده -->
  <pl-file-preview></pl-file-preview>
</pl-submission-panel>
```

---

## ۴. نوشتن آزمون‌ها با `PLTestCase` و ماژول `Feedback` (`tests/test.py`)

فریم‌ورک استاندارد ارزیابی پایتون از کلاس `PLTestCase` و دکوراتورهای `@points` و `@name` استفاده می‌کند:

```python
from pl_unit_test import PLTestCase
from pl_helpers import points, name
import numpy as np
from code_feedback import Feedback

class TestMatrix(PLTestCase):
    
    @points(3)
    @name("تست عمومی ۱: ماتریس متقارن و ساده")
    def test_0(self):
        # بررسی وجود تابع در کد ارسالی دانشجو
        if not hasattr(self.st, "transpose"):
            Feedback.set_score(0)
            Feedback.add_feedback("تابع transpose در فایل ارسالی یافت نشد.")
            return
            
        mat = [[1, 2], [3, 4]]
        expected = [[1, 3], [2, 4]]
        student_res = self.st.transpose(mat)
        
        # مقایسه خروجی با پیام راهنما
        if student_res == expected:
            Feedback.set_score(1.0)
            Feedback.add_feedback("پاسخ شما برای ماتریس‌های ۲×۲ کاملاً صحیح است.")
        else:
            Feedback.set_score(0)
            Feedback.add_feedback(f"ورودی {mat}؛ خروجی مورد انتظار {expected} بود اما خروجی شما {student_res} شد.")

    @points(4)
    @name("تست عمومی ۲: ابعاد غیرمربعی و تک‌سطری")
    def test_1(self):
        mat = [[1, 2, 3]]
        expected = [[1], [2], [3]]
        student_res = self.st.transpose(mat)
        self.assertEqual(student_res, expected, "خطا در مدیریت ماتریس‌های سطری.")

    @points(3)
    @name("تست مخفی: ماتریس‌های بزرگ و داده‌های تصادفی")
    def test_hidden(self):
        # ورودی در بازخورد چاپ نمی‌شود تا از هاردکد جلوگیری گردد
        mat = np.random.randint(1, 100, size=(50, 20)).tolist()
        expected = np.array(mat).T.tolist()
        student_res = self.st.transpose(mat)
        self.assertEqual(student_res, expected, "الگوریتم شما در ابعاد بزرگ با شکست مواجه شد.")
```

### متدهای کاربردی کلاس `Feedback`:
- `Feedback.check_scalar(name, student_val, true_val, rtol=1e-3)`: بررسی متغیرهای عددی اسکالر.
- `Feedback.check_numpy_array_allclose(name, student_arr, true_arr)`: مقایسه دقیق آرایه‌های چندبعدی NumPy.
- `Feedback.check_dataframe(name, student_df, true_df)`: بررسی جداول کتابخانه Pandas.

---

## ۵. داوری با فریم‌ورک مدرن `pytest` (`grader-python-pytest:lite`)

در نسخه جدید پلتفرم، علاوه بر `unittest` سنتی، پشتیبانی کامل از `pytest` با کانتینر فوق‌سبک `prairielearn/grader-python-pytest:lite` فراهم شده است (این رویکرد در دوره نمونه دانشگاه علم و صنعت نیز پیاده‌سازی شده است).

### ۵.۱. پیکربندی در `info.json`:
```json
"externalGradingOptions": {
  "image": "prairielearn/grader-python-pytest:lite",
  "entrypoint": "/grader/run.sh",
  "timeout": 15
}
```

### ۵.۲. نگارش آزمون با سینتکس تمیز `pytest`:
در فایل `tests/test_solution.py`:
```python
import pytest
import solution  # کدی که توسط دانشجو ارسال شده است

def test_circle_area_basic():
    """تست شماره ۱: شعاع یک"""
    assert pytest.approx(solution.circle_area(1), 1e-4) == 3.14159

@pytest.mark.parametrize("r, expected", [
    (0, 0.0),
    (2.5, 19.63495),
    (10, 314.15926)
])
def test_circle_area_values(r, expected):
    """تست شماره ۲: مقادیر مختلف اعشاری و صفر"""
    assert pytest.approx(solution.circle_area(r), 1e-4) == expected

def test_negative_radius_handling():
    """تست شماره ۳: رفتار با شعاع منفی طبق قرارداد تابع"""
    assert solution.circle_area(-5) == 0
```

---

## ۶. هشدارهای حیاتی برای دانشجویان و مدرسان

> [!CAUTION]
> **اشتباه رایج دانشجویان: استفاده از `print()` به جای `return`:**
> بزرگ‌ترین خطایی که دانشجویان تازه‌کار مرتکب می‌شوند، چاپ خروجی با دستور `print(...)` است. تست‌کیس‌های داور خودکار مقدار بازگشتی تابع (`return`) را بررسی می‌کنند؛ در صورت استفاده از `print`، تابع مقدار `None` بازمی‌گرداند و تمام تست‌ها ناموفق ثبت خواهند شد! در صورت سوال حتماً این موضوع را به دانشجویان گوشزد نمایید.

> [!WARNING]
> **حلقه‌های بی‌نهایت و زمان اجرا (Timeout):**
> اگر کد دانشجو وارد حلقه بی‌نهایت (`while True`) شود، کانتینر داوری پس از سپری شدن زمان `timeout` (پیش‌فرض ۱۰ تا ۱۵ ثانیه) متوقف شده و خطای `TimeoutError` صادر می‌کند. تست‌های اعتبارسنجی را به نحوی بنویسید که ورودی‌های با ابعاد نامتعارف سیستم را مسدود نکنند.
