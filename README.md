## Dependencies
python3
pip3
pip3 install pipenv
pipenv shell
pipenv install django djangorestframework django-rest-knox
node (npm)

## IDE
VS code
CTRL + SHIFT + P
Python select interpreter [project-name ...]: pipenv

## Setup
django-admin startproject [project name]
cd [project name]
python3 manage.py startapp [app name]

## Settings
cd [project name]
vim settings.py
  INSTALLED_APPS = [
    ...
    'app name',
    'rest_framework',
    'frontend'
  ]
:wq

## Models
cd .. / [app name]
vim models.py
  from django.db import models
  class ModelName(models.Model):
    name = models.CharField(max_length=100)
:wq

## Migrations
python3 manage.py makemigrations [app name]
python3 manage.py migrate

## Serializers
vim serializers.py
  from rest_framework import serializers
  from [modelname].models import [ModelName]

  class ModelNameSerializer(serializers.ModelSerializer):
    class Meta:
      model = [ModelName]
      fields = '__all__'
:wq

## API
vim api.py
  from [modelname].models import ModelName
  from rest_framework import viewsets, permissions
  from .serializers import ModelNameSerializer

  class ModelNameViewSet(viewsets.ModelViewSet):
    queryset = [ModelName].objects.all()
    permission_classes = [
      permissions.AllowAny
    ]
    serializer_class = ModelNameSerializer
:wq


## URLs
vim urls.py
  from rest_framework import routers
  from .api import ModelNameViewSet
  router = routers.DefaultRouter()
  router.register('api/endpoint', ModelNameViewSet, 'modelname')
  urlpatterns = router.urls
:wq
cd .. / [project name]
vim urls.py
  from django.urls import path, include
  urlpatterns = [
    path('', include('frontend.urls')),
    path('', include('modelname.urls')),
  ]
:wq
    
## Run the server
python3 manage.py runserver
 

## Prepare React frontend
pipenv shell
python3 manage.py startapp frontend
mkdir -p ./frontend/src/components
mkdir -p ./frontend/{static,templates}/frontend

## install js dependencies
cd ..
npm init -y
npm i -D webpack webpack-cli
npm i -D @babel/core babel-loader @babel/preset-env @babel/preset-react babel-plugin-transform-class-properties
npm i react react-dom prop-types redux react-redux redux-thunk redux-devtools-extension axios

vim .babelrc
  {
    "presets": ["@babel/preset-env", "@babel/preset-react"],
    "plugins": ["transform-class-properties"]
  }
:wq

vim webpack.config.js
  module.exports = {
    module: {
      rules: [
        {
          test: /\.js$/,
          exclude: /node_modules/,
          use: {
            loader: "babel-loader"
          }
        }
      ]
    }
  }
:wq

vim package.json
  "scripts": {
    "dev": "webpack --mode development --watch ./[project name]/frontend/src/index.js --output [project name]/frontend/static/frontend/main.js",
    "build": "webpack --mode production ./[project name]/frontend/src/index.js --output [project name]/frontend/static/frontend/main.js",
  }
:wq

touch frontend/src/index.js  # hook App component to root HTML element
vim templates/frontend/index.html
  {% load static %}
  <script src="{% static "frontend/main.js" %}"></script>
:wq

vim frontend/views.py
  from django.shortcuts import render
  def index(request):
    return render(request, 'frontend/index.html')
:wq

vim frontend/urls.py
  from django.urls import path
  from . imports views
  urlpatterns = [
    path('', views.index)
  ]
:wq

npm run dev
