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

minikube dashboard


crear otro pod en base al anterior:

cp nginx.yaml nginx1.yaml


kubectl create -f nginx.yaml 


Borrar pods:

kubectl get pods

kubectl delete pod/hello-minikube-6ddfcc9757-wknqr

kubectl delete pod nginx, pod1, pod2,...    --> borrar varios

kubectl delete pod apache --grace-period=5   --> espera 5 segundos para eliminar el pod
intenta borrarlo una vez termine todos los procesos

kubectl delete pod apache --now        --> borrelo inmediatamente

kubectl delete pods --all                 --> Ojo con este comando, es muy peligroso jeje

--all    (siempre que un comando es con una palabra es con dos guiones)


Crear pod con multiples contenedores
------------------------

```
apiVersion: v1
kind: Pod
metadata:
  name: redis-django
  labels:
    app: web
spec:
  containers:
    - name: almacen
      image: redis
      ports:
        - containerPort: 6379
    - name: frontal
      image: django
      ports:
        - containerPort: 8000

```

kubectl create -f multi.yaml 

kubectl describe pod/redis-django

tener en cuenta que todos los containers que hay dentro de un pod comparten la misma direccion ip.

kubectl logs pod/redis-django -c almacen        --> -c para indicar el contenedor


comando apply
---------------

poder crear/modificar el recurso con un solo comando

kubectl apply -f nginx.yaml             --> crea el pod

vi nginx.yaml

kubectl apply -f nginx.yaml              --> pod/nginx configured


politica de rebote :  restartPolicy: Always  (por defecto)
------------------
- Always

- OnFailure                              --> solo si ha fallado

- Never                                   --> no restart

:Alway

kubectl apply -f restart-always.yaml

kubectl get pods

kubectl describe pod tomcat

kubectl delete pod tomcat


:OnFailure

kubectl apply -f restart-onfailure.yaml

kubectl get pods


kubectl exec -it tomcat bash                    --> ingresar al container

ps -ef

catalina.sh stop                        --> para tomcat

kubectl describe pod tomcat


Labels  (63 caracteres)
------------------------

```

apiVersion: v1
kind: Pod
metadata:
  name: tomcat
  labels:
    estado: "desarrollo"
    responsable: "edison"
spec:
  containers:
   - name: tomcat     
     image: tomcat

```

kubectl apply -f labels/tomcat.yaml

kubectl get pods

kubectl get pods -o wide

kubectl get pod tomcat

kubectl get pod tomcat -o wide

kubectl get pod tomcat --show-labels

kubectl get pod tomcat --show-labels -L estado      --> -L para que cree una nueva columna con los valores de la etiqueta


kubectl get pod tomcat --show-labels -L estado,responsable

añadir etiqueta:

kubectl label pod tomcat fecha=2021-07-19

kubectl get pod tomcat --show-labels -L estado,responsable,fecha

Modificar etiqueta

kubectl label --overwrite pod/tomcat estado=test

Eliminar etiqueta

kubectl label pod/tomcat responsable-       --> elimina la etiqueta responsable


Selectors  (-l    o  --selector) (condiciones de tipo and)
-------------
condiciones para seleccionar objetos que tengan determinadas etiquetas

kubectl apply -f labels/.

kubectl get pods

kubectl get pods --show-labels

kubectl get pods --show-labels -l estado=desarrollo

kubectl get pods --show-labels -l estado=testing

kubectl get pods --show-labels -l estado=produccion

kubectl get pods --show-labels -l estado==produccion    --> se puede poner = o ==

Condiciones

kubectl get pods --show-labels -l estado=desarrollo,responsable=juan

kubectl get pods --show-labels -l responsable!=juan    --> todos donde el responsable no sea juan


kubectl get pods --show-labels -l estado!=testing

Conjuntos

kubectl get pods --show-labels -l 'estado in(desarrollo)'

