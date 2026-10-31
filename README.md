## Production Deployment

This guide explains how to deploy the Weather API on a Linux server using Docker Compose and a prebuilt Docker image from GitHub Container Registry (GHCR).

### Prerequisites

- Docker Engine
- Docker Compose v2
- Access to the private GHCR package
- A valid GitHub Personal Access Token (PAT) with `read:packages` permission

### 1. Create the deployment directory

```bash
sudo mkdir -p /opt/weather-api
sudo chown "$USER":"$(id -gn)" /opt/weather-api
cd /opt/weather-api
```

### 2. Download the Compose files

Download the following files from the repository's `main` branch:

- `docker-compose.yaml`
- `docker-compose.prod.yaml`

Place both files in `/opt/weather-api`.

The server does not need the application source code.

### 3. Configure environment variables

Create `/opt/weather-api/.env.prod` and configure the required production environment variables, including database credentials, application secrets, and Celery connection settings.

Do not commit `.env.prod` or expose its contents publicly.

Restrict access to the file:

```bash
chmod 600 .env.prod
```

### 4. Authenticate with GHCR

```bash
docker login ghcr.io
```

Enter your GitHub username and a PAT with `read:packages` permission when prompted.

### 5. Pull the application image

```bash
docker pull ghcr.io/mhm-forghani/weather-api:latest
```

### 6. Start the production services

```bash
docker compose --env-file .env.prod -f docker-compose.yaml -f docker-compose.prod.yaml up -d
```

Docker Compose starts the services defined in the Compose files, including the API, PostgreSQL, Redis, Celery workers, and Celery Beat.

### 7. Verify the deployment

Check the status of the services:

```bash
docker compose --env-file .env.prod -f docker-compose.yaml -f docker-compose.prod.yaml ps
```

View application logs:

```bash
docker compose --env-file .env.prod -f docker-compose.yaml -f docker-compose.prod.yaml logs --tail=100 app
```

The API is exposed on port 8000 by default. If access is configured, the interactive API documentation is available at `/docs`.

### Updating the deployment

After a new image is published to GHCR, pull the updated image and recreate the services:

```bash
docker compose --env-file .env.prod -f docker-compose.yaml -f docker-compose.prod.yaml pull
docker compose --env-file .env.prod -f docker-compose.yaml -f docker-compose.prod.yaml up -d
```

Review the service status and logs after each update.


## استقرار در محیط Production

این راهنما نحوه استقرار Weather API روی یک سرور لینوکسی را با استفاده از Docker Compose و یک Image آماده از GitHub Container Registry (GHCR) توضیح می‌دهد.

### پیش‌نیازها

- نصب Docker Engine
- نصب Docker Compose نسخه ۲
- دسترسی مجاز به Package خصوصی در GHCR
- یک GitHub Personal Access Token (PAT) معتبر با مجوز `read:packages`

### ۱. ایجاد پوشه استقرار

```bash
sudo mkdir -p /opt/weather-api
sudo chown "$USER":"$(id -gn)" /opt/weather-api
cd /opt/weather-api
```

این دستورات پوشه استقرار را ایجاد می‌کنند، مالکیت آن را به کاربر فعلی می‌دهند و وارد آن می‌شوند.

### ۲. دریافت فایل‌های Compose

فایل‌های زیر را از شاخه `main` مخزن دریافت کنید:

- `docker-compose.yaml`
- `docker-compose.prod.yaml`

هر دو فایل را در مسیر `/opt/weather-api` قرار دهید.

**توجه:** برای استقرار، نیازی به دریافت سورس کد برنامه روی سرور نیست.

### ۳. تنظیم متغیرهای محیطی

فایل `/opt/weather-api/.env.prod` را ایجاد کنید و متغیرهای موردنیاز محیط Production را در آن تنظیم کنید؛ از جمله اطلاعات اتصال پایگاه داده، کلیدهای محرمانه برنامه و تنظیمات اتصال Celery.

محتوای این فایل را در مخزن عمومی قرار ندهید و آن را منتشر نکنید.

برای محدودکردن دسترسی به فایل، دستور زیر را اجرا کنید:

```bash
chmod 600 .env.prod
```

این دستور دسترسی خواندن و نوشتن را به مالک فایل محدود می‌کند.

### ۴. ورود به GHCR

```bash
docker login ghcr.io
```

نام کاربری GitHub و یک PAT دارای مجوز `read:packages` را وارد کنید.

این مرحله برای دریافت Image از Package خصوصی ضروری است.

### ۵. دریافت Image برنامه

```bash
docker pull ghcr.io/mhm-forghani/weather-api:latest
```

این دستور Image منتشرشده برنامه را از GHCR دریافت و در فضای ذخیره‌سازی Docker نگهداری می‌کند.

### ۶. راه‌اندازی سرویس‌های Production

```bash
docker compose --env-file .env.prod -f docker-compose.yaml -f docker-compose.prod.yaml up -d
```

این دستور سرویس‌های تعریف‌شده در فایل‌های Compose را راه‌اندازی می‌کند؛ از جمله API، PostgreSQL، Redis، Workerهای Celery و Celery Beat.

### ۷. بررسی وضعیت استقرار

برای مشاهده وضعیت سرویس‌ها:

```bash
docker compose --env-file .env.prod -f docker-compose.yaml -f docker-compose.prod.yaml ps
```

این دستور وضعیت سرویس‌ها و کانتینرهای مرتبط را نمایش می‌دهد.

برای مشاهده ۱۰۰ خط آخر لاگ سرویس API:

```bash
docker compose --env-file .env.prod -f docker-compose.yaml -f docker-compose.prod.yaml logs --tail=100 app
```

این دستور به بررسی خطاهای احتمالی هنگام راه‌اندازی API کمک می‌کند.

API به‌طور پیش‌فرض روی پورت ۸۰۰۰ در دسترس است. در صورت تنظیم دسترسی مناسب، مستندات تعاملی API از مسیر `/docs` قابل مشاهده خواهد بود.

### به‌روزرسانی استقرار

پس از انتشار نسخه جدید Image در GHCR، دستورات زیر را اجرا کنید:

```bash
docker compose --env-file .env.prod -f docker-compose.yaml -f docker-compose.prod.yaml pull
docker compose --env-file .env.prod -f docker-compose.yaml -f docker-compose.prod.yaml up -d
```

دستور اول Imageهای به‌روز سرویس‌ها را دریافت می‌کند. دستور دوم سرویس‌ها را با Imageهای دریافت‌شده ایجاد یا به‌روزرسانی می‌کند.

پس از به‌روزرسانی، وضعیت سرویس‌ها و لاگ‌ها را بررسی کنید.
Nginx is used as a reverse proxy in the production deployment.
