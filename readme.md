https://www.youtube.com/watch?v=GE0Q8YNKNgs&t=17s
https://github.com/FaztWeb/djangorestframework_crud

Requirements
Python 3.8

installation
python -m pip install --upgrade pip
pip install venv
python -m venv venv
./venv/Scripts/activate
.\venv\Scripts\activate         (wind)
pip install django
pip install djangorestframework


Crear el Proyecto:
django-admin startproject pdrf_crud .   (el punto es para que lo creen dentro de la misma carpeta)
python manage.py runserver              (para correr el proyecto)

Crear una APP:
python manage.py startapp projects
En "pdrf_crud\settings.py" agregar a la lista "INSTALLED_APPS" la nueva APP creada 'projects' y los frameworks 'rest_framework'.
Vuelvo a correr el proyecto con 'python manage.py runserver' solo para saber que sigue funcionando.

Crear el Modelo:
En "projects\models.py" crear una clase, esta clase es el modelo de tabla que se va a crear en la BD.
    # Create your models here.
    class Project(models.Model):
        title = models.CharField(max_length=100)
        description = models.TextField(blank=True)
        technology = models.CharField(max_length=20)
        created_at = models.DateTimeField(auto_now_add=True)

Debemos hacer las migraciones:
python manage.py makemigrations         (crea el codigo para hacer las migraciones)
python manage.py migrate                (hace las migraciones)

Crear el API:
En 'projects' crear 'serializers.py' - serializa los datos
    from rest_framework import serializers
    from .models import Project

    class ProjectSerializer(serializers.ModelSerializer):
    class Meta:
        model = Project
        fields = '__all__'
        read_only_fields = ('created_at',)

En 'projects' crear 'api.py' - los 'viewsets' para poder visualizar los datos
    from projects.models import Project
    from rest_framework import viewsets, permissions
    from .serializers import ProjectSerializer

    class ProjectViewSet(viewsets.ModelViewSet):
        queryset = Project.objects.all()
        permission_classes = [permissions.AllowAny]
        serializer_class = ProjectSerializer

Crearlas rutas de la API:
En 'projects' crear 'urls.py'
    from rest_framework import routers
    from .api import ProjectViewSet

    router = routers.DefaultRouter()        (crear las rutas para un crud)

    router.register('api/projects', ProjectViewSet, 'projects')

    urlpatterns = router.urls               (genera las rutas para un crud)

Vuelvo a correr el proyecto con 'python manage.py runserver' solo para saber que sigue funcionando.

Agregar las URLs en el proyecto 'pdrf_crud/urls.py':
    from django.contrib import admin
    from django.urls import path, include
    from django.conf.urls.static import static
    from django.conf import settings

    urlpatterns = [
        path('admin/', admin.site.urls),
        path('', include('projects.urls')),
    ] + static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)


Vuelvo a correr el proyecto con 'python manage.py runserver' y al entrar al 'http://127.0.0.1:8000' vemos que todo cambio, ya tengo mi crud, y en la url 'http://127.0.0.1:8000/api/projects' tengo el GET y el POST.


Puedo utlizar este API con 'Thunder Client' o con 'Postman'.
el PATCH necesita terminar en '/' - http://localhost:8000/api/projects/3/

HOST FREE 'https://render.com/':
Create a new Web Service

git init
crear un '.gitignore'
    db.sqlite3
    venv
    __pycache__
    staticfiles
git add .
git commit -m "primer commit"

GUIA RENDER:
https://render.com/docs/deploy-django - Go Production-Ready
En 'settings.py' agregar:
    import os
    import dj_database_url
Actualizar:
SECRET_KEY = os.environ.get('SECRET_KEY', default='your secret key')
DEBUG = 'RENDER' not in os.environ
RENDER_EXTERNAL_HOSTNAME = os.environ.get('RENDER_EXTERNAL_HOSTNAME')
if RENDER_EXTERNAL_HOSTNAME:
    ALLOWED_HOSTS.append(RENDER_EXTERNAL_HOSTNAME)

New PostgreSQL - Agregar la BD en render:
name y database 'pdrf_crud'

por consola ejecuto:
pip install dj-database-url psycopg2-binary

importo 'dj-database-url'

lo agrego en:
DATABASES = {
    'default': dj_database_url.config(
        # Feel free to alter this value to suit your needs.
        default='sqlite:///db.sqlite3',
        conn_max_age=600
    )
}

Static Files:
pip install whitenoise[brotli]
Agregar:
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',    ...
]
Agregar:
    if not DEBUG:
        STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
        STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'

Create a Build Script:
    #!/usr/bin/env bash
    # exit on error
    set -o errexit

    pip install -r requirements.txt

    python manage.py collectstatic --no-input
    python manage.py migrate

Crear el 'requirements.txt':
    pip freeze > requirements.txt

En la consola 'git bash':
    chmod a+x build.sh

Instalar 'gunicorn' en la consola normal:
    pip install gunicorn
Volver a crear el 'requirements.txt', para agregarlo:
    pip freeze > requirements.txt

Cear repositorio en github:
    git remote add origin https://github.com/jav2074/python-django-rest-framework-crud.git
    git push -u origin main

En 'https://github.com/settings/installations' - Applications
en Render.com:
name: ''
Build Command: './buidl.sh'
Start Command: 'gunicorn pdrf_crud.wsgi'

en Enviroment, crear las variables de entorno:
DATABASE_URL	'Internal Database URL' generada en la BD
SECRET_KEY
PYTHON_VERSION  '3.8.2'