kubectl get pods --show-labels -l 'estado in(desarrollo,testing)'     -->permite poner listas


kubectl get pods --show-labels -l 'estado notin(desarrollo,testing)'

kubectl delete pods -l estado=desarrollo     --> cualquier comando admite selectores


Anotaciones
---------------
similares a los labels, clave/valor  sirve para comentarios, describir y documentar componentes

```
apiVersion: v1
kind: Pod
metadata:
  name: tomcat4
  labels:
    estado: "produccion"
    responsable: "edison"
  annotations:
    doc: "Se debe compilar con gcc"
    adjunto: "ejemplo de anotacion"
spec:
  containers:
   - name: tomcat     
     image: tomcat
```

kubectl apply -f anotations/.

kubectl get pod tomcat4

kubectl describe pod tomcat4

kubectl get pod tomcat4 -o jsonpath={.metadata.annotations}     --> -o jsonpath forma de buscar un elemento dentro del contenido en json


Deployments
-------------

Workloads y controllers

workloads: lo que se esta utilizando para desplegar contenedores (ej los pods)

- deployment: envuelven a los pods y les dan caracteristicas (updates, rolbacks, comprobacion)

- replicatset: (subir o bajar los pods de acuerdo a lo que el cluster tenga indicado)(replication controllers)

- stateful set: (gestiona el desplieuge y escalado, garantiza el orden)

- daemon set: (asegura que todos los nodos del cluster van a tener una copia de un pod)

- job: (crea uno o varios pods y se aseguran que terminen satisfactoriamente)

- cron job: (parecido al job per se hace con planificacion/scheduler)


Controllers: siempre consultan sobre el estado de los componentes en el cluster y los intentan dejar segun su definciion.


Deployment
---------------

objeto inicial que puedo obtener en k8s es un contenedor..
este no se puede solo luego hay que ponerlo dentro de un Pod

pods por si solo no escalan, 

no se recuperan ante caidas.

no son buenos para updates y rollbacks

para solucionar esto, se usa el deployment (engloba las propiedades que permiten update y rolbacks adecuados, recuperarse ante caidas y escalar)

cuando se crea un deployment automaticamente se crea el replicaset.

Replicaset: son los encargados de hacer las replicas y de gestionar la recuperacion ante caidas (componente)

siempre estan normalmente dentro del deployment.

kubectl get deploy

kubectl get rs


en modo imperativo:

kubectl create deployment apache --image=httpd

kubectl get deploy

kubectl get rs

kubectl describe deploy apache

pod template: que caracteristicas quiero que tengan los pods asociados al deploy

kubectl get deploy apache -o yaml


```
apiVersion: apps/v1 # i se Usa apps/v1beta2 para versiones anteriores a 1.9.0
kind: Deployment
metadata:
  name: nginx-d
spec:
  selector:   #permite seleccionar un conjunto de objetos que cumplan las condicione
    matchLabels:
      app: nginx
  replicas: 2 # indica al controlador que ejecute 2 pods
  template:   # Plantilla que define los containers
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```

kubectl apply -f deployments/deploy_nginx.yaml

kubectl get pods

kubectl get deploy

kubectl get rs

kubectl get rs -o wide

kubectl get deploy -o wide

kubectl get pods -o wide

selector (conjunto de etiquedas por los cuales se puede buscar un objeto)

kubectl get pods -l app=nginx

kubectl get pods -L app     --> mapear a columna app

kubectl get pods -l app=nginx -L app

kubectl get deploy,pods,rs    --> permite ver distintos tipos de objetos de una

kubectl get deploy,pods,rs -l app=nginx -L app 

Comando EDIT
----------

kubectl describe deploy nginx-d

vi deployments/deploy_nginx.yaml    --> modificar y poner tres replicas->

