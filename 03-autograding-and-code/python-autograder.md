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
