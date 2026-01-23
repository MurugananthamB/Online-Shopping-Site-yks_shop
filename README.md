# YKS Shop

Modern online shopping platform built with Django. Simple to set up, easy to extend, and ready for custom themes, payment flows, and order management.

## Highlights
- Clean, user-friendly shopping experience
- Cart, checkout, and order flow
- Admin-friendly product and inventory management
- Django templating ready for custom UI

## Tech Stack
- Backend: Django, Python
- Database: SQLite (default)
- Frontend: HTML, CSS, JavaScript

## Quick Start
```bash
python -m venv venv
source venv/Scripts/activate
pip install -r requirements.txt
python manage.py migrate
python manage.py runserver
```

Open the app at `http://127.0.0.1:8000/`.

## How to Run the Project
1. Create and activate a virtual environment.
2. Install dependencies from `requirements.txt`.
3. Run database migrations.
4. Start the development server.

Example:
```bash
python -m venv venv
source venv/Scripts/activate
pip install -r requirements.txt
python manage.py makemigrations
python manage.py migrate
python manage.py runserver
```

Optional (admin access):
```bash
python manage.py createsuperuser
```

Then visit:
- App: `http://127.0.0.1:8000/`
- Admin: `http://127.0.0.1:8000/admin/`

## Common Commands
```bash
python manage.py createsuperuser
python manage.py makemigrations
python manage.py migrate
```

## Project Structure
- `yksproject/` Django project settings
- `yksshop/` Main app
- `yksshop/templates/` Templates
- `staticfiles/` Static assets

## Configuration Notes
- Update settings in `yksproject/settings.py`
- Use `DEBUG=False` for production
- Configure database and static files for deployment

## Deployment on Render

### Quick Setup
1. Push your code to GitHub
2. Connect your repository to Render
3. Render will automatically detect `render.yaml` and use the build script

### Manual Configuration (if not using render.yaml)
If you're configuring manually in the Render dashboard:

**Build Command:**
```bash
./build.sh
```

Or if build.sh doesn't work, use:
```bash
pip install -r requirements.txt && python manage.py migrate --noinput && python manage.py collectstatic --noinput --clear
```

**Start Command:**
```bash
gunicorn yksproject.wsgi:application
```

### Important Notes
- The `build.sh` script automatically runs migrations during deployment
- Ensure all environment variables are set in the Render dashboard
- The build script will fail if migrations have errors, preventing broken deployments

### Troubleshooting
If you see "no such table" errors:
1. Check that the build command includes `python manage.py migrate`
2. Verify migrations are in the `yksshop/migrations/` directory
3. Check Render build logs to ensure migrations ran successfully

## Contributing
Pull requests are welcome. For major changes, open an issue first to discuss the proposal.

## License
Add your license information here.