```
apiVersion: apps/v1 # i se Usa apps/v1beta2 para versiones anteriores a 1.9.0
kind: Deployment
metadata:
  name: nginx-d
spec:
  selector:   #permite seleccionar un conjunto de objetos que cumplan las condicione
    matchLabels:
      app: nginx
  replicas: 3 # indica al controlador que ejecute 2 pods
  template:   # Plantilla que define los containers
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```

kubectl apply -f deployments/deploy_nginx.yaml

kubectl get deploy

kubectl get pod

kubectl get rs

la otra forma de editar:    (sin necesidad de tener el yaml original)

kubectl edit deploy nginx-d     --> abre el editor del deploy en vi y permite editar 

kubectl get deploy

kubectl get pod

kubectl get rs

kubectl get deploy nginx-d -o yaml    --para generarlo si no se tiene el archivo

escalar un deployment
-------------

kubectl get deploy

kubectl scale deploy nginx-d --replicas=5

kubectl get pod

kubectl get rs

esclar de acuerdo a un label:

vi deployments/deploy_nginx.yaml

```
apiVersion: apps/v1 # i se Usa apps/v1beta2 para versiones anteriores a 1.9.0
kind: Deployment
metadata:
  name: nginx-d
  labels:
    estado: "1"
spec:
  selector:   #permite seleccionar un conjunto de objetos que cumplan las condicione
    matchLabels:
      app: nginx
  replicas: 3 # indica al controlador que ejecute 2 pods
  template:   # Plantilla que define los containers
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80

```

kubectl apply -f deployments/deploy_nginx.yaml

kubectl scale deploy -l estado=1 --replicas=10

kubectl get deploy

kubectl get pod

kubectl scale deploy -l estado=1 --replicas=2

Configurar la memoria de un deployment
-----------------

```
apiVersion: apps/v1 # i se Usa apps/v1beta2 para versiones anteriores a 1.9.0
kind: Deployment
metadata:
  name: nginx-d
  labels:
    estado: "1"
spec:
  selector:   #permite seleccionar un conjunto de objetos que cumplan las condicione
    matchLabels:
      app: nginx
  replicas: 5 # indica al controlador que ejecute 2 pods
  template:   # Plantilla que define los containers
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
        resources:
          limits:
              memory: "200Mi"
          requests:
              memory: "100Mi" 
```

kubectl apply -f deployments/deploy_nginx.yaml

kubectl edit deploy nginx-d

Configurar la cpu de un pod
-----------------

preguntar como andamos de recursos:

kubectl describe node

kubectl decribe nodes


```
apiVersion: apps/v1 # i se Usa apps/v1beta2 para versiones anteriores a 1.9.0
kind: Deployment
metadata:
  name: nginx-d
  labels:
    estado: "1"
spec:
  selector:   #permite seleccionar un conjunto de objetos que cumplan las condicione
    matchLabels:
      app: nginx
  replicas: 2 # indica al controlador que ejecute 2 pods
  template:   # Plantilla que define los containers
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
        resources:
          limits:
              memory: "200mi"
              cpu: "1"
          requests:
              memory: "100mi" 
              cpu: "0.5"

```

kubectl apply -f deployments/deploy_nginx.yaml

kubectl top node

kubectl top pods

kubectl describe node 

Servicios
----------------------
componentes que permiten que nuestas apps se conecten con el mundo exterior

interactura entre el cliente y el deployment

ofrece: 
 - ip fija
 - nombre fijo
 - puerto fijo

tipos de servicio
------------

- ClusterIP
  
  para que el pod solo sea accedido dentro del cluster
  ofrece ip, nombre y puerto

- NodePort

  accesible desde fuera del cluster
  ofrece Ip, nombre, puerto y nodeport

- LoadBalancer

  accesible desde fuera del cluster
  integrado con loadbalancers de terceros
  aws, google cloud, azure

cd servicios

docker build -t edison954/web .

docker images

docker run -d -p 80:80 edison954/web

docker ps

docker stop a5149f1e2fce

docker login

docker push edison954/web

verificar que la imagen este subida al regestry https://hub.docker.com/

