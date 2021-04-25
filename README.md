# greysd_platform
greysd Platform repository

##Задание 1

###Почему все pod в namespace kube-system восстановились после удаления

Все под с суфиксом названия машины (в данном случае minikube) статические pod. Kubelet наблюдает за каждым статическим подом и автоматически перезапускает, если они падают)
Статические поды:
* etcd-minikube
* kube-apiserver-minikube
* kube-controller-manager-minikube
* kube-scheduler-minikube

Pod coredns-74ff55c5b-5jv7h Это deployment c параметром Replicas 1 desired что видно из команды:

```
kubectl get deployment -n kube-system
kubectl describe deployment coredns -n kube-system
```

kube-proxy это daemonset который выполняется на каждой ноде в количестве описанном в параметре
Number of Nodes Scheduled with Up-to-date Pods: 1

```
kubectl get daemonsets -n kube-system
kubectl describe daemonset kube-proxy -n kube-system
```

### Выясните причину, по которой pod  frontend  находится в статусе  Error

В логах kubectl logs frontend видно что из-за незаданной переменной окружения PRODUCT_CATALOG_SERVICE_ADDR контейнер падает с паникой

```
panic: environment variable "PRODUCT_CATALOG_SERVICE_ADDR" not set
```

Для того чтобы контейнер заработал прописываем все переменные окружения описанные в примере манифеста с параметрами по умолчанию

