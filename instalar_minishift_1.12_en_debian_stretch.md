# Instalar Minishift 1.19 en Debian Stretch

Minishift es una herramienta que permite levantar un cluster openshift de un nodo en una virtual requiere un hypervisor, en linux esta KVM y VirtualBox principalmente, es mejor KVM ya que es nativo y consume menos recursos.

* Instalar KVM con sus drivers para docker-machine

Primero que nada bajo el driver KVM para el docker-machine, y lo hago ejecutable

```
sudo curl -L https://github.com/dhiltgen/docker-machine-kvm/releases/download/v0.7.0/docker-machine-driver-kvm -o /usr/local/bin/docker-machine-driver-kvm
sudo chmod +x /usr/local/bin/docker-machine-driver-kvm
```

_Hay versiones mas nuevas de docker-machine-kvm pero para openshift la ultima recomendada es la v0.7.0_

Instalar libvirt, qemu-kvm y otros paquetes necesarios

```
sudo apt install qemu-kvm libvirt-clients libvirt-daemon-system bridge-utils libguestfs-tools genisoimage virtinst libosinfo-bin
```

Agregar al usuario al grupo libvirtd

```
sudo usermod -a -G libvirt $USER
sudo usermod -a -G libvirt-qemu $USER
```

Reload permisos de grupos

```
$ newgrp libvirt
$ newgrp libvirt-qemu 
```

Para corroborar si anda, simplemente se puede ejecutar el comando "kvm" y ver la salida

Antes de seguir con la instalacion de Minishift hay que levantar la red de libvirt (en el caso de que este inactiva)

```
sudo virsh net-start default
sudo virsh net-list --all
```



* Instalar e iniciar Minishift

Antes que nada bajo los archivos para el minishift, los descomprimo, y copio el binario a /usr/bin

```
wget https://github.com/minishift/minishift/releases/download/v1.19.0/minishift-1.19.0-linux-amd64.tgz
tar zxvf minishift-1.19.0-linux-amd64.tgz
cd minishift-1.19.0-linux-amd64/
sudo mv minishift /usr/bin/
```

Ahora simplemente se ejecuta el binario, esto nos mostrara en pantalla los pasos necesarios para instalar el Openshift Cluster, bajara las OC tools y las isos necesarias, basicamente las imagenes de Boot2Docker y la de CentOs.

```
minishift start
```
ej:

```
naguer@zenbian:~$ minishift start 
-- Starting profile 'minishift'
-- Check if depereccated options are used ... OK
-- Checking if https://github.com is reachable ... OK
-- Checking if requested OpenShift version 'v3.9.0' is valid ... OK
-- Checking if requested OpenShift version 'v3.9.0' is supported ... OK
-- Checking if requested hypervisor 'kvm' is supported on this platform ... OK
-- Checking if KVM driver is installed ... 
   Driver is available at /usr/local/bin/docker-machine-driver-kvm ... 
   Checking driver binary is executable ... OK
-- Checking if Libvirt is installed ... OK
-- Checking if Libvirt default network is present ... OK
-- Checking if Libvirt default network is active ... OK
-- Checking the ISO URL ... OK
-- Checking if provided oc flags are supported ... OK
-- Starting local OpenShift cluster using 'kvm' hypervisor ...
-- Minishift VM will be configured with ...
   Memory:    2 GB
   vCPUs :    2
   Disk size: 20 GB
-- Starting Minishift VM ............... OK
-- Checking for IP address ... OK
-- Checking for nameservers ... OK
-- Checking if external host is reachable from the Minishift VM ... 
   Pinging 8.8.8.8 ... FAIL
   VM is unable to ping external host
-- Checking HTTP connectivity from the VM ... 
   Retrieving http://minishift.io/index.html ... OK
-- Checking if persistent storage volume is mounted ... OK
-- Checking available disk space ... 1% used OK
   Importing 'openshift/origin:v3.9.0' . CACHE MISS
   Importing 'openshift/origin-docker-registry:v3.9.0' . CACHE MISS
   Importing 'openshift/origin-haproxy-router:v3.9.0' . CACHE MISS
-- OpenShift cluster will be configured with ...
   Version: v3.9.0
-- Copying oc binary from the OpenShift container image to VM ...................... OK
-- Starting OpenShift cluster .....................
Using nsenter mounter for OpenShift volumes
Using public hostname IP 192.168.42.104 as the host IP
Using 192.168.42.104 as the server IP
Starting OpenShift using openshift/origin:v3.9.0 ...
OpenShift server started.

The server is accessible via web console at:
    https://192.168.42.104:8443

You are logged in as:
    User:     developer
    Password: <any value>

To login as administrator:
    oc login -u system:admin


-- Exporting of OpenShift images is occuring in background process with pid 7638.
```

Una vez que termina la instalacion, muestra la ip y las credenciales para loguearte.

Ahora faltara modificar el PATH environment para incluir el Openshift Client Tools (oc)

Nota: Cuando usas Minishift interactuas con dos componentes

1. A *virtual machine* (VM) creada por Minishift
2. The *OpenShift cluster* provisiona por Minishift que corre en la VM

La primera es manejada por el binario de minishift (/usb/bin/minishift) y la segunda es manejada por el OC, como parte del aprovisionamiento Minishift descarga el OC, pero hasta no configurar el PATH correcto no lo podremos utilizar.

Para esto hay que ejecutar "minishift oc-env" que mostrar el comando que se necesita tipear para agregar el OC al path

ej:

```
naguer@zenbian:~$ minishift oc-env
export PATH="/home/naguer/.minishift/cache/oc/v3.9.0/linux:$PATH"
```



 Despues realizar el export correspondiente

```
export PATH="/home/naguer/.minishift/cache/oc/v3.9.0/linux:$PATH"
```

 Ahora ya se pueden utilizar los comandos de OC

ej:

```
naguer@zenbian:~$ oc status
In project My Project (myproject) on server https://192.168.42.104:8443

You have no services, deployment configs, or build configs.
Run 'oc new-app' to create an application.
```



Fuentes:
https://blog.novatec-gmbh.de/getting-started-minishift-openshift-origin-one-vm
https://docs.openshift.org/latest/minishift/using/basic-usage.html