crear deploy

```
apiVersion: apps/v1 # i se Usa apps/v1beta2 para versiones anteriores a 1.9.0
kind: Deployment
metadata:
  name: web-d
  labels:
    estado: "1"
spec:
  selector:   #permite seleccionar un conjunto de objetos que cumplan las condicione
    matchLabels:
      app: web
  replicas: 2 # indica al controlador que ejecute 2 pods
  template:   # Plantilla que define los containers
    metadata:
      labels:
        app: web
    spec:
      containers:
      - name: apache
        image: edison954/web:latest
        ports:
        - containerPort: 80
```

kubectl apply -f deploy_web.yaml

kubectl get deploy

kubectl get rs

kubectl get pods

kubectl exec -it web-d-55bfc5c788-vpc4s bash      --> ingresar al pod para probar

ps -ef

apt-get install curl

curl localhost

exit 

/// el problema es que si ingresamos desde el navegador localhost no funciona.
por lo que no se puede acceder desde afuera al deployment.

crearlo de modo imperativo y de modo declarativo

kubectl expose deployment web-d --name=web-svc --target-port=80 --type=NodePort

kubectl get svc

kubectl describe svc web-svc

minikube ip

kubectl get node -o wide

kubectl delete svc web-svc

crear servicio de forma declarativa
-----------


```
apiVersion: v1
kind: Service
metadata:
  name: web-svc
  labels:
     app: web
spec:
  type: NodePort
  ports:
  - port: 80
    nodePort: 30002
    protocol: TCP
  selector:
     app: web
```

kubectl apply -f web-svc.yaml

kubectl get svc

revisar en el navegador con la ip del nodo (internal-ip) + el puerto expuesto en el servicio 30002

kubectl get node -o wide

kubectl describe svc web-svc

Rolling updates  (modificar aplicaciones)
-------------------
(destruir y recrear los pods en las actualizaciones)
  strategy:
     type: RollingUpdate

- RollingUpdate: va modificando los pods de manera evolutiva para que siempre haya un pod que este dando servicio.

- Recreate: directamente borra los pods y substituye creando las replicas.


```
apiVersion: apps/v1 # i se Usa apps/v1beta2 para versiones anteriores a 1.9.0
kind: Deployment
metadata:
  name: nginx-d
  labels:
    estado: "1"
spec:
  selector:   #permite seleccionar un conjunto de objetos que cumplan las condicione
    matchLabels:
      app: nginx
  replicas: 10 # indica al controlador que ejecute 2 pods
  strategy:
     type: RollingUpdate
  template:   # Plantilla que define los containers
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```

cd rolling/

kubectl apply -f deploy_nginx_ori.yaml

kubectl get pods

kubectl describe deploy nginx-d

kubectl rollout history deploy nginx-d    --> ver el historico de desplieuges

kubectl rollout history deploy nginx-d --revision=1

kubectl get rs

Rollingupdate
------------------

  strategy:
     type: RollingUpdate
     rollingUpdate:
        maxUnavailable: 1          --> nunca bajes de (replicas-1)
        maxSurge: 1                --> nunca seas superior de (replicas+1)
  minReadySeconds: 2            --> minimo de segundos que pasa para crear un pod (lo hace mas lento en este caso c/2segs)



```
apiVersion: apps/v1 # i se Usa apps/v1beta2 para versiones anteriores a 1.9.0
kind: Deployment
metadata:
  name: nginx-d
  labels:
    estado: "1"
spec:
  selector:   #permite seleccionar un conjunto de objetos que cumplan las condicione
    matchLabels:
      app: nginx
  replicas: 10 # indica al controlador que ejecute 2 pods
  strategy:
     type: RollingUpdate
     rollingUpdate:
        maxUnavailable: 1
        maxSurge: 1
  minReadySeconds: 2
  template:   # Plantilla que define los containers
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.16.1
        ports:
        - containerPort: 80
```

