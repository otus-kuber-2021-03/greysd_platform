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


# Задание 11 kubernetes-vault

## $ helm status vault

NAME: vault
LAST DEPLOYED: Sun Aug 15 14:48:23 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
Thank you for installing HashiCorp Vault!

Now that you have deployed Vault, you should look over the docs on using
Vault with Kubernetes available here:

https://www.vaultproject.io/docs/


Your release is named vault. To learn more about the release, try:

  $ helm status vault
  $ helm get manifest vault

## Инициализация кластера vault

Unseal Key 1: DrN/0SJX6HkO/lpu1MgyXwNmoJDnR3PzF6HF3A0GqJI=

Initial Root Token: s.fcNb889JhcKRR0jYBPr63uc0

Vault initialized with 1 key shares and a key threshold of 1. Please securely
distribute the key shares printed above. When the Vault is re-sealed,
restarted, or stopped, you must supply at least 1 of these keys to unseal it
before it can start servicing requests.

Vault does not store the generated master key. Without at least 1 keys to
reconstruct the master key, Vault will remain permanently sealed!

It is possible to generate new unseal keys, provided you have a quorum of
existing unseal keys shares. See "vault operator rekey" for more information.


## Статус распечатанного кластера vault
$ kubectl exec -it vault-0 -- vault status

Key                    Value
---                    -----
Seal Type              shamir
Initialized            true
Sealed                 false
Total Shares           1
Threshold              1
Version                1.8.0
Storage Type           consul
Cluster Name           vault-cluster-9ec49daa
Cluster ID             5d023461-82d7-489f-a55e-e124a627bd20
HA Enabled             true
HA Cluster             https://vault-0.vault-internal:8201
HA Mode                standby
Active Node Address    http://10.100.1.8:8200

## авторизация в vault
$alias vault='kubectl exec -it vault-0 -- vault'
$ vault login
Token (will be hidden):
Success! You are now authenticated. The token information displayed below
is already stored in the token helper. You do NOT need to run "vault login"
again. Future Vault requests will automatically use this token.

Key                  Value
---                  -----
token                s.fcNb889JhcKRR0jYBPr63uc0
token_accessor       YBEnWv4afhCe1hqhz0fB2SYi
token_duration       ∞
token_renewable      false
token_policies       ["root"]
identity_policies    []
policies             ["root"]

## Список авторизаций

$ vault auth list
Path      Type     Accessor               Description
----      ----     --------               -----------
token/    token    auth_token_b088297a    token based credentials

## Чтение секретов

$ vault read otus/otus-ro/config
Key                 Value
---                 -----
refresh_interval    768h
password            asajkjkahs
username            otus

$ vault kv get otus/otus-rw/config
====== Data ======
Key         Value
---         -----
password    asajkjkahs
username    otus

## обновленный список авторизаций

$ vault auth list
Path           Type          Accessor                    Description
----           ----          --------                    -----------
kubernetes/    kubernetes    auth_kubernetes_a0f2cd69    n/a
token/         token         auth_token_b088297a         token based credentials

## Почему мы смогли записать otus-rw/conﬁg1 но не смогли otus-rw/conﬁg

Потому, что config1 создается, а config меняется.
В политике прописывали разрешение на создание, а на обновление нет, необходимо добавить разрешение "update"

