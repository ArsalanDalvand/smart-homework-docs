# پیکربندی درس و مشخصات ترم

هر درس دارای یک پیکربندی کلان به نام `infoCourse.json` و هر نیم‌سال تحصیلی دارای پیکربندی `infoCourseInstance.json` است.

---

## ۱. فایل پیکربندی درس (`infoCourse.json`)

این فایل در ریشه پوشه درس قرار دارد:

```json
{
  "uuid": "4f5e6d7c-8b9a-1011-1213-141516171819",
  "name": "CE 101",
  "title": "مبانی برنامه‌نویسی و حل مسئله",
  "timezone": "Asia/Tehran",
  "topics": [
    { "name": "متغیرها و عملگرها", "color": "blue1" },
    { "name": "حلقه‌ها و شروط", "color": "green1" },
    { "name": "توابع و بازگشت", "color": "purple1" }
  ],
  "tags": [
    { "name": "ساده", "color": "gray1" },
    { "name": "پروژه", "color": "red1" }
  ]
}
```

---

## ۲. فایل پیکربندی ترم (`infoCourseInstance.json`)

این فایل در مسیر `courseInstances/<ترم>/infoCourseInstance.json` قرار دارد:

```json
{
  "uuid": "9a8b7c6d-5e4f-3210-fedc-ba9876543210",
  "longName": "نیم‌سال اول ۱۴۰۵-۱۴۰۶ (پاییز)",
  "number": "Fa1405",
  "allowAccess": [
    {
      "mode": "Public",
      "role": "Student"
    }
  ]
}
```

### تنظیم قوانین دسترسی (`allowAccess`):
- **`Public`**: هر کاربری که وارد سایت شده باشد می‌تواند با کد دوره عضو شود.
- **`Institution`**: فقط دانشجویان یا اعضای موسسه مربوطه مجاز به عضویت هستند.
- **`startDate` و `endDate`**: مشخص کردن بازه زمانی فعال بودن ترم تحصیلی.
