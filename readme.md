Kubernetes
--------------------------------
del griego κυβερνήτης:,  --> significa timonel
k8s
oficial: kubernetes.io
origen: julio 2015 google


minikube instalar
--------------------------------
ir a: https://minikube.sigs.k8s.io/docs/start/

minikube status

minikube starxt

kubectl get po -A

minikube kubectl -- get po -A

minikube dashboard


Deploy example

kubectl create deployment hello-minikube --image=k8s.gcr.io/echoserver:1.4

kubectl expose deployment hello-minikube --type=NodePort --port=8080

kubectl get services hello-minikube

minikube service hello-minikube

kubectl port-forward service/hello-minikube 7080:8080

now: http://localhost:7080/


minikube pause

minikube stop

minikube config set memory 16384

minikube addons list

minikube start -p aged --kubernetes-version=v1.16.1

minikube delete --all   --> borrar todo del cluster


Releases

https://github.com/kubernetes/minikube/releases


su - root     --> conectarse como root

minikube ip   ->> ip del nodo

minikube logs   --> ver los logs

minikube ssh    --> ingresar a la maquina viltual (virtualbox)

uname -a   -->> comproban sitema operativo

df          --ver definicion de archivos

minikube help

minikube update-check  .->> comprobar si hay una version mas moderna de la que tenemos

ps -ef | grep virtualbox            -->> ver maquinas virtuales

pwd         -->> ver directorio actual

ls -la      --> ver directorios y ficheros ocultos

cd .kube

cd .minikube    -->contiene los componentes necesarios para arrnacar el cluster (q esta en .kube)

cd machines      --> aca se contiene un directorio para c/uno de los clusters que haya podido arrancar dentro de la maquina


minikube dashboard

----
arrancar minikube con otro container runtime
--------------------------

minikube start --container-runtime=cri-o

kubectl get nodes

kubectl get nodes -o wide   --> informacion de los nodos con mas detalle


Tener mas de un cluster
-------------------------

minikube start -p desarrollo       --> p de profile

kubectl config use-context minikube         -->cuando estas en otro contexto

minikube profile        --> permete conocer en cual perfil/cluster estamos

minikube profile list

minikube profile desarrollo         -->> cambiarse de perfil

kubectl config current-context   --> ver en que perfil se esta (con kubectl)

en directorio .minikube/machines se crea automaticamente un directorio por cada perfil que se tenga.
y en directorio .kube/config en este archivo escribe la configuracion de los clusters y el current

ps -f | grep virtualbox         --> ver maquinas virtuales con virtualbox

minikube profile desarrollo 

minikube stop


Borrar un cluster minikube
-------------------------------
minikube delete             -->> estando parado sobre el profile  en este caso desarrollo

minikube profile list       --> verificarlo

dentro de .minikube/machines el directorio desarrollo debe desaparecer


Docker desktop
------------------------------

descargar y habilitar.

kubectl cluster-info

muestra la sig info:
 - Kubernetes control plane is running at https://192.168.64.2:8443
 - KubeDNS is running at https://192.168.64.2:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

 - To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.


 PODs
 -------------------------------
 compuestos por contenedores docker.

Ciclo de vida de una app en k8s
```
app -> imagen-container  -> deploy  

                                -- con docker ( docker run )

                                -- con kubernetes

                                        -- imperativo

                                                - despliegue directo en un pod (kubectl run)

                                        -- declarativo

                                                -  manifiesto yaml 

                                                        apiversion: v1

                                                        kind: Prod

                                                        metadata: 

                                                         name nginx

                                                        spec:

                                                         containers:

                                                         - name: nginx

                                                           image: nginx

```

Pod: debe ser independiente en recursos ( por lo general unicamente tienen un container, independiente de otros contenedores) 

  --  cuando scala escalan todos los contenedores que esten dentro del pod


 - tiene un conjunto de recursos ( direcciones, puertos, hostnames, sockets, memoria, volumenes, etc)
 
 - tiene una ip unica para todo el pod

 - se crean detro de un node del cluster

 - el scheduler determina en que nodo del cluster va a parar un pod (dependiendo disponibilidad y metricas)

 - hay una red privada / virtual network que utilizan todos los pods dentro de k8s

 - no tienen estado no se debe guardar informacion en ellos


Crear pod
-----------------------


en ver < 1.17

kubectl run nginx1 --image=nginx

kubectl get pods

kubectl get deployments

kubectl run --generator=run-pod/v1 nginx2 --image=nginx

en ver > 1.18

kubectl run nginx3  --image=nginx


ver las propiedades de un pod:

kubectl get pods


kubectl describe pod/nginx1                     tipo y nombre


comando exec    

kubectl exec nginx1 ls