crear servicio:

```
apiVersion: v1
kind: Service
metadata:
  name: nginx-svc
  labels:
     app: nginx
spec:
  type: NodePort
  ports:
  - port: 80
    nodePort: 31000
    protocol: TCP
  selector:
     app: nginx
```

kubectl apply -f nginx-svc.yaml

kubectl describe svc nginx-svc

kubectl apply -f deploy_nginx2.yaml

kubectl get pods

kubectl get rs

kubectl describe svc nginx-svc

kubectl rollout history deploy nginx-d

kubectl rollout history deploy nginx-d --revision=2

kubectl describe svc nginx-svc

Rolling back
----------------

kubectl rollout status deploy nginx-d     --> verificar el estado

kubectl rollout history deploy nginx-d

kubectl rollout history deploy nginx-d --revision=2

kubectl rollout undo deployment nginx-d --to-revision=1     --> volver a poner la v1

kubectl get pods

kubectl rollout status deploy nginx-d

kubectl rollout history deploy nginx-d

kubectl rollout history deploy nginx-d --revision=3


RollingUpdate:  recreate  
---------------

los termina todos y los vuelve a crear

```
apiVersion: apps/v1 # i se Usa apps/v1beta2 para versiones anteriores a 1.9.0
kind: Deployment
metadata:
  name: nginx-d
  labels:
    estado: "1"
spec:
  selector:   #permite seleccionar un conjunto de objetos que cumplan las condicione
    matchLabels:
      app: nginx
  replicas: 10 #  indica al controlador que ejecute 2 pods
  strategy:
     type: Recreate
  minReadySeconds: 2
  template:   # Plantilla que define los containers
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.16.1
        ports:
        - containerPort: 80
```

Variables de entorno del servicio en pods
------------------

kubectl get svc

kubectl describe svc nginx-svc

kubectl get pods

kubectl exec -it nginx-d-5d59d67564-zkqc7 bash

env         --> ver las variables de entorno

env | grep NGINX

----------


kubectl get svc

kubectl describe svc nginx-svc

kubectl get pods

kubectl exec -it nginx-d-5d59d67564-d5g4h bash

env

env | grep NGINX

```
root@nginx-d-5d59d67564-d5g4h:/# env | grep NGINX
NGINX_SVC_PORT=tcp://10.106.235.125:80
NGINX_SVC_PORT_80_TCP_PORT=80
NGINX_SVC_PORT_80_TCP_PROTO=tcp
NGINX_SVC_SERVICE_HOST=10.106.235.125
NGINX_VERSION=1.7.9-1~wheezy
NGINX_SVC_SERVICE_PORT=80
NGINX_SVC_PORT_80_TCP_ADDR=10.106.235.125
NGINX_SVC_PORT_80_TCP=tcp://10.106.235.125:80
```

kubectl describe svc nginx-svc    -->coinciden los datos con los del contenedor (env)

Ejemplo app php-redis
----------------

cd php_app

vi redis-master.yaml

```
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
kind: Deployment
metadata:
  name: redis-master
  labels:
    app: redis
spec:
  selector:
    matchLabels:
      app: redis
      role: master
      tier: backend
  replicas: 1
  template:
    metadata:
      labels:
        app: redis
        role: master
        tier: backend
    spec:
      containers:
      - name: master
        image: k8s.gcr.io/redis:e2e  # or just image: redis
        ports:
        - containerPort: 6379
```

kubectl apply -f redis-master.yaml

kubectl get pods

kubectl get deploy

kubectl get rs

kubectl get all -l app=redis      -->(muestra los tres elementos, pod,deploy y rs)

Crear el servicio
------------------

vi redis-master-service.yaml

```
apiVersion: v1
kind: Service
metadata:
  name: redis-master
  labels:
    app: redis
    role: master
    tier: backend
spec:
  ports:
  - port: 6379
    targetPort: 6379
  selector:
    app: redis
    role: master
    tier: backend

```

