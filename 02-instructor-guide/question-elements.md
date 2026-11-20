# المان‌ها و تگ‌های تعاملی سوالات (`question.html`)

سامانه دارای مجموعه‌ای غنی از تگ‌های وب اختصاصی با پیشوند `pl-` است که در فایل `question.html` استفاده می‌شوند و ورود اطلاعات، اعتبارسنجی و تعامل کاربر را بدون نیاز به کدهای پیچیده جاوااسکریپت ممکن می‌سازند.

---

## ۱. جدول مقایسه و انتخاب المان‌های ورودی

| نام المان | نوع داده ورودی | روش‌های اعتبارسنجی | کاربرد نمونه |
| :--- | :--- | :--- | :--- |
| `<pl-number-input>` | اعداد ممیز شناور (Float) | خطای نسبی/مطلق (`rtol`, `atol`)، ارقام با معنی (`sigfigs`) | محاسبات فیزیکی، مهندسی و مالی |
| `<pl-integer-input>` | اعداد صحیح (Integer) | برابری ریاضی دقیق بدون خطای اعشاری | شمارش، ابعاد ماتریس، نظریه اعداد |
| `<pl-symbolic-input>` | عبارات جبری و ریاضیاتی | تطابق جبری با SymPy (هم‌ارزی فرمول‌ها) | مشتق، انتگرال، بسط توابع، فرمول‌ها |
| `<pl-units-input>` | عدد به همراه یکای فیزیکی | اعتبارسنجی یکا با Pint (تبدیل خودکار واحدها) | ابعاد فیزیکی (`12.5 m/s` معادل `45 km/h`) |
| `<pl-string-input>` | رشته متنی (String) | الگوی RegEx، حذف فاصله‌ها، حساسیت به حروف | کلیدواژه‌ها، نام الگوریتم، فرمان‌های لینوکس |
| `<pl-matrix-input>` | ماتریس اعداد | ابعاد ماتریس، تلرانس درایه‌ها | جبر خطی، سیستم‌های کنترل، انتقال حرارت |
| `<pl-multiple-choice>` | چهارگزینه‌ای تک‌پاسخی | انتخاب یک گزینه از چند گزینه | پرسش‌های مفهومی و تئوریک |
| `<pl-checkbox>` | چندگزینه‌ای چندپاسخی | انتخاب تمام موارد درست | تشخیص عبارات صحیح و غلط |
| `<pl-dropdown>` | منوی کشویی انتخابی | انتخاب گزینه مناسب در متن | پر کردن جاهای خالی در متن سوال |
| `<pl-order-blocks>` | مرتب‌سازی بلوک‌ها (Parsons) | ترتیب گام‌ها با Drag & Drop | بازسازی ترتیب کدهای پایتون یا اثبات قضیه |
| `<pl-file-editor>` | کدنویسی چندزبانه | اعتبارسنجی با گرایدر خودکار | تمرین‌های برنامه‌نویسی پایتون، C++ و جاوا |
| `<pl-file-upload>` | ارسال فایل ضمیمه | فرمت و پسوند فایل، حداکثر حجم | ارسال گزارش کار آزمایشگاه، PDF یا فایل‌های CAD |

---

## ۲. راهنمای تفصیلی ویژگی‌های المان‌های محاسباتی

### ۲.۱. ورودی عددی (`<pl-number-input>`)
```html
<pl-number-input 
    answers-name="ans_current" 
    label="جریان الکتریکی (I):" 
    suffix="آمپر" 
    comparison="relabs" 
    rtol="0.01" 
    atol="1e-4" 
    placeholder="0.0">
</pl-number-input>
```
- `comparison="relabs"`: مقایسه با ترکیب خطای نسبی (`rtol`) و خطای مطلق (`atol`).
- `comparison="sigfigs"`: مقایسه بر اساس تعداد ارقام بامعنی (`digits="3"`).
- `comparison="decdigits"`: مقایسه بر اساس تعداد ارقام بعد از ممیز اعشار (`digits="2"`).
- `show-help-text="true"`: نمایش راهنمای اعشار مجاز برای دانشجو.

