Pasos para crear una instancia de Django con postgres y con react usando docker-compose

1. Crear las carpetas respectivas backend, frontend, db y compose

2. Al crear la app de django con django-admin (la cli de django) se vincula con una db sqlite. Se quiere usar postgres como db. Esta db está ejecutándose en otro container, entonces hacemos lo siguiente:

NOTA: No olvidar el archivo .env con las variables de entorno

3. Dentro del directorio compose ejecutamos primeramente este comando: `docker compose run backend django-admin startproject backend .` Lo que hace es `compose` construye la imagen a partir del directorio indicado: `build: context: ../backend`. Una vez que la imagen ha sido construida Compose ejecuta el contenedor y a su vez el comando `django-admin startproject backend`. Esto genera los archivos de Django necesarios para ejecutar la aplicación

4. Como estamos usando volúmenes, se crea uno llamado backend con el archivo setting.py adentro. Abrimos este archivo ubicado en la carpeta backend/backend/settings.py y buscamos la lista DATABASES  = [...] y lo reemplazamos con:
`DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': os.getenv('POSTGRES_DB'),
        'USER': os.getenv('POSTGRES_USER'),
        'PASSWORD': os.getenv('POSTGRES_PASSWORD'),
        'HOST': 'db',
        'PORT': 5432,
    }
}`
Además como estamos usando variables de entorno, entonces al inicio de este archivo escribimos:
`import os
from dotenv import load_dotenv
load_dotenv()
`
Guardamos el archivo y ejecutamos `docker compose up`

5. Ahora se debe instalar django-cors-headers: Eso lo hariamos ingresando a docker con el siguiente comando 'docker compose exec backend sh'una vez dentro corremos el siguiente comando 'pip install django-cors-headers' y también instalaremos coreapi con el siguiente comando 'pip install coreapi'


Para finalizar la instalación de Django con Postres ejecutamos: `docker exec compose-backend-1 python manage.py migrate` Esto debe crear las tablas de Django en la base de datos

Troubleshooting:

CommandError: /app/manage.py already exists. Overlaying a project into an existing directory won't replace conflicting files. Este error se refire a que los archivos de una instalación previa están todavía disponibles. Es necesario removerlos para eliminar este error.
6. Toda la documentacion de la API se encuentra en la ruta 'http://localhost:8000/denuncia/docs/', se encuentra documentado con coreapi. 