$ vault write pki_int/issue/example-dot-ru common_name="gitlab.example.ru" ttl="24h"
Key                 Value
---                 -----
ca_chain            [-----BEGIN CERTIFICATE-----
MIIDnDCCAoSgAwIBAgIUW03dnagkf0Jshc76zcexiPY7RyowDQYJKoZIhvcNAQEL
BQAwFTETMBEGA1UEAxMKZXhhbXBsZS5ydTAeFw0yMTA4MTUyMDI5MjBaFw0yNjA4
MTQyMDI5NTBaMCwxKjAoBgNVBAMTIWV4YW1wbGUucnUgSW50ZXJtZWRpYXRlIEF1
dGhvcml0eTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAOBYAQldlJ2S
noFaBY9jQUmBbb3KBfLzDWB7AaTHkvnSQx7jrDKGP2u7VofpdIwPCC0bXBQ2Xgig
Y6+grl5hUuswgYqSrawv3/C9RuM9MRLyhhxbdiWFY0HAoojJDXl2S+S35rTY6TII
QhVqM9DU2SL/rGCnyG1CnNzD74ejP8LGB2RRfJNbzTaoV76QFijm4dDWoLvADvJs
I9k/TDnkesHRCVyat4iDSIzUfGDsFB3G7jiM7wqGUIv+afAnobuFkqoIKtnkLZcJ
2QxNQpgp/+7g2duYC8DQJa1leYiFnATk69ahBapSKLI/mPkeYIuS6VkvkQj+T4mX
jETuNZDLpccCAwEAAaOBzDCByTAOBgNVHQ8BAf8EBAMCAQYwDwYDVR0TAQH/BAUw
AwEB/zAdBgNVHQ4EFgQUIuoezXQD1nnz1am8OIbtYN7c19wwHwYDVR0jBBgwFoAU
23wibMRwrgGeHZcK+NpF4E9yrlUwNwYIKwYBBQUHAQEEKzApMCcGCCsGAQUFBzAC
hhtodHRwOi8vdmF1bHQ6ODIwMC92MS9wa2kvY2EwLQYDVR0fBCYwJDAioCCgHoYc
aHR0cDovL3ZhdWx0OjgyMDAvdjEvcGtpL2NybDANBgkqhkiG9w0BAQsFAAOCAQEA
gaGtdOgAtJfJKIzzUQwmQy9y7TLCxkxDISrWpkY3G2Ydw0IyA5nMr2bof7VNpZLN
LP1LRv4+jK/fnVrG/s/Nm3TO2JlQawgnNdh4kBYovwiWPx/DPjUBnCBQxSjCc9wj
T66+CtjglZjzwULyRCqddEvTWsp+H0Xq8+2WZ2mN37ytrKGHbxWbW25a4RAp8vjb
HT72B+ZbvsVeEW4SXtkhKQcQDPwzKQDUk1XQN160wwMSPcaALeyLQCyoqH3pExLY
tL5Sz59LIFib6wuu3q/5WfTWk6awsZVwiBZdmfc/X23q9AsXNOtun9QQgaanh1G3
0eAi1mWwTj5MCdLsjNOB2Q==
-----END CERTIFICATE-----]
certificate         -----BEGIN CERTIFICATE-----
MIIDZzCCAk+gAwIBAgIUUIu7XLUobUcjemX0Lq9rjgRNA1IwDQYJKoZIhvcNAQEL
BQAwLDEqMCgGA1UEAxMhZXhhbXBsZS5ydSBJbnRlcm1lZGlhdGUgQXV0aG9yaXR5
MB4XDTIxMDgxNTIwNDYxM1oXDTIxMDgxNjIwNDY0MlowHDEaMBgGA1UEAxMRZ2l0
bGFiLmV4YW1wbGUucnUwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDU
DpgYzKvk+5BPPZL7uB75e3vRkWaYSj3+aCgLd5ypWBE18eFNV1OALi/Dwbef7/vj
gLstPYLhYhqy9jaDAdIejnfEm1BrcwxGlgxCAUdvcozB6QrJcFI18xUYMk+d8cZw
hD5s9PCRDQahBaLm2f5OkQs/e+NUCdXV8R14/kjYCRMexfnp64xLYSnbuEMo3HhM
VeqjXvPwpNcZeHLukmr9r+z2K2e3qupcWR5mFbbgSM5EjiiiefW6ohB6kTf3dna0
1JLv33SLTkwwDrdfPuM0+SpQrsDuM3WU0P8vnaF/oxhb0KYSXSZm9rxICnT4TPR9
8y6cdeAX8iPsiGahrN7BAgMBAAGjgZAwgY0wDgYDVR0PAQH/BAQDAgOoMB0GA1Ud
JQQWMBQGCCsGAQUFBwMBBggrBgEFBQcDAjAdBgNVHQ4EFgQUulUbLh/yZeKerLXL
QaD31hIkNcowHwYDVR0jBBgwFoAUIuoezXQD1nnz1am8OIbtYN7c19wwHAYDVR0R
BBUwE4IRZ2l0bGFiLmV4YW1wbGUucnUwDQYJKoZIhvcNAQELBQADggEBACnWHQaX
AjXJWehrbqheOweR/spTLW9TH4GmlrbagP7WQMuoUTLQqY8zbyHbD0xZJofrkvl6
V5Cf+D8ZYis3COM+ReIGYuIGFIVsI0Hcc7cAYcgwGXtgr/9IwZELv70Thh+RKtHj
iLbmUsoMeq4v3IvGpcbWzvGtYxA97kZQwSxeyQaI7fYgMDWHJtPzFVr/JbpkWiWO
BHue8ORlooUNnWlU3/m7xxXjixC2L2vM0P1v8u09+071wkhLv2HEHQJdmFBvoSKd
JV1tdMCgpEXk7tnA7TlMXQVOBRThQLjUmMLZSuw3cLzfXrcm/4VdPUHzQJ9bnkGh
RcV8+9oGjBWASXU=
-----END CERTIFICATE-----
expiration          1629146802
issuing_ca          -----BEGIN CERTIFICATE-----
MIIDnDCCAoSgAwIBAgIUW03dnagkf0Jshc76zcexiPY7RyowDQYJKoZIhvcNAQEL
BQAwFTETMBEGA1UEAxMKZXhhbXBsZS5ydTAeFw0yMTA4MTUyMDI5MjBaFw0yNjA4
MTQyMDI5NTBaMCwxKjAoBgNVBAMTIWV4YW1wbGUucnUgSW50ZXJtZWRpYXRlIEF1
dGhvcml0eTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAOBYAQldlJ2S
noFaBY9jQUmBbb3KBfLzDWB7AaTHkvnSQx7jrDKGP2u7VofpdIwPCC0bXBQ2Xgig
Y6+grl5hUuswgYqSrawv3/C9RuM9MRLyhhxbdiWFY0HAoojJDXl2S+S35rTY6TII
QhVqM9DU2SL/rGCnyG1CnNzD74ejP8LGB2RRfJNbzTaoV76QFijm4dDWoLvADvJs
I9k/TDnkesHRCVyat4iDSIzUfGDsFB3G7jiM7wqGUIv+afAnobuFkqoIKtnkLZcJ
2QxNQpgp/+7g2duYC8DQJa1leYiFnATk69ahBapSKLI/mPkeYIuS6VkvkQj+T4mX
jETuNZDLpccCAwEAAaOBzDCByTAOBgNVHQ8BAf8EBAMCAQYwDwYDVR0TAQH/BAUw
AwEB/zAdBgNVHQ4EFgQUIuoezXQD1nnz1am8OIbtYN7c19wwHwYDVR0jBBgwFoAU
23wibMRwrgGeHZcK+NpF4E9yrlUwNwYIKwYBBQUHAQEEKzApMCcGCCsGAQUFBzAC
hhtodHRwOi8vdmF1bHQ6ODIwMC92MS9wa2kvY2EwLQYDVR0fBCYwJDAioCCgHoYc
aHR0cDovL3ZhdWx0OjgyMDAvdjEvcGtpL2NybDANBgkqhkiG9w0BAQsFAAOCAQEA
gaGtdOgAtJfJKIzzUQwmQy9y7TLCxkxDISrWpkY3G2Ydw0IyA5nMr2bof7VNpZLN
LP1LRv4+jK/fnVrG/s/Nm3TO2JlQawgnNdh4kBYovwiWPx/DPjUBnCBQxSjCc9wj
T66+CtjglZjzwULyRCqddEvTWsp+H0Xq8+2WZ2mN37ytrKGHbxWbW25a4RAp8vjb
HT72B+ZbvsVeEW4SXtkhKQcQDPwzKQDUk1XQN160wwMSPcaALeyLQCyoqH3pExLY
tL5Sz59LIFib6wuu3q/5WfTWk6awsZVwiBZdmfc/X23q9AsXNOtun9QQgaanh1G3
0eAi1mWwTj5MCdLsjNOB2Q==
-----END CERTIFICATE-----
private_key         -----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEA1A6YGMyr5PuQTz2S+7ge+Xt70ZFmmEo9/mgoC3ecqVgRNfHh
TVdTgC4vw8G3n+/744C7LT2C4WIasvY2gwHSHo53xJtQa3MMRpYMQgFHb3KMwekK
yXBSNfMVGDJPnfHGcIQ+bPTwkQ0GoQWi5tn+TpELP3vjVAnV1fEdeP5I2AkTHsX5
6euMS2Ep27hDKNx4TFXqo17z8KTXGXhy7pJq/a/s9itnt6rqXFkeZhW24EjORI4o
onn1uqIQepE393Z2tNSS7990i05MMA63Xz7jNPkqUK7A7jN1lND/L52hf6MYW9Cm
El0mZva8SAp0+Ez0ffMunHXgF/Ij7IhmoazewQIDAQABAoIBAQCAeQsK0009IG/Q
ojRfjrAtY/OjBt1KXIhsjnvcXq4qJrHepdli+woauWC0z7NJEaLgtUgxY3fcxov4
apSiEENVweir05EIWB5S0WtBvfaifZrrjB295u/XwZrBGxIDVcxstKIBbvAkYOjo
Ozrzc5TP0q4m2w7iBkdoI3lAqYZGYzaRHf5yHGMflhGGqBVWfIv82xJ4Zbt6Pil3
YpJePBBPbjrBHku2wjzMH2Jymu5FAAB7a6b75oBcnlmfP3DYy5RsroxRhZYJFYFq
KDc6F8bECta3l4wQl7yu2YzWjzqzgblZzjgIU8zZbRJqKHCuK0dGsSrrm1mRB7dB
7CExAGkxAoGBAPGqrNZeCc/ugGHW5vLZ5l5Z610iF6oAb5zYOkkPvJaAgCgEbDeX
osr8TA4bmlIwesLyPpiyNgwOyoftqiZFTyhbzutHVFeMnyzOn889vzAR1dozlWVH
JXJVqlBJojhrBsPRbGGiGLxfgG/VEdCoLyKq8ey4oL5v2Ju3F3Hqak7dAoGBAOCi
V8XvCixerPl60RgyvBVO/WF3FwyOy1DFypR0xteoc9+kMgN0xa70EPeYze3gz5n2
493AHkh+nuPeoaDeYy7bMR4rnB5JV91aeOXilw2+8Gk8q8cUq7sbkm/J/ilx5B5B
V/xdQs0YJHX9O8Sx8GGAa/x21b/TVChk35UPJIc1AoGBAM61l3sRGsGBlsyZXhgR
rAu+TCTweV9PWijFhy1hSYVOStBv4AS5LmUD4yYaFCkDEK5ZOJOxs6sip7gW4Pg1
Rp0V0mrLK2hrfud7oZRJk5RRXSN0BfCIJ46hmbltElXBrhqmslbcqN3PrnN5w/A/
O3oi0CYUUmIyFwwyUtp8kQv9AoGBAI6LFe2ZQThkn5j0MYkMcMOIy0q02mByoFvS
FznbXG5vC5CHzeDZkbPyVm33ff2MIdCOlYwapFzWVJc+qAu/8upB10pQ3BFv8xyY
k401Gyty1XXCNTLwUAU8etELOYgtKFd2mZGf0Ir63fAtUGcBjwsgBeY/tmWygX3c
fPBGmqRdAoGAXv/ntDQ5UHZmqngHgf68Uh1Qdx0jAWdwEpBG4jr5F+KtJSF1KX6Y
M5O7HKZ+1K2nKbp6dBCd1C6ZTD0ZqTpDZQoOOySCCLCJxnlZGEEfUGPQZdqCO0iP
Xq3wwMAvw7xgBtd61Ah9MI7CJBazG5Y5OG9OgqRD3IDKygIsVA9wDdM=
-----END RSA PRIVATE KEY-----
private_key_type    rsa
serial_number       50:8b:bb:5c:b5:28:6d:47:23:7a:65:f4:2e:af:6b:8e:04:4d:03:52

$ vault write pki_int/revoke serial_number="50:8b:bb:5c:b5:28:6d:47:23:7a:65:f4:2e:af:6b:8e:04:4d:03:52"
Key                        Value
---                        -----
revocation_time            1629060567
revocation_time_rfc3339    2021-08-15T20:49:27.435136408Z



