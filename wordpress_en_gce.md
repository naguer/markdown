Primero que nada instalamos gcloud



Una vez instalado ya podemos autenticar contra nuestra cuenta de google

```
gcloud init
```

Configuramos los valores por defecto, y verificamos.

```
$ gcloud config set project [PROJECT_ID]
$ gcloud config set compute/zone us-central1-c
$ gcloud config set compute/region us-central1
$ gcloud config list 
```

Configuramos variables

```
$ export CLUSTER_NAME="wordpress"
$ export ZONE="us-central1-c"
$ export REGION="us-central1"
```

Creamos el cluster de kubernetes, esto puede tardar varios minutos.

```
$ gcloud container clusters create wordpress-cluster --num-nodes 1
```

Ahora lo configuramos como el cluster default

```
$ gcloud config set container/cluster wordpress-cluster
```



Si queremos limpiar todo rapidamente se puede hacer de la siguiente forma

```
kubectl delete deployment,service,pvc --all
kubectl delete secret mysql-pass
gcloud container clusters delete wordpress-cluster
```

