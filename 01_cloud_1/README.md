# Домашнее задание к занятию "14.1 Создание и использование секретов"

## Задача 1: Работа с секретами через утилиту kubectl в установленном minikube

Выполните приведённые ниже команды в консоли, получите вывод команд. Сохраните
задачу 1 как справочный материал.

### Как создать секрет?

```
openssl genrsa -out cert.key 4096
openssl req -x509 -new -key cert.key -days 3650 -out cert.crt \
-subj '/C=RU/ST=Moscow/L=Moscow/CN=server.local'
kubectl create secret tls domain-cert --cert=certs/cert.crt --key=certs/cert.key
```

### Результат:

```
✗ openssl genrsa -out certs/cert.key 4096
Generating RSA private key, 4096 bit long modulus (2 primes)
...........................................................................................++++
.....................++++
e is 65537 (0x010001)

✗ openssl req -x509 -new -key certs/cert.key -days 3650 -out certs/cert.crt \
-subj '/C=RU/ST=Moscow/L=Moscow/CN=server.local'

✗ kubectl create secret tls domain-cert --cert=certs/cert.crt --key=certs/cert.key
secret/domain-cert created
```

### Как просмотреть список секретов?

```
kubectl get secrets
kubectl get secret
```

### Результат:

```
✗ kubectl get secret
NAME          TYPE                DATA   AGE
domain-cert   kubernetes.io/tls   2      34s
```

### Как просмотреть секрет?

```
kubectl get secret domain-cert
kubectl describe secret domain-cert
```

### Результат:

```
✗ kubectl get secret domain-cert
NAME          TYPE                DATA   AGE
domain-cert   kubernetes.io/tls   2      63s

✗ kubectl describe secret domain-cert
Name:         domain-cert
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  kubernetes.io/tls

Data
====
tls.crt:  1944 bytes
tls.key:  3243 bytes
```

### Как получить информацию в формате YAML и/или JSON?

```
kubectl get secret domain-cert -o yaml
kubectl get secret domain-cert -o json
```

### Результат:

```
✗ kubectl get secret domain-cert -o yaml
apiVersion: v1
data:
  tls.crt: ...

  tls.key: ...
kind: Secret
metadata:
  creationTimestamp: "2022-08-01T15:43:19Z"
  name: domain-cert
  namespace: default
  resourceVersion: "118203"
  uid: d4d44bfe-fa72-4d61-b104-9cd208ce1ff2
type: kubernetes.io/tls

✗ kubectl get secret domain-cert -o json
{
    "apiVersion": "v1",
    "data": {
        "tls.crt": "...",
        "tls.key": "..."
    },
    "kind": "Secret",
    "metadata": {
        "creationTimestamp": "2022-08-01T15:43:19Z",
        "name": "domain-cert",
        "namespace": "default",
        "resourceVersion": "118203",
        "uid": "d4d44bfe-fa72-4d61-b104-9cd208ce1ff2"
    },
    "type": "kubernetes.io/tls"
}
```

### Как выгрузить секрет и сохранить его в файл?

```
kubectl get secrets -o json > secrets.json
kubectl get secret domain-cert -o yaml > domain-cert.yml
```

### Результат:

```
✗ kubectl get secrets -o json > secrets.json

✗ cat secrets.json
{
    "apiVersion": "v1",
    "items": [
        {
            "apiVersion": "v1",
            "data": {
                "tls.crt": "...",
                "tls.key": "..."
            },
            "kind": "Secret",
            "metadata": {
                "creationTimestamp": "2022-08-01T15:43:19Z",
                "name": "domain-cert",
                "namespace": "default",
                "resourceVersion": "118203",
                "uid": "d4d44bfe-fa72-4d61-b104-9cd208ce1ff2"
            },
            "type": "kubernetes.io/tls"
        }
    ],
    "kind": "List",
    "metadata": {
        "resourceVersion": ""
    }
}

✗ kubectl get secret domain-cert -o yaml > domain-cert.yml

✗ cat domain-cert.yml
apiVersion: v1
data:
  tls.crt: ...
  tls.key: ...
kind: Secret
metadata:
  creationTimestamp: "2022-08-01T15:43:19Z"
  name: domain-cert
  namespace: default
  resourceVersion: "118203"
  uid: d4d44bfe-fa72-4d61-b104-9cd208ce1ff2
type: kubernetes.io/tls
```

### Как удалить секрет?

```
kubectl delete secret domain-cert
```

### Результат:

```
✗ kubectl delete secret domain-cert
secret "domain-cert" deleted
```

### Как загрузить секрет из файла?

```
kubectl apply -f domain-cert.yml
```

### Результат:

```
✗ kubectl apply -f domain-cert.yml
secret/domain-cert created
```

## Задача 2 (*): Работа с секретами внутри модуля

Выберите любимый образ контейнера, подключите секреты и проверьте их доступность
как в виде переменных окружения, так и в виде примонтированного тома.

### Ответ:

0. Для работы буду использовать инструмент werf

1. Создал [репозиторий](https://github.com/kezan860/netology_cloud/tree/master/01_cloud_1)


2. Запустил деплой

```
✗ werf converge --repo kezan86/secret
Version: v1.2.122+fix2
Using werf config render file: /private/var/folders/dt/h5ttnzf54pnf7tn07wwt9bym0000gn/T/werf-config-render-2357650771

┌ Getting client id for the http synchronization server
│ Using clientID "fa334969-add7-48b7-8360-c35177bdb090" for http synchronization server at address https://synchronization.werf.io/fa334969-add7-48b7-8360-c35177bdb090
└ Getting client id for the http synchronization server (2.52 seconds)

┌ ⛵ image app
│ Use cache image for app/from
│      name: kezan86/secret:64bfa901e61699564fa7f287d1c540cb37f963fa6f645689500c088d-1659371565955
│        id: f50d09d7c21f
│   created: 2022-08-01 19:32:45 +0300 MSK
│      size: 52.8 MiB
└ ⛵ image app (0.01 seconds)

┌ Waiting for resources to become ready
│ ┌ Status progress
│ │ DEPLOYMENT                                                                                                         REPLICAS             AVAILABLE              UP-TO-DATE
│ │ secret                                                                                                             1/1                  1                      1
│ │ │   POD                                         READY           RESTARTS              STATUS
│ │ └── 76b7bb996d-5b97b                            1/1             0                     Running
│ │ RESOURCE                                                                             NAMESPACE                  CONDITION: CURRENT (DESIRED)
│ │ Secret/domain-cert                                                                   dev                        -
│ └ Status progress
└ Waiting for resources to become ready (3.03 seconds)

Release "secret" has been upgraded. Happy Helming!
NAME: secret
LAST DEPLOYED: Mon Aug  1 23:58:32 2022
LAST PHASE: rollout
LAST STAGE: 0
NAMESPACE: dev
STATUS: deployed
REVISION: 9
TEST SUITE: None
Running time 8.39 seconds
```

3. Посмотрел созданный под

```
✗ kubectl -n dev get po
NAME                      READY   STATUS    RESTARTS   AGE
secret-76b7bb996d-5b97b   1/1     Running   0          46s
```

4. Подключился к нему

```
✗ kubectl -n dev exec -ti secret-76b7bb996d-5b97b bash
kubectl exec [POD] [COMMAND] is DEPRECATED and will be removed in a future version. Use kubectl exec [POD] -- [COMMAND] instead.
root@secret-76b7bb996d-5b97b:/#

```

5. Вывел секрты которые были примонтированы

```
root@secret-76b7bb996d-5b97b:/# ls -l /etc/secret/
total 0
lrwxrwxrwx 1 root root 14 Aug  1 20:58 tls.crt -> ..data/tls.crt
lrwxrwxrwx 1 root root 14 Aug  1 20:58 tls.key -> ..data/tls.key
root@secret-76b7bb996d-5b97b:/#

root@secret-76b7bb996d-5b97b:/# cat /etc/secret/tls.crt
-----BEGIN CERTIFICATE-----
...
-----END CERTIFICATE-----
root@secret-76b7bb996d-5b97b:/#

root@secret-76b7bb996d-5b97b:/# cat /etc/secret/tls.key
-----BEGIN RSA PRIVATE KEY-----
...
-----END RSA PRIVATE KEY-----
root@secret-76b7bb996d-5b97b:/#
```

6. Вывел секрты которые были переменными окружения

```
root@secret-76b7bb996d-5b97b:/# echo ${TLS_CRT}
-----BEGIN CERTIFICATE----- ... -----END CERTIFICATE-----
root@secret-76b7bb996d-5b97b:/#

root@secret-76b7bb996d-5b97b:/# echo ${TLS_KEY}
-----BEGIN RSA PRIVATE KEY----- ... -----END RSA PRIVATE KEY-----
root@secret-76b7bb996d-5b97b:/#
```