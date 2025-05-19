# Software-Engineering-Lab-Exp6

# راه‌اندازی پروژه با داکر

در این پروژه از داکر برای اجرای برنامه استفاده شده است. دو فایل اصلی برای راه‌اندازی پروژه وجود دارد:

---

## فایل `Dockerfile`

```Dockerfile
FROM python:3.11
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
CMD ["sh", "-c", "python manage.py migrate && python manage.py runserver 0.0.0.0:8000"]
```

این فایل شامل مراحل زیر است:
- استفاده از تصویر پایه‌ی پایتون نسخه ۳.۱۱
- تعیین دایرکتوری کاری به عنوان `/app`
- کپی کردن تمام فایل‌ها به درون کانتینر
- نصب وابستگی‌ها با استفاده از `requirements.txt`
- اجرای دستورات `migrate` و سپس راه‌اندازی سرور جنگو روی پورت ۸۰۰۰

---

## فایل `docker-compose.yaml`

```yaml
version: '3.9'

services:
  db:
    image: postgres:17
    restart: unless-stopped
    environment:
      - POSTGRES_DB=notes
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=password
    volumes:
      - postgres_data:/var/lib/postgresql/data

  web:
    build: .
    ports:
      - 8000:8000
    depends_on:
      - db
    environment:
      - DB_HOST=db
      - DB_PORT=5432
      - DB_NAME=notes
      - DB_USER=user
      - DB_PASSWORD=password

volumes:
  postgres_data:
```

در این فایل دو سرویس تعریف شده است:

### سرویس `db`:
- استفاده از تصویر `PostgreSQL` نسخه ۱۷
- تعیین متغیرهای محیطی برای ساخت پایگاه داده
- ذخیره اطلاعات پایگاه داده در یک volume به نام `postgres_data`

### سرویس `web`:
- ساخت از روی `Dockerfile` فعلی
- اتصال پورت ۸۰۰۰ کانتینر به پورت ۸۰۰۰ سیستم
- وابستگی به سرویس `db` برای اتصال به پایگاه داده
- تعیین متغیرهای محیطی مربوط به پایگاه داده

---

## اجرای وب سرور

برای اجرای پروژه از دستور زیر استفاده می‌کنیم:

```bash
docker-compose up -d
```

---

## روند اجرا

### ابتدا، داکر شروع به دانلود تصاویر می‌کند:

![Screenshot 2025-05-17 210222](https://github.com/user-attachments/assets/95c9bd93-16f9-4c60-9c14-adebd7ae6aff)


---

### سپس، پس از اتمام دانلود، کانتینرها با موفقیت ساخته و اجرا می‌شوند:

![Screenshot 2025-05-17 223204](https://github.com/user-attachments/assets/4488e5ec-c9df-460c-ab8f-5075d8a6b42f)

![Screenshot 2025-05-17 225405](https://github.com/user-attachments/assets/5503e05a-e330-4567-956c-d739a67eca37)


سپس با اجرای و مشاهده کانتینر web می توان به آن درسترسی داشت و فایل پروژه را به عنوان یک وب سرور استفاده کرد که نشانه درست ران شدن داکر است:

![Screenshot 2025-05-17 225522](https://github.com/user-attachments/assets/225b4fd3-c9b5-47ff-83e4-d016540820a3)

## ارسال درخواست با postman
اپلیکیشن postman را اجرا کرده و سپس در گام اول به شکل زیر یک درخواست POST می‌زنیم و یک کاربر با یوزرنیم user1 و پسورد 1234 می‌سازیم.

![Screenshot 2025-05-19 160531](https://github.com/user-attachments/assets/1563090d-7717-4245-b629-1c2fd7ba0cc4)

حال با یک درخواست POST دیگر لاگین را انجام می‌دهیم.

![Screenshot 2025-05-19 160615](https://github.com/user-attachments/assets/040e9933-e8c8-4527-bf10-cd03b83dc8b1)

مشاهده می‌کنیم که کد 200 یعنی موفقیت آمیز بودن لاگین برگردانده شد، همچنین کوکی‌ها نیز تنظیم شدند.

![Screenshot 2025-05-19 160632](https://github.com/user-attachments/assets/ed00af79-f4b4-45f1-b533-f463b5d3a87c)

حال به وسیله یک ریکوئست POST، یک note با تایتل title1 و بدنه body1 می‌سازیم.

![Screenshot 2025-05-19 160742](https://github.com/user-attachments/assets/2e9512a8-286d-4142-ab30-125ef5533699)

حال به همین شکل یک note با تایتل title2 و بدنه body2 می‌سازیم.

![Screenshot 2025-05-19 160814](https://github.com/user-attachments/assets/ff4bb5d7-6feb-4824-81e6-7d3c9e248c83)

در نهایت نیز لیست note های کاربر را با استفاده از یک ریکوئست GET می‌گیریم و می‌بینیم که دو تا note ای که ساختیم نمایش داده می‌شود.

![Screenshot 2025-05-19 161047](https://github.com/user-attachments/assets/f409528c-f4c4-4410-88d7-97e4401e63d5)
