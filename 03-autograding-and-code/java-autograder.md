# داوری خودکار سوالات جاوا با JUnit

دروس برنامه‌نویسی شیءگرا، مهندسی نرم‌افزار و طراحی الگوریتم می‌توانند از داور خودکار جاوا مبتنی بر JUnit استفاده کنند.

---

## ۱. نحوه ارزیابی کدهای جاوا

- کامپایل با کامپایلر استاندارد Java (`javac`).
- اجرای تست‌های واحد با فریم‌ورک `JUnit 5`.
- بازخورد تفکیک‌شده شامل تست‌های پاس‌شده، استثناهای پرتاب‌شده (Exceptions) و زمان اجرای هر تست.

---

## ۲. نمونه کلاس تست در جاوا

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

public class StudentSolutionTest {

    @Test
    public void testIsEmpty() {
        MyStack<Integer> stack = new MyStack<>();
        assertTrue(stack.isEmpty(), "پشته در ابتدا باید خالی باشد");
    }

    @Test
    public void testPushAndPop() {
        MyStack<String> stack = new MyStack<>();
        stack.push("الف");
        stack.push("ب");
        assertEquals("ب", stack.pop());
        assertEquals("الف", stack.pop());
    }
}
```

---

## ۳. محافظت از محیط اجرا
- جلوگیری از عملیات پرخطر مانند اجرای دستورات سیستمی (`Runtime.getRuntime().exec`) یا باز کردن پورت‌های شبکه از طریق Java Security Manager.
