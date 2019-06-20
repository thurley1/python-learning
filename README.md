# Python with Django

- Tutorial: https://scotch.io/tutorials/build-your-first-python-and-django-application

## Environment set-up
- install python 3.7.3
- from shell - `python` enters interactive python shell - cntl-z enter to exit
- install virtualenv - `pip install virtualenv`
```To avoid polluting our global scope with unecessary packages, we are going to use a virtual environment to store our packages```
- Create an environment: `virtualenv env`
- activate the environment `source env/bin/activate` (linux) or `env\Scripts\activate` (windows)
- Install Django - `pip install django`
- Create skeleton project - `django-admin startproject helloapp`

## First Application
- `settings.py` - project settings
    - `INSTALLED_APPS` - default apps come pre-installed. Any third pary apps or apps we create go at the bottom of this list
```Django apps follow the Model, View, Template paradigm. In a nutshell, the app gets data from a model, the view does something to the data and then renders a template containing the processed information. As such, Django templates correspond to views in traditional MVC and Django views can be likened to the controllers found in traditional MVC```
- create an app - `python manage.py startapp howdy`
- add the app name to the `Installed Apps` list in our `settings.py` file
- Django comes with a built in lightweight web server
- run the server: `python manage.py runserver`
- access via `http://127.0.0.1:8000/`

## Migrations
- Django comes with some migrations already created for its default apps
- Apply the migrations - `python manage.py migrate`

## Urls and Templates
- We need Django to access our `howdy` app when someone goes to the home page URL which is `/`
- Edit the `urls.py` file in the INNER `helloapp` folder
- add the following at the end of the `urlpatterns` collection - `url(r'^', include('howdy.urls'))`
- create a file named `urls.py` in the howdy folder and fill it with:

``` python
from django.conf.urls import url
from howdy import views

urlpatterns = [
    url(r'^$', views.HomePageView.as_view()),
]
```

- create a view in the `views.py` file in the `howdy`folder
- create `templates` folder in the `howdy` app
- create `index.html` in the tempaltes folder
- use this content:
``` html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>Howdy!</title>
    </head>
    <body>
        <h1>Howdy! I am Learning Django!</h1>
    </body>
</html>
```

## Linking Pages
- Create a new template - `about.html`
- Code for about page:
``` html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>Howdy!</title>
    </head>
    <body>
        <h1>Welcome to the about page</h1>
    <p>
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nunc quis neque ex. Donec feugiat egestas dictum. In eget erat sit amet elit pellentesque convallis nec vitae turpis. Vivamus et molestie nisl. Aenean non suscipit velit. Nunc eleifend convallis consectetur. Phasellus ornare dolor eu mi vestibulum, ornare tempus lacus imperdiet. Interdum et malesuada fames ac ante ipsum primis in faucibus. Pellentesque ut sem ligula. Mauris volutpat vestibulum dui in cursus. Donec aliquam orci pellentesque, interdum neque sit amet, vulputate purus. Quisque volutpat cursus nisl, in volutpat velit lacinia id. Maecenas id felis diam.    
    </p>
    <a href="/">Go back home</a>
    </body>
</html>
```
- add link on `index.html` template - `<a href="/about/">About Me</a>`
- define the url in the `urls.py` file - `url(r'^about/$', views.AboutPageView.as_view()),`
- edit `views.py` to add the view for `about.html` 
``` python
class AboutPageView(TemplateView):
    template_name = "about.html"
```
