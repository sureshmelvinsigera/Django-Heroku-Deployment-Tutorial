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

# cover
cover/
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


