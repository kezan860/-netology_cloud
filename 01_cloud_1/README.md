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

1) Файл [deployment.yaml](https://github.com/kezan860/netology_cloud/blob/master/01_cloud_1/.helm/templates/deployment.yaml)
2) Файл [ingress.yaml](https://github.com/kezan860/netology_cloud/blob/master/01_cloud_1/.helm/templates/ingress.yaml)
3) Файл [secret.yaml](https://github.com/kezan860/netology_cloud/blob/master/01_cloud_1/.helm/templates/secret.yml)
4) Файл [service.yaml](https://github.com/kezan860/netology_cloud/blob/master/01_cloud_1/.helm/templates/service.yaml)
5) Файл проекта [werf.yaml](https://github.com/kezan860/netology_cloud/blob/master/01_cloud_1/werf.yaml)

2. Создал `secret` для доступа к образам `Docker Hub`
```
$ kubectl create ns dev

$ kubectl -n secret create secret docker-registry registrysecret \
  --docker-server='https://index.docker.io/v1/' \
  --docker-username='kezan86' \
  --docker-password='ZenMax86!@'
secret/registrysecret created
```

3. Собрал образ из [Dockerfile](https://github.com/kezan860/netology_cloud/blob/master/01_cloud_1/Dockerfile)

```
werf build
Version: v1.2.122+fix2
Using werf config render file: /private/var/folders/dt/h5ttnzf54pnf7tn07wwt9bym0000gn/T/werf-config-render-1045382035

┌ ⛵ image app
│ Use cache image for app/dockerfile
│      name: secret:e5c6ebcd2718ccfe74d01069a0d758e03d5a2554155ccdc01be0daff-1659385361222
│        id: c477c448deee
│   created: 2022-08-01 23:22:41.2028235 +0300 MSK
│      size: 5.7 MiB
└ ⛵ image app (0.02 seconds)

Running time 0.97 seconds
```

4. Запустил деплой

```
✗ werf converge --repo kezan86/secret
Version: v1.2.122+fix2
Using werf config render file: /private/var/folders/dt/h5ttnzf54pnf7tn07wwt9bym0000gn/T/werf-config-render-3876218598

┌ Getting client id for the http synchronization server
│ Using clientID "5404e5c0-101f-40d9-88f3-a1d0d1901354" for http synchronization server at address https://synchronization.werf.io/5404e5c0-101f-40d9-88f3-a1d0d1901354
└ Getting client id for the http synchronization server (2.58 seconds)

WARNING: There is no way to ignore the Dockerfile due to docker limitation when building an image for a compressed context that reads from STDIN.
WARNING: To hide this message, remove the Dockerfile ignore rule from the ".dockerignore" or add an exception rule "!Dockerfile".
┌ ⛵ image app
│ Use cache image for app/dockerfile
│      name: kezan86/secret:e5c6ebcd2718ccfe74d01069a0d758e03d5a2554155ccdc01be0daff-1659385361222
│        id: c477c448deee
│   created: 2022-08-01 23:22:41 +0300 MSK
│      size: 2.9 MiB
└ ⛵ image app (0.01 seconds)

Release "secret" does not exist. Installing it now.

┌ Waiting for resources to become ready
│ ┌ Status progress
│ │ DEPLOYMENT                                                                                                         REPLICAS             AVAILABLE              UP-TO-DATE
│ │ secret                                                                                                             4/4                  4                      4
│ │ │   POD                                         READY           RESTARTS              STATUS
│ │ ├── 6f64466cd8-nl6dv                            1/1             0                     Running
│ │ ├── 6f64466cd8-qphn4                            1/1             0                     Running
│ │ ├── 6f64466cd8-v5kt2                            1/1             0                     Running
│ │ ├── 6f64466cd8-w6cb5                            1/1             0                     Running
│ │ RESOURCE                                                                             NAMESPACE                  CONDITION: CURRENT (DESIRED)
│ │ Secret/secret-tls                                                                    secret                     -
│ │ Service/secret                                                                       secret                     -
│ │ Ingress/secret                                                                       secret                     -
│ └ Status progress
└ Waiting for resources to become ready (3.02 seconds)

NAME: secret
LAST DEPLOYED: Tue Aug  2 00:59:27 2022
LAST PHASE: rollout
LAST STAGE: 0
NAMESPACE: secret
STATUS: deployed
REVISION: 1
TEST SUITE: None
Running time 7.35 seconds
```

5. Посмотрел созданный под

```
✗ kubectl -n secret get po
NAME                      READY   STATUS    RESTARTS   AGE
secret-6f64466cd8-nl6dv   1/1     Running   0          63s
secret-6f64466cd8-qphn4   1/1     Running   0          63s
secret-6f64466cd8-v5kt2   1/1     Running   0          63s
secret-6f64466cd8-w6cb5   1/1     Running   0          63s
```

6. Подключился к нему

```
✗ kubectl -n secret exec -ti secret-6f64466cd8-nl6dv sh
kubectl exec [POD] [COMMAND] is DEPRECATED and will be removed in a future version. Use kubectl exec [POD] -- [COMMAND] instead.
/app #

```

7. Вывел секрты которые были примонтированы

```
/app # ls -l /etc/secret/
total 0
lrwxrwxrwx    1 root     root            14 Aug  1 21:59 tls.crt -> ..data/tls.crt
lrwxrwxrwx    1 root     root            14 Aug  1 21:59 tls.key -> ..data/tls.key
/app #

/app # cat /etc/secret/tls.crt
-----BEGIN CERTIFICATE-----
...
-----END CERTIFICATE-----
/app #

/app # cat /etc/secret/tls.key
-----BEGIN RSA PRIVATE KEY-----
...
-----END RSA PRIVATE KEY-----
/app #
```

8. Вывел секрты которые были переменными окружения

```
/app # echo ${TLS_CRT}
-----BEGIN CERTIFICATE----- ... -----END CERTIFICATE-----
/app #

/app # echo ${TLS_KEY}
-----BEGIN RSA PRIVATE KEY----- ... -----END RSA PRIVATE KEY-----
/app #
```