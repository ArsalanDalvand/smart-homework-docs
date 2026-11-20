# المان‌ها و تگ‌های تعاملی سوالات

سامانه دارای مجموعه‌ای غنی از تگ‌های آماده HTML است که بدون نیاز به جاوااسکریپت، امکان دریافت و داوری انواع پاسخ‌ها را فراهم می‌کنند.

---

## ۱. سوالات چندگزینه‌ای (`pl-multiple-choice`)

برای آزمون‌های تستی با یک گزینه صحیح:

```html
<pl-question-panel>
  <p>کدام‌یک از پیچیدگی‌های زمانی زیر بهینه‌تر است؟</p>
</pl-question-panel>

<pl-multiple-choice answers-name="ans" fixed-order="false">
  <pl-answer correct="true">$O(1)$</pl-answer>
  <pl-answer correct="false">$O(n)$</pl-answer>
  <pl-answer correct="false">$O(n \log n)$</pl-answer>
  <pl-answer correct="false">$O(n^2)$</pl-answer>
</pl-multiple-choice>
```

---

## ۲. سوالات چند پاسخی یا چک‌باکس (`pl-checkbox`)

برای سوالاتی که ممکن است چند گزینه صحیح داشته باشند:

```html
<pl-checkbox answers-name="cb" min-select="1" max-select="3">
  <pl-answer correct="true">پایتون یک زبان مفسری است.</pl-answer>
  <pl-answer correct="true">لیست‌ها در پایتون تغییرپذیر (Mutable) هستند.</pl-answer>
  <pl-answer correct="false">تاپل‌ها (Tuple) در پایتون قابل ویرایش هستند.</pl-answer>
</pl-checkbox>
```

---

## ۳. سوالات عددی و محاسباتی (`pl-number-input`)

با قابلیت تعیین خطای مجاز (ارقام با معنی یا درصد خطا):

```html
<pl-number-input 
  answers-name="resistance" 
  label="مقاومت معادل $R_{eq} =$" 
  suffix="$\Omega$" 
  comparison="relabs" 
  rtol="0.02">
</pl-number-input>
```

---

## ۴. سوالات متنی و نمادین ریاضی (`pl-symbolic-input`)

برای دریافت فرمول‌های ریاضی و تطبیق آن‌ها با کتابخانه SymPy:

```html
<pl-symbolic-input 
  answers-name="derivative" 
  variables="x, a" 
  label="مشتق تابع نسبت به x:">
</pl-symbolic-input>
```

---

## ۵. سوالات ماتریسی و برداری (`pl-matrix-input`)

برای دروس جبر خطی، ریاضی مهندسی و هوش مصنوعی:

```html
<pl-matrix-input answers-name="matrix_A" rows="2" columns="2" label="A ="></pl-matrix-input>
```

---

## ۶. جعبه کد تعاملی (`pl-code` و `pl-file-editor`)

برای نمایش کدهای رنگی و ویرایشگر کد درون مرورگر:

```html
<!-- نمایش کد با فرمت سینتکس -->
<pl-code language="python">
def add(a, b):
    return a + b
</pl-code>

<!-- ویرایشگر کد برای نوشتن تابع توسط دانشجو -->
<pl-file-editor file-name="solution.py" ace-mode="ace/mode/python"></pl-file-editor>
```
