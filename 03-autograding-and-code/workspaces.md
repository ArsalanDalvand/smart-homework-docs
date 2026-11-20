# میزکارهای ابری تعاملی (Cloud Workspaces: VS Code و JupyterLab)

میزکارهای ابری به فراگیران اجازه می‌دهند بدون نیاز به نصب هیچ‌گونه نرم‌افزار، کامپایلر یا پکیج روی کامپیوتر شخصی خود، یک محیط توسعه کامل (IDE) مبتنی بر لینوکس را مستقیماً درون مرورگر باز کرده و پروژه‌های بزرگ چندفایلی را توسعه دهند.

---

## ۱. نحوه اتصال Workspace به سیستم داوری خودکار (`gradedFiles`)

مهم‌ترین بخش تعریف یک Workspace، مشخص کردن فایل‌هایی است که پس از ویرایش توسط دانشجو باید برداشته شده و جهت نمره‌دهی به کانتینر Autograder ارسال شوند:

```json
// در فایل info.json
{
  "uuid": "5c1f9b3e-7a20-41be-8e01-2a4b8c91d3ef",
  "title": "پروژه ساخت وب‌سرور چندنخی در C",
  "type": "v3",
  "singleVariant": true,
  "workspaceOptions": {
    "image": "prairielearn/workspace-vscode",
    "port": 8080,
    "home": "/home/coder",
    "gradedFiles": [
      "src/**/*.c",
      "include/**/*.h",
      "Makefile"
    ]
  },
  "gradingMethod": "External",
  "externalGradingOptions": {
    "image": "prairielearn/grader-c",
    "entrypoint": "/grader/run.sh",
    "timeout": 30
  }
}
```

> [!IMPORTANT]
> **الزام `"singleVariant": true`:**  
> در تمامی سوالات دارای Workspace، باید حتماً ویژگی `"singleVariant": true` فعال باشد تا با تغییر تصادفی واریانت، فایل‌ها و تغییرات دانشجو پاک نشوند.

---

## ۲. انواع ایمیج‌های رسمی پشتیبانی‌شده برای Workspace

| ایمیج | ابزار ارائه شده | کاربرد آموزشی |
| :--- | :--- | :--- |
| `prairielearn/workspace-vscode-python` | VS Code + Python 3, NumPy, Matplotlib | یادگیری ماشین، پایتون پیشرفته، پروژه‌های پانداس |
| `prairielearn/workspace-vscode-cpp` | VS Code + GCC, GDB, CMake, Make | سیستم‌های عامل، ساختمان داده، مدارهای دیجیتال |
| `prairielearn/workspace-vscode-java` | VS Code + OpenJDK, Maven, Gradle | برنامه‌نویسی پیشرفته جاوا، وب‌سرویس‌ها |
| `prairielearn/workspace-jupyterlab-python` | JupyterLab + نوت‌بوک تعاملی پایتون | علوم داده، آمار مهندسی و حل معادلات دیفرانسیل |
| `prairielearn/workspace-xtermjs` | ترمینال خالص Bash لینوکس | آموزش خط فرمان، شل‌اسکریپت و گیت |

---

## ۳. تزریق کدهای اولیه و تمپلیت‌های تصادفی (Templates)

### ۳.۱. فایل‌های استارتر ثابت (`workspace/`):
هر فایل یا پوشه‌ای که درون پوشه `questions/<q_name>/workspace/` قرار دهید، در ابتدای راه‌اندازی کانتینر در دایرکتوری کار دانشجو کپی خواهد شد.

### ۳.۲. فایل‌های تمپلیت با مقادیر تصادفی (`workspaceTemplates/`):
اگر می‌خواهید کدهای اولیه دارای مقادیر متغیر باشند (مثلاً یک پورت تصادفی یا یک کلید تست منحصربه‌فرد برای هر دانشجو):
1. فایلی با پسوند `.mustache` در پوشه `workspaceTemplates/` بسازید:
   ```c
   // فایل workspaceTemplates/config.h.mustache
   #define SERVER_PORT {{params.random_port}}
   #define SECRET_TOKEN "{{params.secret_token}}"
   ```
2. مقادیر در `server.py` تولید می‌شوند:
   ```python
   def generate(data):
       data["params"]["random_port"] = random.randint(5000, 9000)
       data["params"]["secret_token"] = uuid.uuid4().hex[:12]
   ```
3. سامانه پیش از باز شدن ادیتور، متغیرها را جایگزین کرده و فایل نهایی `config.h` را می‌سازد.

---

## ۴. نکته حیاتی برای دانشجویان: ذخیره در برابر ارسال پاسخ

یکی از سوءتفاهم‌های رایج دانشجویان در کار با محیط ابری:
- **کلید `Ctrl + S` درون VS Code:** فقط فایل را درون کانتینر ذخیره می‌کند و هیچ نمره‌ای محاسبه نمی‌شود.
- **دکمه «ذخیره و داوری پاسخ» (Save & Grade):** در صفحه اصلی سوال، دانشجو باید روی دکمه ذخیره و داوری کلیک کند تا کدهای موجود در مسیر `gradedFiles` برداشت شده، تست‌های نمره‌دهی اجرا شوند و نمره کارنامه ثبت گردد.

---

## ۵. عیب‌یابی و مدیریت میزکار

- **دکمه ریبوت (Reboot Workspace):** اگر به دلیل یک حلقه بی‌نهایت، پردازنده یا حافظه کانتینر فریز شود، دانشجو یا استاد می‌توانند روی دکمه Reboot کلیک کنند. در این فرایند پروسه‌ها ریستارت می‌شوند اما فایل‌های نوشته‌شده روی دیسک کانتینر حفظ می‌گردند.
- **دسترسی اینترنت (`enableNetworking`):** به صورت پیش‌فرض دسترسی به شبکه برای جلوگیری از کپی‌برداری غیرمجاز قطع است. اگر برای پروژه‌ای نیاز به دانلود پکیج با `pip` یا `apt` دارید، می‌توانید این فلگ را `true` کنید.
