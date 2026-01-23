# Render.com Deployment Guide for YKS Shop

This guide provides step-by-step instructions for deploying your Django project on Render.com.

## 📋 Prerequisites
- ✅ GitHub repository pushed and connected to Render
- ✅ Render.com account created
- ✅ All code committed and pushed to GitHub

---

## 🚀 Render.com Build & Deploy Configuration

### Service Type
**Web Service** (not Static Site)

### Build & Deploy Settings

#### **Build Command**
```bash
pip install -r requirements.txt && python manage.py collectstatic --noinput
```

#### **Start Command**
```bash
gunicorn yksproject.wsgi:application
```

---

## ⚙️ Environment Variables

Add these environment variables in your Render dashboard under **Environment** section:

### Required Core Settings
```
SECRET_KEY=your-secret-key-here-generate-a-new-one
DEBUG=False
```

### Database (Important!)
⚠️ **Note:** SQLite is not suitable for production on Render. Consider using PostgreSQL:
- Go to Render Dashboard → Create New → PostgreSQL
- Copy the Internal Database URL
- Add to environment variables:

```
DATABASE_URL=postgresql://user:password@hostname:port/dbname
```

Or keep SQLite for now (not recommended for production):
```
# Leave empty or remove DATABASE_URL to use SQLite
```

### Cloudinary Settings (for image storage)
Get your credentials from: https://cloudinary.com/console
```
CLOUD_NAME=your-cloudinary-cloud-name
API_KEY=your-cloudinary-api-key
API_SECRET=your-cloudinary-api-secret
# OR use CLOUDINARY_URL instead:
# CLOUDINARY_URL=cloudinary://api_key:api_secret@cloud_name
```

### Email Configuration (Production)
Get your SendGrid API key from: https://app.sendgrid.com/settings/api_keys
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

### Razorpay Payment Gateway
Get your credentials from: https://dashboard.razorpay.com/app/keys
```
RAZORPAY_KEY_ID=your-razorpay-key-id
RAZORPAY_KEY_SECRET=your-razorpay-key-secret
RAZORPAY_ENABLED=true
```

### Twilio (Optional - for WhatsApp notifications)
```
TWILIO_ACCOUNT_SID=your-twilio-account-sid
TWILIO_AUTH_TOKEN=your-twilio-auth-token
TWILIO_WHATSAPP_FROM=+14155238886
```

### Allowed Hosts
Your Render service URL will be automatically added, but ensure your custom domain is in `ALLOWED_HOSTS` in settings.py if you have one.

---

## 📝 Step-by-Step Deployment Instructions

