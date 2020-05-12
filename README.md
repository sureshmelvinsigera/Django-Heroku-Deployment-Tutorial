# Tutorial: Django + Postgres + Heroku Deployment


## Requirements
```
pip install whitenoise
pip install gunicorn
```
create two files in the project root
```
touch Procfile
runtime.txt
```




Heroku actually uses a cli to control their deployments. Let’s download it.
```
brew install heroku
```

Now, we need to “log in” to the local cli. This command will ask for your credentials. When you type in your password, you won’t see it, but it is working. Press enter when you are done.

```
heroku login
```