### ۲.۲. ورودی نمادین جبری (`<pl-symbolic-input>`)
```html
<p>مشتق تابع $f(x) = \sin(x^2)$ را وارد کنید:</p>
<pl-symbolic-input 
    answers-name="ans_deriv" 
    variables="x" 
    allow-blank="false" 
    placeholder="مثلاً 2*x*cos(x**2)">
</pl-symbolic-input>
```
- `variables`: متغیرهای نمادین مجاز (جداشده با کاما، مثلاً `x, y, t`).
- موتور سامانه عبارات معادل مانند `2*x*cos(x^2)` و `cos(x**2)*2*x` را کاملاً هم‌ارز و صحیح تشخیص می‌دهد.

### ۲.۳. ورودی به همراه یکای فیزیکی (`<pl-units-input>`)
```html
<pl-units-input 
    answers-name="ans_velocity" 
    label="سرعت نهایی:" 
    default-units="m/s">
</pl-units-input>
```
- سیستم تبدیل واحدها را به صورت هوشمند مدیریت می‌کند. اگر پاسخ `10 m/s` باشد، ثبت `36 km/h` توسط دانشجو نیز نمره کامل خواهد گرفت.

---

## ۳. المان‌های سازماندهی و انتخاب گزینه‌ای

### ۳.۱. سوالات چندگزینه‌ای پیشرفته (`<pl-multiple-choice>`)
```html
<pl-multiple-choice 
    answers-name="ans_algo" 
    weight="1" 
    fixed-order="false" 
    inline="false" 
    none-of-the-above="random">
    <pl-answer correct="true">الگوریتم دایکسترا (Dijkstra)</pl-answer>
    <pl-answer correct="false">الگوریتم بلمن-فورد (Bellman-Ford)</pl-answer>
    <pl-answer correct="false">الگوریتم پریم (Prim)</pl-answer>
</pl-multiple-choice>
```
- `fixed-order="false"`: بر زدن تصادفی ترتیب گزینه‌ها در هر باز شدن سوال.
- `none-of-the-above="random"`: اضافه شدن هوشمند گزینه «هیچ‌کدام از موارد» با موقعیت متغیر.

### ۳.۲. مسائل پارسونز و مرتب‌سازی بلوک‌ها (`<pl-order-blocks>`)
```html
<p>خطوط کد زیر را مرتب کنید تا فاکتوریل عدد $n$ محاسبه شود:</p>
<pl-order-blocks answers-name="ans_code" source-blocks-order="random">
    <pl-block correct="true" tag="1">def fact(n):</pl-block>
    <pl-block correct="true" tag="2">&nbsp;&nbsp;&nbsp;&nbsp;if n <= 1: return 1</pl-block>
    <pl-block correct="true" tag="3">&nbsp;&nbsp;&nbsp;&nbsp;return n * fact(n - 1)</pl-block>
    <pl-block correct="false">def factorial():</pl-block> <!-- بلوک مزاحم / گمراه‌کننده -->
</pl-order-blocks>
```

---

## ۴. المان‌های نمایش محتوا و فرمول‌ها

### ۴.۱. نمایش ماتریس و متغیر پایتون با فرمت زیبا (`<pl-variable-output>`)
اگر در `server.py` یک ماتریس نامپای داشته باشید:
```python
# در server.py
data["params"]["matrix_A"] = pl.to_json(np.array([[2, -1], [4, 3]]))
```
در `question.html`:
```html
<p>ماتریس ضرایب به صورت زیر داده شده است:</p>
<pl-variable-output var-name="matrix_A"></pl-variable-output>
```

### ۴.۲. ادیتور کد درون‌متنی (`<pl-file-editor>`)
```html
<pl-file-editor 
    file-name="solution.py" 
    ace-mode="ace/mode/python" 
    ace-theme="ace/theme/monokai" 
    min-lines="10" 
    max-lines="30">
</pl-file-editor>
```