### Step 1: Create Web Service on Render
1. Go to [Render Dashboard](https://dashboard.render.com)
2. Click **"New +"** → **"Web Service"**
3. Connect your GitHub repository
4. Select your repository: `Online-Shopping-Site-yks_shop`

### Step 2: Configure Build Settings
- **Name:** `yksshop` (or your preferred name)
- **Region:** Choose closest to your users
- **Branch:** `main` (or your default branch)
- **Root Directory:** Leave empty (if project is in root) or specify if in subdirectory
- **Runtime:** `Python 3`
- **Build Command:** 
  ```
  pip install -r requirements.txt && python manage.py collectstatic --noinput
  ```
- **Start Command:**
  ```
  gunicorn yksproject.wsgi:application
  ```

### Step 3: Add Environment Variables
Click on **"Environment"** tab and add all the environment variables listed above.

**Important:** Generate a new `SECRET_KEY` for production:
```python
# Run this locally to generate a new secret key:
python -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"
```

### Step 4: Configure Database (Optional but Recommended)
1. Create a PostgreSQL database:
   - Go to **"New +"** → **"PostgreSQL"**
   - Name it (e.g., `yksshop-db`)
   - Copy the **Internal Database URL**
2. Add to environment variables:
   ```
   DATABASE_URL=postgresql://user:password@hostname:port/dbname
   ```
3. Update `settings.py` to use PostgreSQL (see settings update below)

### Step 5: Deploy
1. Click **"Create Web Service"**
2. Render will automatically:
   - Clone your repository
   - Install dependencies
   - Run build command
   - Start the service
3. Monitor the build logs for any errors

---

## 🔧 Required Settings.py Updates for Production

Update your `yksproject/settings.py` for production:

### 1. Update Database Configuration (if using PostgreSQL)
Add this to your `settings.py`:

```python
import dj_database_url

# Database
DATABASES = {
    'default': dj_database_url.config(
        default=os.environ.get('DATABASE_URL'),
        conn_max_age=600,
        conn_health_checks=True,
    )
}

# Fallback to SQLite if DATABASE_URL not set
if not DATABASES['default']:
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.sqlite3',
            'NAME': BASE_DIR / 'db.sqlite3',
        }
    }
```

**Add to requirements.txt:**
```
dj-database-url>=2.0.0
```

### 2. Update Security Settings for Production
In `settings.py`, update these for production:

```python
# Security settings for production
if not DEBUG:
    CSRF_COOKIE_SECURE = True
    SESSION_COOKIE_SECURE = True
    SECURE_SSL_REDIRECT = True
    SECURE_HSTS_SECONDS = 31536000
    SECURE_HSTS_INCLUDE_SUBDOMAINS = True
    SECURE_HSTS_PRELOAD = True
```

### 3. Update ALLOWED_HOSTS
Your Render URL will be something like: `yksshop.onrender.com`
Make sure it's in ALLOWED_HOSTS (it already is in your settings).

---

## 📦 Create Procfile (Optional but Recommended)

Create a `Procfile` in your project root:

```
web: gunicorn yksproject.wsgi:application
```

This makes the start command explicit.

---

## 🔍 Post-Deployment Checklist

After deployment:

1. ✅ **Run Migrations:**
   - Go to Render Shell or use Render's console
   - Run: `python manage.py migrate`

2. ✅ **Create Superuser:**
   - Run: `python manage.py createsuperuser`
   - Follow prompts to create admin account

3. ✅ **Verify Static Files:**
   - Check if static files are loading correctly
   - Visit: `https://your-app.onrender.com/static/`

4. ✅ **Test the Application:**
   - Visit your deployed URL
   - Test registration, login, product browsing
   - Check admin panel

5. ✅ **Monitor Logs:**
   - Check Render logs for any errors
   - Monitor performance

---

## 🐛 Troubleshooting

### Build Fails
- Check build logs in Render dashboard
- Ensure all dependencies are in `requirements.txt`
- Verify Python version compatibility

### Application Crashes
- Check application logs
- Verify all environment variables are set
- Ensure database is accessible (if using PostgreSQL)

### Static Files Not Loading
- Verify `collectstatic` ran successfully
- Check `STATIC_ROOT` and `STATIC_URL` settings
- Ensure WhiteNoise middleware is enabled

### Database Connection Issues
- Verify `DATABASE_URL` is correct
- Check database service is running
- Ensure database credentials are correct

### 500 Internal Server Error
- Check application logs
- Verify `DEBUG=False` in production
- Check `SECRET_KEY` is set
- Verify all required environment variables

---

## 📊 Render Service Specifications

### Recommended Plan
- **Free Tier:** Good for testing (spins down after inactivity)
- **Starter Plan ($7/month):** Better for production (always on)

### Auto-Deploy
- ✅ Enable auto-deploy on push to main branch
- Render will automatically redeploy on each push

---

## 🔐 Security Notes

1. **Never commit `.env` file** - Use Render environment variables
2. **Generate new SECRET_KEY** for production
3. **Use strong database passwords**
4. **Enable HTTPS** (automatic on Render)
5. **Keep dependencies updated**

---

## 📞 Support

If you encounter issues:
1. Check Render documentation: https://render.com/docs
2. Check Django deployment checklist: https://docs.djangoproject.com/en/5.2/howto/deployment/checklist/
3. Review application logs in Render dashboard

---

## ✅ Quick Reference

**Build Command:**
```bash
pip install -r requirements.txt && python manage.py collectstatic --noinput
```

**Start Command:**
```bash
gunicorn yksproject.wsgi:application
```

**Required Environment Variables:**
- `SECRET_KEY`
- `DEBUG=False`
- Cloudinary credentials
- Email settings
- Razorpay credentials

---

**Good luck with your deployment! 🚀**
