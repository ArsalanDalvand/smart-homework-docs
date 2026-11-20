# اصول طراحی سوالات

هر سوال در سامانه یک پوشه مستقل داخل پوشه `questions/` است که شامل سه بخش اصلی است:
1. `info.json`: شناسه و مشخصات متادیتای سوال
2. `server.py`: اسکریپت تولید اعداد تصادفی و محاسبه پاسخ صحیح
3. `question.html`: ظاهر سوال و فیلدهای ورودی پاسخ

---

## ۱. فایل متادیتا (`info.json`)

```json
{
  "uuid": "a1b2c3d4-e5f6-7890-abcd-ef0123456789",
  "title": "محاسبه انرژی جنبشی جسم",
  "topic": "فیزیک مکانیک",
  "tags": ["انرژی", "حرکت"],
  "type": "v3"
}
```

---

## ۲. اسکریپت تولید داده‌ها (`server.py`)

تابع `generate(data)` قبل از هر بار نمایش سوال به دانشجو اجرا می‌شود:

```python
import random

def generate(data):
    # تولید جرم بین ۲ تا ۱۰ کیلوگرم
    m = random.randint(2, 10)
    # تولید سرعت بین ۳ تا ۱۵ متر بر ثانیه
    v = random.randint(3, 15)
    
    # ذخیره متغیرها جهت نمایش در صورت سوال
    data["params"]["m"] = m
    data["params"]["v"] = v
    
    # محاسبه پاسخ صحیح بر اساس متغیرهای تصادفی
    ke = 0.5 * m * (v ** 2)
    data["correct_answers"]["ke"] = ke
```

---

## ۳. ظاهر سوال (`question.html`)

در فایل `question.html`، متغیرهای تعریف‌شده در `data["params"]` را با `{{params.variable}}` فراخوانی می‌کنیم:

```html
<pl-question-panel>
  <p>
    جسمی به جرم <strong>{{params.m}} kg</strong> با سرعت <strong>{{params.v}} m/s</strong> در حال حرکت است.
  </p>
  <p>
    انرژی جنبشی این جسم را بر حسب ژول (J) محاسبه کنید:
  </p>
</pl-question-panel>

<pl-number-input answers-name="ke" label="K =" suffix="J" comparison="sigfigs" digits="3"></pl-number-input>
```

### بخش‌های اصلی `question.html`:
- `<pl-question-panel>`: کادر صورت سوال.
- تگ‌های پاسخ (مانند `<pl-number-input>` یا `<pl-multiple-choice>`): کادرهای تعاملی دریافت پاسخ.
- `<pl-submission-panel>` (اختیاری): نمایش توضیحات یا جزئیات پس از ارسال پاسخ.
- `<pl-answer-panel>` (اختیاری): نمایش راه‌حل تشریحی به دانشجو پس از اتمام مهلت آزمون.
