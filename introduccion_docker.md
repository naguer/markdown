# Introduccion a Docker

Docker es una plataforma que permite desarrollar, deployar y correr aplicaciones en contenedores.

Los contenedores dockers corren nativamente en linux, y comparten su kernel, consumiendo mucho menos memoria que si levantariamos esa misma aplicacion en una maquina virtual (que corre un sistema operativo completo)

Algunas ventajas de los containers son:

- Flexibles: Hasta la más compleja aplicacion puede correr en conteiners
- Livianos: Son sistemas minimos, y comparten el kernel del sistema host.
- Intercambiables: Puedes deployar y correr actualizaciones sin downtime
- Portables: Podes desarrollarlo local, deployarlo en la nube, y correrlo donde sea.
- Escalables: Podes escalar automaticamente y distribuir las replicas

#### Imagenes y contenedores

Un contedor corre una imagen, una imagen es un ejecutable que tiene todo lo necesario para correr una aplicacion (codigo, librerias, variables de entorno y archivos de configuracion)

Un contenedor es una instancia que esta corriendo de una imagen, se pueden ver los containers que estan corriendo simplemente haciendo un 'docker ps'

#### Probar la instalacion

1. Podremos correr una imagen de prueba, al intentar ejecutarla la buscara en nuestro registro local, si no la encuentro ira a Docker Hub (repositorio de imagenes docker)

```
$ docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
9bb5a5d4561a: Pull complete 
Digest: sha256:f5233545e43561214ca4891fd1157e1c3c563316ed8e237750d59bde73361e77
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.
```

2. Listar las imagenes que tenemos en nuestro registro
```
$ docker images 
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              e38bc07ac18e        10 days ago         1.85kB
```

3. Podremos ver el containers, como ya corrio y termino, necesitaremos el argumento "-a"
```
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES

d6c9c1f429cf        hello-world         "/hello"            5 minutes ago       Exited (0) 5 minutes ago                       infallible_pare
```



## Uno nuevo ambiente de desarrollo

Antes si necesitabas correr una aplicacion python, necesitabas un nuevo ambiente vm/fisico con python corriendo y todas las librerias necesarias, que sea igual a produccion.

Con docker simplemente usas una imagen python que no necesita instalarse.
Que ya tiene todas las dependencias que tu aplicacion necesita.
Estas imagenes portables se llaman Dockerfile



#### Definir un contenedor con Dockerfile

Dockerfile es un archivo, un manifiesto que define que habra en tu contenedor, en este archivo tambien podremos definir que puerto estara publico, y que archivos desde el exterior querras copiar al contenedor.



###### Dockerfile

Simplemente tendremos que crear un nuevo archivo en un nuevo directorio de la siguiente manera:



```
# Use an official Python runtime as a parent image
FROM python:2.7-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install --trusted-host pypi.python.org -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py

CAMBIAR
```



#### Construir la app

Una vez que tengamos el archivo Dockerfile creado estamos listos para hacer build y crear nuestra imagen, usaremos el "-t" para etiquetar la imagen.

```
$ docker build -t friendlyhello .
```

Ahora tendremos la imagen creada y la podremos listar

```
$ docker image ls

REPOSITORY            TAG                 IMAGE ID
friendlyhello         latest              326387cea398
```



#### Levantar la app

Podremos levantar la app, y mapear el puerto, la app levanta en el puerto 80, y le diremos que la mapee al 4000, tambien con "-d" (detach) podremos hacer que nos libere la shell

```
docker run -p 4000:80 friendlyhello
```

Podremos ver que la app esta arriba con 

```
docker ps 
```

Y tambien podremos darle stop utilizando su ID

```
docker container stop 1fa4ab2cf395
```

https://docs.docker.com/get-started/part2/#tag-the-image

# Comandos utiles

```
docker build -t friendlyhello .  # Create image using this directory's Dockerfile
docker run -p 4000:80 friendlyhello  # Run "friendlyname" mapping port 4000 to 80
docker run -d -p 4000:80 friendlyhello         # Same thing, but in detached mode
docker container ls                                # List all running containers
docker container ls -a             # List all containers, even those not running
docker container stop <hash>           # Gracefully stop the specified container
docker container kill <hash>         # Force shutdown of the specified container
docker container rm <hash>        # Remove specified container from this machine
docker container rm $(docker container ls -a -q)         # Remove all containers
docker image ls -a                             # List all images on this machine
docker image rm <image id>            # Remove specified image from this machine
docker image rm $(docker image ls -a -q)   # Remove all images from this machine
docker login             # Log in this CLI session using your Docker credentials
docker tag <image> username/repository:tag  # Tag <image> for upload to registry
docker push username/repository:tag            # Upload tagged image to registry
docker run username/repository:tag                   # Run image from a registry
```