# Instalación 3Scale 2.22 en Minishift

* Crear directorios en el NFS

Para el 3scale se necesitan 4 volumenes, hay que crear los directorios en el server openshiftnfs:

```
mkdir /export/noprod_OCP-3SCALE-{1,2,3,4}
chmod 777 /export/noprod_OCP-3SCALE-?
```



* Agregar las lineas de montaje al /etc/exports

```
/export/noprod_OCP-3SCALE-1    *OOKUB*(rw,no_root_squash,sync,no_subtree_check)                 /export/noprod_OCP-3SCALE-2    *OOKUB*(rw,no_root_squash,sync,no_subtree_check)                 /export/noprod_OCP-3SCALE-3    *OOKUB*(rw,no_root_squash,sync,no_subtree_check)         /export/noprod_OCP-3SCALE-4    *OOKUB*(rw,no_root_squash,sync,no_subtree_check) 
```

Aplicar los cambios

```
exportsfs -a
```

Esto es lo ultimo que hay que hacer en el server de nfs, volvemos a nuestro host que tiene el OC instalado.




* Ahora hay que crear 4 archivos con la configuracion del pv de la siguiente manera:

```
apiVersion: v1
kind: PersistentVolume
metadata:
	name: 3scale-01-volume
spec:                                                                                           	accessModes:                                                                              
	- ReadWriteOnce
	capacity:                                                                                   		storage: 5Gi                                                                       
	nfs:                                                                                  
		path: /export/noprod_OCP-3SCALE-1
		server: openshiftnfs.bancogalicia.com.ar
	persistentVolumeReclaimPolicy: Recycle
```

El 4 PV tiene una diferencia, en vez de "ReadWriteOnce" se configura en "ReadWriteMany" (RWX)

El tamaño asignado es como refenrencia, openshift no te lo limita, y en el NFS no estan asignadas quotas_



* Una vez que esten los 4 archivos yml, creamos los PV.

```
oc create -f pv_3scale_01.yml pv_3scale_02.yml pv_3scale_03.yml pv_3scale_04.yml
```

Luego podemos verificar los PV

```
oc describe pv 3scale-01-volume 3scale-02-volume 3scale-03-volume 3scale-04-volume
```



* Una vez que estan los PVs, cremos el proyecto y nos posicionamos en él.

```
oc new-project 3scale-api-manager \
    --description="3Scale Api Manager" --display-name="3Scale Api Manager"
oc project 3scale-api-manager
```



* Ahora bajamos el template de 3scale, y creamos la aplicacion

```
wget https://raw.githubusercontent.com/3scale/3scale-amp-openshift-templates/master/amp/amp.yml
oc new-app --file amp.yml --param WILDACARD_DOMAIN=devopenshift.bancogalicia.com.ar
```



Salida de ejemplo:

```
--> Deploying template "3scale-api-manager/3scale-api-management" for "./amp.yml" to project 3scale-api-manager                                                                           3scale API Management
---------
3scale API Management main system
Login on https://3scale-admin.devopenshift.bancogalicia.com.ar as admin/XXXX
* With parameters:
* PostgreSQL Connection Password=XXXXA # generated
* ZYNC_SECRET_KEY_BASE=XXXXX # generated
* ZYNC_AUTHENTICATION_TOKEN=XXXX # generated
* AMP_RELEASE=2.2.0
* ADMIN_PASSWORD=XXXX # generated
* ADMIN_USERNAME=admin
* APICAST_ACCESS_TOKEN=XXXX # generated
* ADMIN_ACCESS_TOKEN=XXXX # generated
* WILDCARD_DOMAIN=devopenshift.bancogalicia.com.ar
* WILDCARD_POLICY=None
* TENANT_NAME=3scale
* MySQL User=mysql
* MySQL Password=XXXX # generated
* MySQL Database Name=system
* MySQL Root password.=XXXX # generated
* SYSTEM_BACKEND_USERNAME=3scale_api_user
* SYSTEM_BACKEND_PASSWORD=3na5jqs5 # generated
* REDIS_IMAGE=registry.access.redhat.com/rhscl/redis-32-rhel7:3.2
* MYSQL_IMAGE=registry.access.redhat.com/rhscl/mysql-57-rhel7:5.7-5
* SYSTEM_BACKEND_SHARED_SECRET=pvyqgfsc # generated
*SYSTEM_APP_SECRET_KEY_BASE=a732ab1022630c666018d3c8107eaedd0de85e8ed180718d05b11aa15c00326ba103c14c07424b5728ebde5831d7a8b27de56438e68a83a16ba2018e754b6350 # generated                          * APICAST_MANAGEMENT_API=status
* APICAST_OPENSSL_VERIFY=false
* APICAST_RESPONSE_CODES=true
* MASTER_NAME=master
* MASTER_USER=master
* MASTER_PASSWORD=XXXX # generated
* MASTER_ACCESS_TOKEN=XXX # generated
* APICAST_REGISTRY_URL=http://apicast-staging:8090/policies
--> Creating resources ...
imagestream "amp-system" created
imagestream "amp-backend" created
imagestream "amp-apicast" created
imagestream "amp-wildcard-router" created
persistentvolumeclaim "system-storage" created
persistentvolumeclaim "mysql-storage" created
persistentvolumeclaim "system-redis-storage" created
persistentvolumeclaim "backend-redis-storage" created
deploymentconfig "backend-cron" created
deploymentconfig "backend-redis" created
deploymentconfig "backend-listener" created
service "backend-redis" created
service "backend-listener" created
service "system-provider" created
service "system-master" created
service "system-developer" created
deploymentconfig "backend-worker" created
service "system-mysql" created
service "system-redis" created
deploymentconfig "system-redis" created
service "system-sphinx" created
deploymentconfig "system-sphinx" created
service "system-memcache" created
deploymentconfig "system-memcache" created
route "system-provider-admin-route" created
route "system-master-admin-route" created
route "backend-route" created
route "system-developer-route" created
deploymentconfig "apicast-staging" created
service "apicast-staging" created
deploymentconfig "apicast-production" created
service "apicast-production" created
route "api-apicast-staging-route" created
route "api-apicast-production-route" created
deploymentconfig "apicast-wildcard-router" created
service "apicast-wildcard-router" created
route "apicast-wildcard-router-route" created
configmap "system" created
configmap "mysql-extra-conf" created
configmap "mysql-main-conf" created
deploymentconfig "system-app" created
deploymentconfig "system-resque" created
deploymentconfig "system-sidekiq" created
deploymentconfig "system-mysql" created
configmap "redis-config" created
configmap "smtp" created
imagestream "postgresql" created
imagestream "amp-zync" created
secret "zync" created
deploymentconfig "zync" created
service "zync" created
service "zync-database" created
deploymentconfig "zync-database" created --> Success
Access your application via route '3scale-admin.devopenshift.bancogalicia.com.ar'
Access your application via route 'master-admin.devopenshift.bancogalicia.com.ar'
Access your application via route 'backend-3scale.devopenshift.bancogalicia.com.ar'
Access your application via route '3scale.devopenshift.bancogalicia.com.ar
Access your application via route 'api-3scale-apicast-staging.devopenshift.bancogalicia.com.ar'
Access your application via route 'api-3scale-apicast-production.devopenshift.bancogalicia.com.ar'
Access your application via route 'apicast-wildcard.devopenshift.bancogalicia.com.ar
Run 'oc status' to view your app. 
```



En este paso termino la instalacion del producto, ahora queda configurarlo.



_Nota:  3scale tiene redis como parte del producto, redhat lo compro de esa forma, y nunca lo modifico para que use Datagrid, igualmente tenemos soporte de redis al ser parte de 3Scale_



Links de interes:
https://access.redhat.com/documentation/en-us/red_hat_3scale/2.0/html/infrastructure/onpremises-installation