kubectl exec [POD] [COMMAND] is DEPRECATED and will be removed in a future version. 

Use kubectl exec [POD] -- [COMMAND] instead.

kubectl exec hello-minikube-6ddfcc9757-wknqr -- ls

kubectl exec hello-minikube-6ddfcc9757-wknqr -it bash

uname -a

df -f                   comprobar sistema de ficheros

------------

contenedor basado en apache:

kubectl run apache --image=httpd --port=8080 --generator=run-pod/v1

kubectl run apache --image=httpd --port=8080

kubectl logs apache                     --> ver logs del pod apache

kubectl logs -f apache                  --> ver logs y se queda recibiendo mas logs

kubectl get pods

kubectl logs apache --tail=30           --> ver logs las ultimas 30 lineas

kubectl exec apache -it bash

wget localchost

apt-get update                          --> actualizar todos los paquetes (del so) dentro del container



apt-get install wget

wget localhost                  -->  para ve el apache corriendo.

kubectl proxy                   -- permite probar la app (abre un puerto por el cual se peude preguntar por las caracteristicas del cluster y las apps)


http://127.0.0.1:8001/            --> muestra el listado de todas las apis disponibles (por el apiserver)

http://127.0.0.1:8001/version

http://127.0.0.1:8001/healthz          --> indica si el cluster esta ok

http://127.0.0.1:8001/api/v1

http://127.0.0.1:8001/api/v1/namespaces                --> lista de namaspaces

http://127.0.0.1:8001/api/v1/namespaces/default         --> namespace default

http://127.0.0.1:8001/api/v1/namespaces/default/pods

http://127.0.0.1:8001/api/v1/namespaces/default/pods/apache

http://127.0.0.1:8001/api/v1/namespaces/default/pods/apache/proxy/   --> verifaicar el pod

-----------


minikube ssh            --> conectarse a minikube

uname -a

todo lo que se va haciendo con kubernetes se esta escribiendo con el motor de contenedores que se esta trabajando.

docker ps

docker ps | grep apache


Introduccion a YAML
---------------------

yaml --> fichero que tienen componentes de tipo clave valor

```

#Las cadenas no requieren comillas:
Título: Introducción a YAML

# Pero se pueden usar:
title-w-quotes: 'Introducción a YAML'

# Las cadenas multilínea comienzan con |
ejecutar: |
    npm ci
    npm build
    prueba npm

#Secuencias
#Las secuencias nos permiten definir listas en YAML:
# Una lista de números usando guiones:
números:
    - uno
    - dos
    - Tres

# La versión en línea:
números: [uno, dos, tres]

#Valores anidados
#Podemos usar todos los tipos anteriores para crear un objeto con valores anidados, así:
# Mil novecientos ochenta y cuatro datos nuevos.
1984:
    autor: George Orwell
    publicado en: 1949-06-08
    recuento de páginas: 328
    descripción: |
        Una novela, a menudo publicada como 1984, es una novela distópica del novelista inglés George Orwell.
        Fue publicado en junio de 1949 por Secker & Warburg como noveno y último b de Orwell.


#Lista de objetos
#Combinando secuencias y valores anidados  podemos crear una lista de objetos.
# Hagamos una lista de libros:
- 1984:
    autor: George Orwell
    publicado en: 1949-06-08
    recuento de páginas: 328
    descripción: |
        Una novela, a menudo publicada como 1984, es una novela distópica del novelista inglés George Orwell.

- el Hobbit:
    autor: J. R. R. Tolkien
    publicado en: 1937-09-21
    recuento de páginas: 310
    descripción: |
        The Hobbit, o There and Back Again es una novela de fantasía para niños del autor inglés J. R. R.

```

practica1

cd practica1

docker build -t edison954/nginx:v1 .

docker images

docker login

docker push edison954/nginx:v1                  --> con ello ya subimos la imagen al regestry, ahora si podriamos crear el yaml que use esta img.

probar que la img funciona:

docker run -d -p 80:80 --name nginx edison954/nginx:v1

docker ps

localhost                       --> debe salir el texto: "Ejemplo de POD con KUBERNETES y YAML"

docker stop nginx                       --> para el contenedor


kubectl create -f nginx.yaml                           --> permite crear un recurso a partir de un fichero yaml

kubectl get pods

kubectl delete pod/nginx1

kubectl create -f nginx.yaml

kubectl logs nginx1

kubectl describe pod/nginx1

-----------

Generar la configuracion de un POD en JSON o en YAML



kubectl get pod/nginx1 -o yaml                      --> -o permite generar la info en los dos tipos como yml o como json


kubectl get pod/nginx1 -o yaml > f1.yaml               --> guardarlo como fichero f1.yaml

kubectl get pod/nginx1 -o json > f1.json




























