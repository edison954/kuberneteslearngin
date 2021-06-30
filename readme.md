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










