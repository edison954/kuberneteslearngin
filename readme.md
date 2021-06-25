Kubernetes
--------------------------------
del griego κυβερνήτης:,  --> significa timonel
k8s
oficial: kubernetes.io
origen: julio 2015 google


minikube instalar
--------------------------------
ir a: https://minikube.sigs.k8s.io/docs/start/

minikube start
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





