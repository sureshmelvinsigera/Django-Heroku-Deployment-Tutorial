# Tutorial: Django + Postgres + Heroku Deployment


## step 01: requirements
```
pip install whitenoise
pip install gunicorn
```
Create two files in the project root.
```
touch Procfile
runtime.txt
```
Let's freeze the requirements.txt file
```
pip freeze -> requirements.txt
```
You should see something like this:
```
dj-database-url==version
Django==version
gunicorn==version
psycopg2==version
psycopg2-binary==version
pytz==version
whitenoise==version
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

## setep 03: set Up the static assets
Configure the STATIC-related parameters in settings.py:
```python
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
STATIC_URL = '/static/'
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, "static")
]
```

## step 02: edit Heroku required files
Edit the Procfile file in the project root with the following content
```
web: gunicorn <project_name>.wsgi --log-file -
```
Edit the runtime.txt file in the project root with the following content, For example, I'll be using Python 3.6.8
```
python-3.6.8
```
If you like more information about Heroku supported runtime enviroments please visit: https://devcenter.heroku.com/articles/python-support#supported-runtimes






## step 02: deployment
Alright, enough configuration. Let’s get the deployment started. First, let's initialize our git repo
```
git init
```
Take a look at what files are ready to be committed to git
```
git status
```
Some of these files we don’t want to track in git. Why? Well, some of them can be created each time you run the program, so it is a waste of space and distracting to check them into git. To handle this, we create a file called .gitignore (like git ignore, but all one word) in the base directory of our project. Then, we write the file or folder names or paths that we want to exclude from git.
```
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



## step 02: sign up on Heroku and install the Heroku Toolbeit
Heroku actually uses a cli to control their deployments. So let’s install it.
```
brew install heroku
```
Now, we need to “log in” to the local cli. This command will ask for your credentials. When you type in your password, you won’t see it, but it is working. Press enter when you are done.

```
heroku login
heroku create <app_name>
```


