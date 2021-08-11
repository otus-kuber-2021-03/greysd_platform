# greysd_platform
greysd Platform repository

## Задание 1

### Почему все pod в namespace kube-system восстановились после удаления

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
В примере манифеста так же указаны еще несколько переменных окружения отсутствие которое вызовет панику

```
panic: environment variable "PRODUCT_CATALOG_SERVICE_ADDR" not set
```

Для того чтобы контейнер заработал прописываем все переменные окружения описанные в примере манифеста с параметрами по умолчанию

## Задание 2

### Определите, что необходимо добавить в манифест

В манифесте не хватает описание селектора по которому Replicaset будет осуществлять выбор подов для масштабирования

###  опишите произошедшую ситуацию, почему обновление ReplicaSet не повлекло обновление запущенных pod

ReplicaSet следит чтобы количество подов соответствовало описанию, поэтому изменение в спецификации темплейта не привело к изменениям в версиях образа.

### Найдите способ модернизировать свой DaemonSet таким образом, чтобы Node Exporter был развернут как на master, так и на worker нодах

Так как я нашел описание DaemonSet в интернете, то там уже было описание того чтобы он запускался на мастернодах в том числе
      tolerations:
      - operator: Exists

что значит этот toleration подходит для всех ключей, значение и эффектов, то есть запускаться будет на любой ноде

## Задание 3

### task01
Создан аккаунт bob
Дана роль admin
Создан аккаунт dave
### task02
Создан Namespace prometheus
Создан ServiceAccount carol
Даны права get list watch
### task03
Создан Namespace dev
Создан ServiceAccount jane
Jane дана роль admin
Создан ServiceAccount ken
Ken дана роль view


## Задание 4

Создан statefulset
Создан Headless statefulset
Создан Secret
исправлен statefulset на использование secret

## Задание 5

## Почему конфигурация валидна, но не имеет смысла

Потому что данная проверка всегда оканчивается успешно, независимо от наличия процесса

## Бывают ли ситуцаии когда она все-таки имеет смысл

Надо немного изменить запрос sh -c "pidof grep my_web_server_process"

## Задание 9

- [x] Создан Dockerfile с nginx и с конфигом где описан basic_status
- [x] Создан deployment с контейнером nginx и nginx_exporter
- [x] Создан сервис для деплоймента
- [x] Установлен Prometheus-operator через helm
- [x] создан ServiceMоnitor
- [x] сделан скриншот в графане
![Screenshot](kubernetes-monitoring/images/screenshot.png)

## Задание kubernetes-operators

ресурсы создаются потому что контроллер получает существуещее состояние CR как ADDED

#Состояние jobs
kubectl get jobs
NAME                         COMPLETIONS   DURATION   AGE
backup-mysql-instance-job    1/1           1s         58m
restore-mysql-instance-job   1/1           42s        37m

# SQL запрос
kubectl exec -it $MYSQLPOD -- mysql -potuspassword -e "select * from test;" otus-database
mysql: [Warning] Using a password on the command line interface can be insecure.
+----+-------------+
| id | name        |
+----+-------------+
|  1 | some data   |
|  2 | some data-2 |
+----+-------------+

# Задание kubernetes-gitops
Название Deploy обновилось
k -n microservices-demo get deploy
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
frontend-hipster   1/1     1            1           42s

Логи:
ts=2021-08-06T17:12:33.914016838Z caller=helm.go:69 component=helm version=v3 info="preparing upgrade for frontend" targetNamespace=microservices-demo release=frontend
ts=2021-08-06T17:12:33.920237393Z caller=helm.go:69 component=helm version=v3 info="resetting values to the chart's original version" targetNamespace=microservices-demo release=frontend
ts=2021-08-06T17:12:34.566674247Z caller=helm.go:69 component=helm version=v3 info="performing update for frontend" targetNamespace=microservices-demo release=frontend
ts=2021-08-06T17:12:34.595507533Z caller=helm.go:69 component=helm version=v3 info="creating upgraded release for frontend" targetNamespace=microservices-demo release=frontend
ts=2021-08-06T17:12:34.610501104Z caller=helm.go:69 component=helm version=v3 info="checking 5 resources for changes" targetNamespace=microservices-demo release=frontend
ts=2021-08-06T17:12:34.617218977Z caller=helm.go:69 component=helm version=v3 info="Looks like there are no changes for Service \"frontend\"" targetNamespace=microservices-demo release=frontend
ts=2021-08-06T17:12:34.628552375Z caller=helm.go:69 component=helm version=v3 info="Created a new Deployment called \"frontend-hipster\" in microservices-demo\n" targetNamespace=microservices-demo release=frontend
ts=2021-08-06T17:12:34.808647809Z caller=helm.go:69 component=helm version=v3 info="Deleting \"frontend\" in microservices-demo..." targetNamespace=microservices-demo release=frontend
ts=2021-08-06T17:12:34.885470284Z caller=helm.go:69 component=helm version=v3 info="updating status for upgraded release for frontend" targetNamespace=microservices-demo release=frontend



ссылка на инфраструктурный репозиторий git@gitlab.com:greysd/microservices-demo.git

kubectl get canaries.flagger.app -n microservices-demo
NAMESPACE            NAME       STATUS      WEIGHT   LASTTRANSITIONTIME
microservices-demo   frontend   Succeeded   0        2021-08-09T11:39:57Z

kubectl describe canaries.flagger.app frontend -n microservices-demo
Events:
  Type     Reason  Age                 From     Message
  ----     ------  ----                ----     -------
  Warning  Synced  17m (x51 over 42m)  flagger  deployment frontend.microservices-demo get query error: deployments.apps "frontend" not found
  Normal   Synced  4m56s               flagger  New revision detected! Scaling up frontend.microservices-demo
  Normal   Synced  4m26s               flagger  Starting canary analysis for frontend.microservices-demo
  Normal   Synced  2m56s               flagger  Advance frontend.microservices-demo canary weight 40

