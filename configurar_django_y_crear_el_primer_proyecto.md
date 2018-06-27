# Configurar Django y crear el primer proyecto

Una vez que tenemos el virtualenv activado para nuestro entorno en django. Necesitamos instalar django, para esto usamos 'pip'.
Pip ya viene instalado en el virtualenv y es un manejador de paquetes. Los paquetes que instales con pip solo se instalaran en este ambiente.

1. Instalar django con pip
```
	pip install django
```

2. Ahora creamos nuestro primer proyecto de django.
```
	django-admin.py startproject firstproyect
```

3. Esto nos creara los archivos base para nuestro proyecto, ahora necesitamos configurar la db y crear el primer usuario.
```
	cd firstproyect
	python manage.py migrate
	python manage.py createsuperuser
```

4. Ahora ya podes levantar el server y ver que esta todo ok
```
	python manage.py runserver
```

Podemos checkear el server en 127.0.0.1:8000

Aparecera un mensaje que dira:

> It worked!
> Congratulations on your first Django-powered page.  
> Next, start your first app by running python manage.py startapp [app_label].  
> You're seeing this message because you have DEBUG = True in your Django settings file and you haven't configured any URLs. Get to work!
