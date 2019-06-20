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

## REST API with Django - Test Driven

- Tutorial: https://scotch.io/tutorials/build-a-rest-api-with-django-a-test-driven-approach-part-1
- Use TDD to create a Bucketlist API with CRUD operations
- Django Rest Framework (or simply DRF) is a powerful module for building web APIs. It's very easy to build model-backed APIs that have authentication policies and are browsable.

## Setting up
- create project folder
- Create an environment: `virtualenv venv`
- activate the environment `source venv/bin/activate` (linux) or `venv\Scripts\activate` (windows)
- to track requirements - `touch requirements.txt `
- add installed requirements - `pip freeze > requirements.txt`
- create the Django project - `django-admin startproject djangorest`
- install DRF - `pip install djangorestframework`
- add `rest_framework` into our `settings.py`
``` python
INSTALLED_APPS = [
...,
'rest_framework'
]
```

## Creating the REST Api App
- In Django, we can create multiple apps that integrate to form one application. An app in django is simply a python package with a bunch of files including the `__init__.py` file
- From the `djangorest` directory - `python manage.py startapp api`
    - `startapp` creates a new app
    - `api` is the name of the app
- To integrate the `api` app with the `djangorest` main app, add it to our djangorest `settings.py` - add `api` to `INSTALLED_APPS`

## Tests
- Example test class:
``` python
from django.test import TestCase
from .models import Bucketlist


class ModelTestCase(TestCase):
    """This class defines the test suite for the bucketlist model."""

    def setUp(self):
        """Define the test client and other test variables."""
        self.bucketlist_name = "Write world class code"
        self.bucketlist = Bucketlist(name=self.bucketlist_name)

    def test_model_can_create_a_bucketlist(self):
        """Test the bucketlist model can create a bucketlist."""
        old_count = Bucketlist.objects.count()
        self.bucketlist.save()
        new_count = Bucketlist.objects.count()
        self.assertNotEqual(old_count, new_count)
```
- create a BucketList model in `models.py`
- run the test - `python manage.py test`
- add fields to the model - `models.py`
- run migration to propogate model changes to the database schema (using sqlLite - which comes bundled)
    - create the migration - `python manage.py makemigrations`
    - migrate - `python manage.py migrate`
- Tests now pass

## Serializers
- The `ModelSerializer` class lets you automatically create a Serializer class with fields that correspond to the Model fields
- Example:
``` python
# api/serializers.py

from rest_framework import serializers
from .models import Bucketlist


class BucketlistSerializer(serializers.ModelSerializer):
    """Serializer to map the Model instance into JSON format."""

    class Meta:
        """Meta class to map serializer's fields with the model fields."""
        model = Bucketlist
        fields = ('id', 'name', 'date_created', 'date_modified')
        read_only_fields = ('date_created', 'date_modified')
```

## Views
- Example test class for testing views:
``` python
from rest_framework.test import APIClient
from rest_framework import status
from django.core.urlresolvers import reverse

...

class ViewTestCase(TestCase):
    """Test suite for the api views."""

    def setUp(self):
        """Define the test client and other test variables."""
        self.client = APIClient()
        self.bucketlist_data = {'name': 'Go to Ibiza'}
        self.response = self.client.post(
            reverse('create'),
            self.bucketlist_data,
            format="json")

    def test_api_can_create_a_bucketlist(self):
        """Test the api has bucket creation capability."""
        self.assertEqual(self.response.status_code, status.HTTP_201_CREATED)
```
- create the view in `views.py`
``` python
from rest_framework import generics
from .serializers import BucketlistSerializer
from .models import Bucketlist


class CreateView(generics.ListCreateAPIView):
    """This class defines the create behavior of our rest api."""
    queryset = Bucketlist.objects.all()
    serializer_class = BucketlistSerializer

    def perform_create(self, serializer):
        """Save the post data when creating a new bucketlist."""
        serializer.save()
```
- The `ListCreateAPIView` is a generic view which provides `GET` (list all) and `POST` method handlers

## URLs
- create `urls.py`
- Example:
``` python
from django.conf.urls import url, include
from rest_framework.urlpatterns import format_suffix_patterns
from .views import CreateView

urlpatterns = {
    url(r'^bucketlists/$', CreateView.as_view(), name="create"),
}

urlpatterns = format_suffix_patterns(urlpatterns)
```
- The `format_suffix_pattern` allows us to specify the data format (raw json or even html) when we use the URLs
- add a url to the main app's `api.urls` - 
``` python
from django.conf.urls import url, include

urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^', include('api.urls')) # Add this line
]
```

# Reading, Updating, Deleting
- Add tests:
``` python
def test_api_can_get_a_bucketlist(self):
        """Test the api can get a given bucketlist."""
        bucketlist = Bucketlist.objects.get()
        response = self.client.get(
            reverse('details',
            kwargs={'pk': bucketlist.id}), format="json")

        self.assertEqual(response.status_code, status.HTTP_200_OK)
        self.assertContains(response, bucketlist)

    def test_api_can_update_bucketlist(self):
        """Test the api can update a given bucketlist."""
        change_bucketlist = {'name': 'Something new'}
        res = self.client.put(
            reverse('details', kwargs={'pk': bucketlist.id}),
            change_bucketlist, format='json'
        )
        self.assertEqual(res.status_code, status.HTTP_200_OK)

    def test_api_can_delete_bucketlist(self):
        """Test the api can delete a bucketlist."""
        bucketlist = Bucketlist.objects.get()
        response = self.client.delete(
            reverse('details', kwargs={'pk': bucketlist.id}),
            format='json',
            follow=True)

        self.assertEquals(response.status_code, status.HTTP_204_NO_CONTENT)
```

- Add Views:
``` python
class DetailsView(generics.RetrieveUpdateDestroyAPIView):
    """This class handles the http GET, PUT and DELETE requests."""

    queryset = Bucketlist.objects.all()
    serializer_class = BucketlistSerializer
```

- `RetrieveUpdateDestroyAPIView` is a generic view that provides GET(one), PUT, PATCH and DELETE method handlers
- create this new url to be associated with our DetailsView in `api\urls.py`
``` python
url(r'^bucketlists/(?P<pk>[0-9]+)/$',
        DetailsView.as_view(), name="details"),
```