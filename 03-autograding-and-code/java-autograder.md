# داوری خودکار کدهای جاوا با JUnit 5

داوری کدهای جاوا از طریق ایمیج `prairielearn/grader-java` و فریم‌ورک استاندارد JUnit 5 انجام می‌شود.

---

## ۱. معماری امنیتی و ایزولاسیون مدرن JVM

در سامانه‌های مدرن، به جای استفاده از قابلیت منسوخ‌شده Java Security Manager، امنیت کانتینر ارزیابی جاوا از طریق لایه‌های زیر تضمین می‌شود:
- **کاربر بی‌اختیار سیستم (`sbuser` با UID 1001):** پروسه جاوا تحت یک کاربر فاقد دسترسی ریشه در دایرکتوری محدود `/home/sbuser` اجرا می‌شود.
- **مسدودسازی کامل Native Access و JNI:** دانشجو نمی‌تواند با فراخوانی کتابخانه‌های محلی C دستورات سیستمی خارج از سندباکس اجرا کند.
- **سقف‌گذاری حافظه JVM با `JDK_JAVA_OPTIONS`:** برای جلوگیری از حملات Fork Bomb و خفگی سرور بر اثر خطای OutOfMemoryError، سقف حافظه هیپ (Heap) به `256m` یا `512m` محدود می‌گردد.

---

## ۲. ساختار پوشه‌ها و تنظیمات `info.json`

```text
questions/java_binary_tree/
├── info.json
├── question.html
└── tests/
    └── junit/
        └── BinaryTreeTest.java
```

```json
{
  "uuid": "2a4b8c91-12df-47ab-98e1-456789abcdef",
  "title": "پیاده‌سازی درخت دودویی جستجو در جاوا",
  "type": "v3",
  "singleVariant": true,
  "gradingMethod": "External",
  "externalGradingOptions": {
    "image": "prairielearn/grader-java",
    "entrypoint": "/grader/run.sh",
    "timeout": 20,
    "enableNetworking": false
  }
}
```

---

## ۳. نگارش آزمون‌های JUnit 5 با تگ‌های بارم‌بندی

```java
package tests.junit;

import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Tag;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.TestReporter;
import static org.junit.jupiter.api.Assertions.*;

import student.BinarySearchTree;

public class BinaryTreeTest {

    @Test
    @Tag("points=5")
    @DisplayName("تست ۱: درج و جستجوی عناصر پایه")
    public void testInsertAndSearch(TestReporter reporter) {
        BinarySearchTree bst = new BinarySearchTree();
        bst.insert(50);
        bst.insert(30);
        bst.insert(70);

        assertTrue(bst.contains(30), "عنصر ۳۰ باید در درخت یافت شود.");
        assertFalse(bst.contains(99), "عنصر ۹۹ نباید در درخت باشد.");
        
        reporter.publishEntry("بازخورد", "درج و جستجوی عناصر با موفقیت تایید شد.");
    }

    @Test
    @Tag("points=5")
    @DisplayName("تست ۲: محاسبه ارتفاع درخت و حالت‌های مرزی")
    public void testTreeHeight() {
        BinarySearchTree bst = new BinarySearchTree();
        assertEquals(0, bst.height(), "ارتفاع درخت خالی باید صفر باشد.");
        
        bst.insert(10);
        bst.insert(20);
        bst.insert(30);
        assertEquals(3, bst.height(), "خطا در محاسبه ارتفاع درخت نامتوازن.");
    }
}
```

### نکات کلیدی نمره‌دهی جاوا:
1. **انوتیشن `@Tag("points=X")`:** موتور ارزیابی با اسکن این تگ، بارم هر متد تست را مشخص کرده و در کارنامه دانشجو لحاظ می‌کند.
2. **کلاس `TestReporter`:** با فراخوانی `publishEntry`، پیام‌ها و راهنمایی‌های تشخیصی مستقیماً در کارنامه آزمون برای دانشجو چاپ می‌شوند.
3. **متغیرهای محیطی جاوا:** امکان تنظیم `--release 17` و `-Xmx256m` در تنظیمات گرایدر وجود دارد تا کامپایلر دقیقاً مطابق نسخه درسی دانشگاه رفتار کند.
