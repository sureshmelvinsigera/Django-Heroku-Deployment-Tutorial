# Tutorial: Django + Postgres + Heroku Deployment


## step 01: requirements
```
pip install whitenoise
pip install gunicorn
pip install dj-database-url
pip install django-cors-headers
```
Create two files in the project root.
```
touch Procfile
runtime.txt
```
Here is my current directory structure
```
.
├── Procfile
├── apps
├── manage.py
├── myproject
├── requirements.txt
└── runtime.txt
```
Let's freeze the requirements.txt file
```
pip freeze -> requirements.txt
```
You should see something like this:
```
asgiref==version
dj-database-url==version
Django==version
django-cors-headers==version
django-heroku==version
djangorestframework==version
gunicorn==version
psycopg2==version
pytz==version
sqlparse==version
whitenoise==version
```

## step 02: static assets management and serving
By default, Django does not serve static files in production. Hence, we will use WhiteNoise for serving static assets in production. So let's configure the STATIC-related parameters in settings.py:
```python
PROJECT_ROOT = os.path.dirname(os.path.abspath(__file__))
STATIC_URL = '/static/'
# Extra places for collectstatic to find static files.
STATICFILES_DIRS = (
    os.path.join(PROJECT_ROOT, 'static'),
)
```

## step 03: enable whitenoise
If you’re familiar with Django you’ll know what to do. If you’re just getting started with a new Django project then you’ll need add the following to the bottom of your settings.py file:
```python
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
```
Edit your ```settings.py``` file and add WhiteNoise to the MIDDLEWARE list. The WhiteNoise middleware should be placed directly after the Django SecurityMiddleware (if you are using it) and before all other middleware:
```python
MIDDLEWARE = [
  'django.middleware.security.SecurityMiddleware',
  'whitenoise.middleware.WhiteNoiseMiddleware',
  # ...
]
```

## step 04: edit Heroku required files
Edit the Procfile file in the project root with the following content
```
web: gunicorn <project_name>.wsgi --log-file -
```
Edit the runtime.txt file in the project root with the following content, For example, I'll be using Python 3.6.8
```
python-3.6.8
```
If you like more information about Heroku supported runtime enviroments please visit: https://devcenter.heroku.com/articles/python-support#supported-runtimes


## step 05: database configuration
The following commands create postgresql database on heroku for you app and fetch its url.
```
heroku addons:create heroku-postgresql:hobby-dev
heroku config -s | grep DATABASE_URL
```
We use the dj-database-url library to extract database configurations from the environment.
For Django applications, a Heroku Postgres hobby-dev database is automatically provisioned. This populates the DATABASE_URL environment variable.

To set up the database, we will add the following code in ```settings.py```:
```python
import os
import dj_database_url
```
After the databases setting add the following 
```python
DATABASES = {
    'default': {
            'ENGINE': 'django.db.backends.postgresql_psycopg2',
            'NAME': 'sample123',
            'USER': 'suresh',
            'PASSWORD': '',
            'HOST': 'localhost',
            'PORT': '',
        }
}
# dj-database-url settings
db_from_env = dj_database_url.config()
DATABASES['default'].update(db_from_env)
# Update database configuration with $DATABASE_URL.
db_from_env = dj_database_url.config()
DATABASES['default'].update(db_from_env)
```

## step 06: deployment settings and cors headers
Alright, enough configuration. Let’s get the deployment started. First, let's initialize our gitrepo in the In your project root
```
git init
```
Take a look at what files are ready to be committed to git
```
git status
```
Some of these files we don’t want to track in git. Why? Well, some of them can be created each time you run the program, so it is a waste of space and distracting to check them into git. To handle this, we create a file called .gitignore (like git ignore, but all one word) in the base directory of our project. Then, we write the file or folder names or paths that we want to exclude from git.
```
git add .
git commit -m "Initial commit"
touch .gitignore
```
add the following to the .gitignore file
```
# Byte-compiled / optimized / DLL files
__pycache__/
*.py[cod]
*$py.class

# C extensions
*.so

# Distribution / packaging
.Python
env/
build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
wheels/
*.egg-info/
.installed.cfg
*.egg

# Django stuff:
*.log
local_settings.py

# pyenv
.python-version

# dotenv
.env

# virtualenv
.venv
venv/
ENV/

# mypy
.mypy_cache/

# vscode
.vscode/
```

Now let's add the django cors header to the installed apps
```python
INSTALLED_APPS = [
    ...,
    'django.contrib.staticfiles',
    'corsheaders',
    ...
]
```
You will also need to add a middleware class to listen in on responses:
```python
MIDDLEWARE = [  # Or MIDDLEWARE_CLASSES on Django < 1.10
    ...
    'corsheaders.middleware.CorsMiddleware',
    'django.middleware.common.CommonMiddleware',
    ...
]
```
Add ```CORS_ORIGIN_ALLOW_ALL``` before the ```ROOT_URLCONF```
```
CORS_ORIGIN_ALLOW_ALL = True
ROOT_URLCONF = 'myproject.urls'
```


## step 07: sign up on Heroku and install the Heroku Toolbeit
Heroku actually uses a cli to control their deployments. So let’s install it.
```
brew install heroku
```
Now, we need to “log in” to the local cli. This command will ask for your credentials. When you type in your password, you won’t see it, but it is working. Press enter when you are done.

```
heroku login
heroku create <app_name>
```

## step 08: building the app locally
Run the following command to test Heroku deployment locally. Make sure you're the root of the project.
```
heroku local web
```
This will run the Procfile and consequently you can debug any errors if any on your local machine. You should see an output similar to this.
```
4:30:46 PM web.1 |  [2020-05-12 16:30:46 -0400] [4314] [INFO] Starting gunicorn 20.0.4
4:30:47 PM web.1 |  [2020-05-12 16:30:46 -0400] [4314] [INFO] Listening at: http://0.0.0.0:5000 (4314)
4:30:47 PM web.1 |  [2020-05-12 16:30:46 -0400] [4314] [INFO] Using worker: sync
4:30:47 PM web.1 |  [2020-05-12 16:30:46 -0400] [4317] [INFO] Booting worker with pid: 4317
```
Now visit http://0.0.0.0:5000 and see if you can see the Django application.

## step 09: db migration, commit and collect static
Let's run the db migrations
```
heroku run python manage.py migrate
heroku run python manage.py makemigrations
```
```
heroku run python manage.py createsuperuser
```
```
heroku run python manage.py migrate
```


Sometimes, you may not want Heroku to run collectstatic on your behalf. You can disable the collectstatic build step with the DISABLE_COLLECTSTATIC configuration:
```
heroku config:set DISABLE_COLLECTSTATIC=1
```
## step 10:
Finally, we are now ready to deploy our application. Follow these steps to successfully deploy to Heroku.
Commit all changes to git.
```
git add .
git commit -m "deployment ready"
git push heroku master
heroku open
```




