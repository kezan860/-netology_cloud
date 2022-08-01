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

1. Для работы буду использовать инструмент werf

2. Создал [репозиторий](https://github.com/kezan860/netology_cloud/tree/master/01_cloud_1)

3. Создал образ на основе [Dockerfile]()

4. Интегрировал werf и Dockerfile [werf.yaml]()

5. Собрал образ
```

```



1. Создал [файл приложения](https://github.com/kezan860/netology_cloud/blob/master/01_cloud_1/.helm/app.yaml) деплоя (nginx)
1) Создал [файл проекта](https://github.com/kezan860/netology_cloud/blob/master/01_cloud_1/werf.yaml)
2) Засекретил файлы сертификата и ключа
```
✗ werf helm secret file encrypt .helm/secret/cert.crt -o .helm/secret/cert.crt
✗ werf helm secret file encrypt .helm/secret/cert.key -o .helm/secret/cert.key
```
2. Запустил деплой
```
✗ werf converge --dev --repo kezan86/secret
Version: v1.2.122+fix2
Using werf config render file: /private/var/folders/dt/h5ttnzf54pnf7tn07wwt9bym0000gn/T/werf-config-render-2871384593

┌ Getting client id for the http synchronization server
│ Using clientID "0f967aef-435e-4e77-bcf7-736021bbdd89" for http synchronization server at address https://synchronization.werf.io/0f967aef-435e-4e77-bcf7-736021bbdd89
└ Getting client id for the http synchronization server (2.76 seconds)

┌ ⛵ image secret
│ Use cache image for secret/from
│      name: kezan86/secret:64bfa901e61699564fa7f287d1c540cb37f963fa6f645689500c088d-1659371565955
│        id: f50d09d7c21f
│   created: 2022-08-01 19:32:45 +0300 MSK
│      size: 52.8 MiB
└ ⛵ image secret (0.00 seconds)

┌ Waiting for resources to become ready
│ ┌ Status progress
│ │ DEPLOYMENT                                                                                                         REPLICAS             AVAILABLE              UP-TO-DATE
│ │ secret                                                                                                             1/1                  1                      1
│ │ │   POD                                         READY           RESTARTS              STATUS
│ │ └── 5b689cb9f8-7ns2c                            1/1             0                     Running
│ │ RESOURCE                                                                             NAMESPACE                  CONDITION: CURRENT (DESIRED)
│ │ Secret/domain-cert                                                                   dev                        -
│ └ Status progress
└ Waiting for resources to become ready (3.02 seconds)

Release "secret" has been upgraded. Happy Helming!
NAME: secret
LAST DEPLOYED: Mon Aug  1 22:56:57 2022
LAST PHASE: rollout
LAST STAGE: 0
NAMESPACE: dev
STATUS: deployed
REVISION: 15
TEST SUITE: None
Running time 8.53 seconds
```
3. Посмотрел созданный под
```
✗ kubectl get po
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-6674876d49-7tpzv   1/1     Running   0          44s
```
4. Подключился к нему
```
✗ kubectl exec -ti nginx-deployment-6674876d49-7tpzv bash
kubectl exec [POD] [COMMAND] is DEPRECATED and will be removed in a future version. Use kubectl exec [POD] -- [COMMAND] instead.
root@nginx-deployment-6674876d49-7tpzv:/#
```
5. Вывел секрты которые были примонтированы
```
root@nginx-deployment-6674876d49-7tpzv:/# ls -l /etc/secret/
total 0
lrwxrwxrwx 1 root root 14 Aug  1 16:00 tls.crt -> ..data/tls.crt
lrwxrwxrwx 1 root root 14 Aug  1 16:00 tls.key -> ..data/tls.key

root@nginx-deployment-6674876d49-7tpzv:/# cat /etc/secret/tls.crt
-----BEGIN CERTIFICATE-----
MIIFbTCCA1WgAwIBAgIUMy8zk19mrr/SVwU6WpnqqvghhvAwDQYJKoZIhvcNAQEL
BQAwRjELMAkGA1UEBhMCUlUxDzANBgNVBAgMBk1vc2NvdzEPMA0GA1UEBwwGTW9z
Y293MRUwEwYDVQQDDAxzZXJ2ZXIubG9jYWwwHhcNMjIwODAxMTU0MjQ2WhcNMzIw
NzI5MTU0MjQ2WjBGMQswCQYDVQQGEwJSVTEPMA0GA1UECAwGTW9zY293MQ8wDQYD
VQQHDAZNb3Njb3cxFTATBgNVBAMMDHNlcnZlci5sb2NhbDCCAiIwDQYJKoZIhvcN
AQEBBQADggIPADCCAgoCggIBAKUVgqVxi9OrXdA3W4/iBohhtdCH+BqluqhCQAFm
e1JeekbYWXVkiHSHRCwSwmC25lNmKqkUbE0ENiDO2lufNbTZfeJGa1SAtFHbpr86
ic3unffrEZNEdC5ok7WQ6/hmcW0BSeFNgbYkgKOh8WJJtmZ0EDOElHkgox9kMux2
PVaEy68Rl4R9EIJRPy7Qf5TKeIZRIWdToGuuAcH0W3u2tSoHV1Ar8Qk6beYHdsl3
3QfPUWKt/yFLuYHmQ499bpmNiUgDtWc+UHqAWScD7R6Xh799JiA11aITScy4GdVt
q5/FjXi8ts04At6s94XluM2Aq6f6wzpbo0Jgagl8SAKxqhXRV1dMUJFeB8rNlqDA
rz/YXB8Ui2yygVjLiAE7TM4SfyOso1FBXHMcbX1vCAemFGY8sxZp25Q1eUYua6SI
mAuyZ+q4HmgakIrAUHuh/GjqwhNatu5+R76n80JALADbKByvcw/VFSPP9ZTIjuSc
ju76evh796euleEUYOnF+BTVuWpIDCN1APnhWwcI2MQpprkWE3t09MKBZftWbpN0
OV9fEX1gOHSOjt3F6Sd6vbZ1KbnWVY/nk7pU3VnHTEG99fmwvi6ziuU5cFsigR8k
+lRDMycF8rsobhU6bdsxcuaY5eBFoemmtjlJvTXi92sAnJJNaD1wBnSnwj3k9Sje
alnfAgMBAAGjUzBRMB0GA1UdDgQWBBRCVDvmanVSQhrP6cNBRUFOykEGWTAfBgNV
HSMEGDAWgBRCVDvmanVSQhrP6cNBRUFOykEGWTAPBgNVHRMBAf8EBTADAQH/MA0G
CSqGSIb3DQEBCwUAA4ICAQARpN24TL0PZTrgOg5tL0qfSEj6dsQi6UXvKR6JzlWj
6yxFSNfxPU9pCGAnUVkJRlSCx2yIDpDL6kS75hu1fOsY8MSg0EHSep8ru/liexcX
magYIvuP15isAFXstYjOFsEisjSy04hRkdLbkG2Fxi+5sRnuy8hVxWaokYzOowjw
prGI+Aou6RUI/Yn8rrbAM3v39uOrITgmKCwG7AolJ+FkeFw+soPQMOyYEeDWeBIG
TpH8jePhvS+tluLEQ7i8Noz2lKY2oTf8SEHCC/cZbXDktnu/tTLc6Ik7vz16TTPA
nV/cDtq/nGeRqDPD5fnBgpmIhblX0C0e3J2J+CxcgKTPa+0zKzxip5CX9eDiiKM4
dOo4PvqpO5GpIyFGCULmVNLFVqUukRJhKevxVt6cwkGGWdYLERLn5AT0sKLEphQQ
kfAMUmvUZev47ZQim16DkwufnJ5W5yzMVItlBmKG9FWTlrLKjx7AHrwuGQTY2MFL
HGmli0qL4whIj7euX74S5yrJSOpeOIijhCqySedCPsj0OqlKx2ysjHjMnWs68e4u
Yat2cRurOORW+rpDxreX5KUZtvXeRdbpj5wMYYrkgL0m10clOQxVdiUDC4M3J15O
Z139sZhXaZpKWCK7hYYbiDm3D517TAjPGEoXSF9KnKy2SycPpFPOKqoeh5zQNyAF
aw==
-----END CERTIFICATE-----
root@nginx-deployment-6674876d49-7tpzv:/# cat /etc/secret/tls.key
-----BEGIN RSA PRIVATE KEY-----
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
-----END RSA PRIVATE KEY-----
```
6. Вывел секрты которые были переменными окружения
```
root@nginx-deployment-6674876d49-7tpzv:/# echo ${TLS_CRT}
-----BEGIN CERTIFICATE----- MIIFbTCCA1WgAwIBAgIUMy8zk19mrr/SVwU6WpnqqvghhvAwDQYJKoZIhvcNAQEL BQAwRjELMAkGA1UEBhMCUlUxDzANBgNVBAgMBk1vc2NvdzEPMA0GA1UEBwwGTW9z Y293MRUwEwYDVQQDDAxzZXJ2ZXIubG9jYWwwHhcNMjIwODAxMTU0MjQ2WhcNMzIw NzI5MTU0MjQ2WjBGMQswCQYDVQQGEwJSVTEPMA0GA1UECAwGTW9zY293MQ8wDQYD VQQHDAZNb3Njb3cxFTATBgNVBAMMDHNlcnZlci5sb2NhbDCCAiIwDQYJKoZIhvcN AQEBBQADggIPADCCAgoCggIBAKUVgqVxi9OrXdA3W4/iBohhtdCH+BqluqhCQAFm e1JeekbYWXVkiHSHRCwSwmC25lNmKqkUbE0ENiDO2lufNbTZfeJGa1SAtFHbpr86 ic3unffrEZNEdC5ok7WQ6/hmcW0BSeFNgbYkgKOh8WJJtmZ0EDOElHkgox9kMux2 PVaEy68Rl4R9EIJRPy7Qf5TKeIZRIWdToGuuAcH0W3u2tSoHV1Ar8Qk6beYHdsl3 3QfPUWKt/yFLuYHmQ499bpmNiUgDtWc+UHqAWScD7R6Xh799JiA11aITScy4GdVt q5/FjXi8ts04At6s94XluM2Aq6f6wzpbo0Jgagl8SAKxqhXRV1dMUJFeB8rNlqDA rz/YXB8Ui2yygVjLiAE7TM4SfyOso1FBXHMcbX1vCAemFGY8sxZp25Q1eUYua6SI mAuyZ+q4HmgakIrAUHuh/GjqwhNatu5+R76n80JALADbKByvcw/VFSPP9ZTIjuSc ju76evh796euleEUYOnF+BTVuWpIDCN1APnhWwcI2MQpprkWE3t09MKBZftWbpN0 OV9fEX1gOHSOjt3F6Sd6vbZ1KbnWVY/nk7pU3VnHTEG99fmwvi6ziuU5cFsigR8k +lRDMycF8rsobhU6bdsxcuaY5eBFoemmtjlJvTXi92sAnJJNaD1wBnSnwj3k9Sje alnfAgMBAAGjUzBRMB0GA1UdDgQWBBRCVDvmanVSQhrP6cNBRUFOykEGWTAfBgNV HSMEGDAWgBRCVDvmanVSQhrP6cNBRUFOykEGWTAPBgNVHRMBAf8EBTADAQH/MA0G CSqGSIb3DQEBCwUAA4ICAQARpN24TL0PZTrgOg5tL0qfSEj6dsQi6UXvKR6JzlWj 6yxFSNfxPU9pCGAnUVkJRlSCx2yIDpDL6kS75hu1fOsY8MSg0EHSep8ru/liexcX magYIvuP15isAFXstYjOFsEisjSy04hRkdLbkG2Fxi+5sRnuy8hVxWaokYzOowjw prGI+Aou6RUI/Yn8rrbAM3v39uOrITgmKCwG7AolJ+FkeFw+soPQMOyYEeDWeBIG TpH8jePhvS+tluLEQ7i8Noz2lKY2oTf8SEHCC/cZbXDktnu/tTLc6Ik7vz16TTPA nV/cDtq/nGeRqDPD5fnBgpmIhblX0C0e3J2J+CxcgKTPa+0zKzxip5CX9eDiiKM4 dOo4PvqpO5GpIyFGCULmVNLFVqUukRJhKevxVt6cwkGGWdYLERLn5AT0sKLEphQQ kfAMUmvUZev47ZQim16DkwufnJ5W5yzMVItlBmKG9FWTlrLKjx7AHrwuGQTY2MFL HGmli0qL4whIj7euX74S5yrJSOpeOIijhCqySedCPsj0OqlKx2ysjHjMnWs68e4u Yat2cRurOORW+rpDxreX5KUZtvXeRdbpj5wMYYrkgL0m10clOQxVdiUDC4M3J15O Z139sZhXaZpKWCK7hYYbiDm3D517TAjPGEoXSF9KnKy2SycPpFPOKqoeh5zQNyAF aw== -----END CERTIFICATE-----

root@nginx-deployment-6674876d49-7tpzv:/# echo ${TLS_KEY}
-----BEGIN RSA PRIVATE KEY----- MIIJKAIBAAKCAgEApRWCpXGL06td0Ddbj+IGiGG10If4GqW6qEJAAWZ7Ul56RthZ dWSIdIdELBLCYLbmU2YqqRRsTQQ2IM7aW581tNl94kZrVIC0UdumvzqJze6d9+sR k0R0LmiTtZDr+GZxbQFJ4U2BtiSAo6HxYkm2ZnQQM4SUeSCjH2Qy7HY9VoTLrxGX hH0QglE/LtB/lMp4hlEhZ1Oga64BwfRbe7a1KgdXUCvxCTpt5gd2yXfdB89RYq3/ IUu5geZDj31umY2JSAO1Zz5QeoBZJwPtHpeHv30mIDXVohNJzLgZ1W2rn8WNeLy2 zTgC3qz3heW4zYCrp/rDOlujQmBqCXxIArGqFdFXV0xQkV4Hys2WoMCvP9hcHxSL bLKBWMuIATtMzhJ/I6yjUUFccxxtfW8IB6YUZjyzFmnblDV5Ri5rpIiYC7Jn6rge aBqQisBQe6H8aOrCE1q27n5HvqfzQkAsANsoHK9zD9UVI8/1lMiO5JyO7vp6+Hv3 p66V4RRg6cX4FNW5akgMI3UA+eFbBwjYxCmmuRYTe3T0woFl+1Zuk3Q5X18RfWA4 dI6O3cXpJ3q9tnUpudZVj+eTulTdWcdMQb31+bC+LrOK5TlwWyKBHyT6VEMzJwXy uyhuFTpt2zFy5pjl4EWh6aa2OUm9NeL3awCckk1oPXAGdKfCPeT1KN5qWd8CAwEA AQKCAgAHKfPNciv7N4iOrJhQmiJmcLcPIZdmsKJ1Asr8RJI9dNQhlunq6j3xsJ0I vJeq0sUUAW8Af15jyTcAHXnkV/hgrL+FvkCSHjO1Ca8mxUeNpDk+tPjCR0ozaV5f lrZmxStO66tlF5P1b4gVkcWD2mcL8yVw1uQKjZwGlLaRBGCNDJ46Lq1AlpzMyvHO +kVPE6o/Se4FKd/gTGDPJeeCat9Zv4/Obtm66Mo0HUbOX2E8IYcKnTphG4QlWvS7 mVnfWAEJGwAYRt//MOqtgsIbfb/qU2gAJdXrfqLJO4QDewrjmBMXljjolvGo8CCd suZeJKNOWtd2BNwE2WJAnyAQ3dzfm6NjzQXOfdyulgaL65C/9bLq7HphkzIh5pbr GRhhQOwCSAErp7vjdvrtbwz/okQ2qSUQvvE/sFhoODRVSFRVBcET9d2ErksbB5Y+ uiiIDGnCErnpxiIcYMjEynwNnMYsgeTnrbs/Qrn6JacAerwkjCUMWgl8ymmVGPl+ UTO+cCnrAolBgtww9upL7nerKyb7tDrVtMncF8kDlH0Jz0sB4qTFLGudo1Kiit1y DFkfCxSkhaw9K6ILsfUA28KrHtClXgOx7HRVRBMR6wRGjPsoefdEs8GJfbjEF01R 7954Db/LTSyTxpeOIZIQBitgBeAFaW32vJnXtthfP8n7bAwGoQKCAQEA2UjU6VPj 1WCNES0IJiQ6nAS0AapOdt3ghCKkeFdQJSWEBMMRMYIy9glHJLpX/X8KrrhpaXOc 6mOh0vX+jgSF/N0V3jEmCh1Y/48y856BC5elC0Kltx1b1HUgI7g+/f6VxJh19ooA B+vFdTootnSQgXlYXSVRzOWktkwEEkcDdLWfVc84iVkcuRuSnMkk3TTfWebLB30B gDEi8E1eBzd2d2FcYXxzdQ0e9OphVv3jUWDnzKrRb4zMva1zReYmQbOXdEukgk0f 63teY2+zJGMFl0ciBX2BrlrvPbrV6+Onhz39ooFInjUa5YifbVDCzTMq45ceRpGG zoY/Ffds0jjt6wKCAQEAwn+eEujrIhyURA0/Hp+WHjKi79YhsQsRPOFuCwRnIF1L wu32h26qd+is7kUpyKRlg4/tz551K2QVXCFC51kSTagzpCUymUpjjvPX8nvzqyev zAjxR7dNUn1FdsAUmjYDfaE15Xs1PxsDkShBUsiza4pm9Ctf+ODjNBT2BZlWKHQ0 Zt9MfD6+RdbIPxcJQuO7M3wWW4EJOlsRgz0WEjMhpTLYhD6E+cwb75ngQyYisazF lmFli+aVYGVLnM5DrZupSayDsqiNdYhuVhgfMnwsAUc70XLzsVH+vB9JNx7qb6Tp B/RBsW5UqJlCi6sj8pj+3c+qogYA3qCfYRArH1Ni3QKCAQBIGrcsxr3wbR3i+UKf BZ69b4Icm1t2bqK2tphFpxPdf9mTivgFqeMnamTFd2EDqkjtOh0g9VC5J17oFuHm VvHvu54qIb0x1hNWmzqZRZwlMKmAVxmO7psuob+MmvOsbfNdTgq3SYxBFKhuAmLI SV462P3Nyzid+gbyx78CIbav4CWD4EQur/esJc49YTJuhcEooEH3ti/tTmD9xW7S jkEt1I1HfHkD+tqvA6hRqebpdnL1pCnkDqFSwGBkbb98RhCYcxGge99/0Wy9KrAT /xg3308W61NBfMOvhHTA1scdRiEI8EYc2hqW2QOuzwIV/kjZRaiyWlCV8E63B2iB SosJAoIBAHDvdTP5tv55pcXWAz6e36XtNRsaNTn9+SZmp2USS2dJhQJM9ocxRR6X JkK8OkTc4G0CF84kblihpp12WsjGuZAKCOJZDwZfYWvSPyP3wUcypitNTfycfPNW 9gy7/7qDfodmIkt7vTFFWE7jFvsgur3JAXrp7LIwsvy85xXdMWAQCZVqN5k1PXqD +oZXs/L5FOwSM1Eync8arhKMV9J9ih3IZlxziPcbA2We7c9Px3lvntNw/mu7miT8 7GjChB28cxHqcBY/NNR4QckP/J1t6Idde2hk3QerWsSVTggJlYr6MK2DsNl7/QBg 7Xj4CMmG+QaG3MdzwPGERscvgjqQSqkCggEBAMIF5HsLUWzRThJkpVLTHe3In4SZ 2YM2B1j/+0boCSHGyuYMqu4ZG4iz4cO6F56lf+6RLzKn7xU1cGOiP0UtCBaW8Umg jsde8lf3dby1ZINtY5OV7xTsghMB6CI/RZKF33u6ZSPEKAdr0bm2He/XqztBRJ6n vxglg5s1hCNRuTyIydzKj/ifQorHkYu0AC5tko2jVnORoyBu0Y11u5SLB193W6iO /W4vVDbEiYut9OluuRwkzzV71xFPsiuvoz5flsAKe+3Vr4B9b/1S2nE2LOLTZffk kIBOF5paHrS3922/7y9+5T8ALLHA7p2+8Iogi0sI/zMu7vXS8FESqEOZt3s= -----END RSA PRIVATE KEY-----
```