kubectl apply -f redis-master-service.yaml

kubectl get svc

kubectl describe svc redis-master

comprobar que el servicio este funcionando:

kubectl get pods

kubectl exec -it redis-master-84777845f-8h9p4 bash

uname -a

cat /etc/hosts

ping redis-master

nombre-namespace-tipo-cluster.local

redis-master.default.svc.cluster.local

wget redis-master

nslookup    --preguntar por una direccion

nslookup redis-master

apt-get update

apt-get install bind-utils    --> instalar el nslookup

apt-get install dnsutils 

nslookup redis-master     --> ver la ip dns del servicio (estando dentro del contenedor)

muestra lo siguient
```
Server:		10.96.0.10
Address:	10.96.0.10#53

Name:	redis-master.default.svc.cluster.local
Address: 10.102.12.31
```

cat /etc/resolv.conf      --> para ver el servidor dns de la maquina actual  (este archivo es el que traduce el dns a la ip correspondiente)

---- Crear los esclavos

vi redis-slave.yaml

```
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
kind: Deployment
metadata:
  name: redis-slave
  labels:
    app: redis
spec:
  selector:
    matchLabels:
      app: redis
      role: slave
      tier: backend
  replicas: 2
  template:
    metadata:
      labels:
        app: redis
        role: slave
        tier: backend
    spec:
      containers:
      - name: slave
        image: gcr.io/google_samples/gb-redisslave:v3
        env:
        - name: GET_HOSTS_FROM
          value: dns

```
kubectl apply -f redis-slave.yaml

crear el servicio del redis sclavo

vi redis-slave-service.yaml

```
apiVersion: v1
kind: Service
metadata:
  name: redis-slave
  labels:
    app: redis
    role: slave
    tier: backend
spec:
  ports:
  - port: 6379
  selector:
    app: redis
    role: slave
    tier: backend
```

kubectl apply -f redis-slave-service.yaml

kubectl get svc

kubectl describe svc redis-slave

probarlo.

kubectl get pods

kubectl exec -it redis-master-84777845f-8h9p4 bash

nslookup redis-slave

```
Server:		10.96.0.10
Address:	10.96.0.10#53

Name:	redis-slave.default.svc.cluster.local
Address: 10.99.2.2
```

nslookup redis-master

Creacion Frontend
-----

vi frontend.yaml

```
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
kind: Deployment
metadata:
  name: frontend
  labels:
    app: guestbook
spec:
  selector:
    matchLabels:
      app: guestbook
      tier: frontend
  replicas: 3
  template:
    metadata:
      labels:
        app: guestbook
        tier: frontend
    spec:
      containers:
      - name: php-redis
        image: gcr.io/google-samples/gb-frontend:v4
        env:
        - name: GET_HOSTS_FROM
          value: dns
```

kubectl apply -f frontend.yaml

kubectl get pods

kubectl get all -l app=guestbook

Crear el servicio frontend
-----

vi frontend-service.yaml

```
apiVersion: v1
kind: Service
metadata:
  name: frontend
  labels:
    app: guestbook
    tier: frontend
spec:  
  type: NodePort 
  #type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: guestbook
    tier: frontend

```

kubectl apply -f frontend-service.yaml

kubectl get svc

kubectl  describe svc frontend

probar el servicio

minikube ip

(usar ip y puerto del nodeport)

kubectl get pods

kubectl exec -it redis-master-84777845f-8h9p4 bash

nslookup frontend

kubectl get all -l app=redis

kubectl get all -l app=guestbook

kubectl get all   (pods,deployment,servicios,resplicaset)

kubectl apply -f .

-------------------
Namespaces
-------------------
-------------------

division logica para agrupar los objetos dentro del cluster


kubectl get namespace

vienen por defecto:

