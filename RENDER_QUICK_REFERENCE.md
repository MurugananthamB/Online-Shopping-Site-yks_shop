# 🚀 Render.com Deployment - Quick Reference Sheet

## Build & Deploy Configuration

### Build Command
```
pip install -r requirements.txt && python manage.py collectstatic --noinput
```

### Start Command
```
gunicorn yksproject.wsgi:application
```

---

## Essential Environment Variables

Copy and paste these into Render's Environment Variables section:

### Core Settings
```
SECRET_KEY=your-generated-secret-key-here
DEBUG=False
```

### Cloudinary (Image Storage)
Get credentials from: https://cloudinary.com/console
```
CLOUD_NAME=your-cloudinary-cloud-name
API_KEY=your-cloudinary-api-key
API_SECRET=your-cloudinary-api-secret
```

### Email (Production)
Get SendGrid API key from: https://app.sendgrid.com/settings/api_keys
```
EMAIL_BACKEND=django.core.mail.backends.smtp.EmailBackend
EMAIL_HOST=smtp.sendgrid.net
EMAIL_PORT=587
EMAIL_USE_TLS=True
EMAIL_USE_SSL=False
EMAIL_HOST_USER=apikey
EMAIL_HOST_PASSWORD=your-sendgrid-api-key-here
DEFAULT_FROM_EMAIL=your-email@example.com
EMAIL_TIMEOUT=10
```

### Razorpay
Get credentials from: https://dashboard.razorpay.com/app/keys
```
RAZORPAY_KEY_ID=your-razorpay-key-id
RAZORPAY_KEY_SECRET=your-razorpay-key-secret
RAZORPAY_ENABLED=true
```

### Database (Optional - PostgreSQL)
If you create a PostgreSQL database on Render, add:
```
DATABASE_URL=postgresql://user:password@hostname:port/dbname
```
*(Render will provide this URL when you create the database)*

---

## Deployment Steps Summary

1. ✅ **Create Web Service** on Render
2. ✅ **Connect GitHub Repository**
3. ✅ **Set Build Command:** `pip install -r requirements.txt && python manage.py collectstatic --noinput`
4. ✅ **Set Start Command:** `gunicorn yksproject.wsgi:application`
5. ✅ **Add Environment Variables** (listed above)
6. ✅ **Deploy**
7. ✅ **Run Migrations:** Use Render Shell → `python manage.py migrate`
8. ✅ **Create Superuser:** `python manage.py createsuperuser`

---

## Generate New SECRET_KEY

Run this locally to generate a secure secret key:
```bash
python -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"
```

Copy the output and use it as your `SECRET_KEY` environment variable.

---

## Service Type
**Web Service** (not Static Site)

## Runtime
**Python 3**

---

📖 **For detailed instructions, see:** `RENDER_DEPLOYMENT.md`
