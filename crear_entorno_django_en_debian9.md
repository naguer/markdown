# Crear Entorno Django En Debian (9)

La mejor forma de crear el entorno es mediante Virtualenv, esto nos permite tener distintos ambientes django y distintas versiones de python para cada ambiente.

1. Primero instalamos los paquetes  

```
apt install python python3 virtualenv   
```
  
2. Creamos nuestro ambiente virtualenv para el proyecto en python (en este caso django)  
(por defecto usa la version de python que usa debian 9, en este caso python 2.7)  

```
virtualenv -p python3 django-project  
```   
   
   
3. Ingresamos al directorio y activamos el ambiente  
Entre parentesis se vera el nombre del proyecto.  
Podes desactivar el ambiente, tipeando 'deactivate'  

```
django git:(master) ->  cd django-project            
django-project git:(master) ->  source bin/activate
(django-project) -> ~VIRTUAL_ENV git:(master) ->
```  

_Fuente: https://linuxconfig.org/set-up-a-python-django-development-environment-on-debian-9-stretch-linux_