|NAME                   |STATUS   |AGE
|-----------------------|---------|-----
|default                |Active   |28d
|kube-node-lease        |Active   |28d
|kube-public            |Active   |28d
|kube-system            |Active   |28d
|kubernetes-dashboard   |Active   |28d


kubectl get namespace default

kubectl describe namespace default


ver los elementos de un namespace

kubectl get pods -n kube-system


Crear y borrar namespaces
------------------

- crearlo de forma imperactiva:

kubectl create namespace n1

kubectl get namespace

kubectl describe namespace n1

kubectl get pods -n n1

- crearlo de forma declarativa

```
apiVersion: v1
kind: Namespace
metadata:
  name: dev1
  labels:
     tipo: desarrollo

```
kubectl apply -f namespace.yaml

kubectl get namespace

kubectl describe namespace dev1

kubectl delete namespace n1

kubectl get namespaces


(permite tener distintos objetos dentro del cluster que se llamen igual)


Crear objetos en un namespace
----------------------

vi deploy_elasitc.yaml

```
apiVersion: apps/v1 # i se Usa apps/v1beta2 para versiones anteriores a 1.9.0
kind: Deployment
metadata:
  name: elastic
  labels:
    tipo: "desarrollo"
spec:
  selector:   #permite seleccionar un conjunto de objetos que cumplan las condicione
    matchLabels:
      app: elastic
  replicas: 2 # indica al controlador que ejecute 2 pods
  strategy:
     type: RollingUpdate
  minReadySeconds: 2
  template:   # Plantilla que define los containers
    metadata:
      labels:
        app: elastic
    spec:
      containers:
      - name: elastic
        image: elasticsearch:7.6.0
        ports:
        - containerPort: 9200
```


kubectl apply -f deploy_elasitc.yaml --namespace=dev1


kubectl get deploy elastic -n dev1

kubectl get deploy pod -n dev1

kubectl describe deploy elastic -n dev1

kubectl get rs -n dev1

kubectl get pods -n dev1

kubectl rollout history deploy elastic -n dev1

-------------------------


Configurar namespace por defecto

abrir el config en el directorio .kube

~ ls -la
cd .kube
ls
cat config

se podria buscar la seccion y configurar: current-context y namespace 

```
- context:
    cluster: minikube
    extensions:
    - extension:
        last-update: Fri, 23 Jul 2021 16:40:34 -05
        provider: minikube.sigs.k8s.io
        version: v1.21.0
      name: context_info
    namespace: default
    user: minikube
  name: minikube
current-context: minikube
kind: Config
```

con kubectl:

kubectl config view

kubectl config set-context --current --namespace=dev1

kubectl get rs    --> en este caso no es necesario poner el namespace (-n), ingresa al defecto dev1.

------------------

Poner CPU y Memoria a los namespaces
-------------------
nuevo kind limitrange

````
apiVersion: v1
kind: LimitRange
metadata:
  name: recursos
spec:
  limits:
  - default:
      memory: 512Mi
      cpu: 1
    defaultRequest:
      memory: 256Mi
      cpu: 0.5
    max:
      memory: 1Gi
      cpu: 4
    min:
      memory: 128Mi
      cpu: 0.5
    type: Container
```

kubectl create namespace n1

kubectl get namespace n1

kubectl describe namespace n1

kubectl apply -f limites.yaml     ---> aqui un error porque se aplicarian al tablespaces por defecto no al namespace

kubectl describe namespace n1   --> no quedaron aqui

kubectl describe namespace dev1    --> quedaron aca

kubectl apply -f limites.yaml -n n1

kubectl get deploy -n n1

kubectl get pods -n n1

kubectl describe pod -n n1


--------------------
Crear un cluster Real con Kubeadm con maquinas virtuales
--------------------


poder crear y usar con 3 nodos por ejemplo (minikube no lo permite)

un maestro y dos esclavos

ej: 2.5GB de memoria

https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/

https://kubernetes.io/docs/setup/production-environment/tools/


1. configurar la maquina









