## Домашнее задание к занятию «Конфигурация приложений»

## 

### Цель задания:

- в тестовой среде Kubernetes необходимо создать конфигурацию и продемонстрировать работу приложения.

### Чеклист готовности к домашнему заданию

  1. Установленное K8s-решение (например, MicroK8s).

  2. Установленный локальный kubectl.

  3. Редактор YAML-файлов с подключённым GitHub-репозиторием.

### Задание 1. Создать Deployment приложения и решить возникшую проблему с помощью ConfigMap. Добавить веб-страницу

  1. Создать Deployment приложения, состоящего из контейнеров nginx и multitool.

  2. Решить возникшую проблему с помощью ConfigMap.

  3. Продемонстрировать, что pod стартовал и оба конейнера работают.

  4. Сделать простую веб-страницу и подключить её к Nginx с помощью ConfigMap. Подключить Service и показать вывод curl или в браузере.

  5. Предоставить манифесты, а также скриншоты или вывод необходимых команд.

- - - - - 

### Решение:

  1. создаём ConfigMap, в него добавляем index.html

  2. применяем

    kubectl apply -f configmap.yaml

  3. производим проверку
    
    kubectl get configmap

  4. в результате создается Pod с двумя контейнерами, ConfigMap монтируется в volume, файл index.html подменяет страницу nginx

  5. применяем
    
    kubectl apply -f deployment.yaml

  6. проверяем все запущенные поды

    kubectl get pods

  7. видим, что запущены под с контейнерами nginx и multitool

  8. применяем

    kubectl apply -f service.yaml

  9. проверяем

    kubectl get svc

  10. проверяем curl http://192.168.49.2:30007


![1](https://github.com/Ivan-Shkutov/kuber_2.2/blob/main/1.png)

![2](https://github.com/Ivan-Shkutov/kuber_2.2/blob/main/2.png)

![3](https://github.com/Ivan-Shkutov/kuber_2.2/blob/main/3.png)

- - - - - 

### Задание 2. Создать приложение с вашей веб-страницей, доступной по HTTPS

  1. Создать Deployment приложения, состоящего из Nginx.

  2. Создать собственную веб-страницу и подключить её как ConfigMap к приложению.

  3. Выпустить самоподписной сертификат SSL. Создать Secret для использования сертификата.

  4. Создать Ingress и необходимый Service, подключить к нему SSL в вид. Продемонстировать доступ к приложению по HTTPS.

  5. Предоставить манифесты, а также скриншоты или вывод необходимых команд.

- - - - - 

### Решение:

  1. создаем ConfigMap, который будет монтироваться в /usr/share/nginx/html/index.html внутри контейнера

  2. применяем

    kubectl apply -f configmap.yaml
    kubectl get configmap
    
ConfigMap хранит файл index.html и мы можем изменять страницу, меняя ConfigMap, не трогая контейнер

  3. контейнер будет использовать образ Nginx, а страницу подключаем через volume

  4. применяем

    kubectl apply -f deployment.yaml
    kubectl get pods

  5. volumeMounts подключает ConfigMap к контейнеру, subPath: index.html позволяет примонтировать только файл, а не весь ConfigMap

  6. чтобы Ingress мог направлять трафик в Pod, создаем ClusterIP Service

    kubectl apply -f service.yaml
    kubectl get svc

  7. ClusterIP Service делает Pod доступным внутри кластера, Ingress будет проксировать трафик через этот Service

  8. создаем самоподписной SSL-сертификат, используем OpenSSL для генерации ключа и сертификата

  9. создаем Secret для TLS

  10. Secret хранит сертификат и ключ, TLS Ingress будет ссылаться на этот Secret для HTTPS

  11. создаем Ingress с TLS

    microk8s enable ingress

  12. применяем

    kubectl apply -f ingress.yaml
    kubectl get ingress

  13. ingressClassName: nginx указывает, что этот Ingress будет обрабатываться Nginx Controller, tls — указывает секрет с сертификатом, rules — маршрутизация по Host и path к Service

  14. настраиваем hosts для локального доступа

    добавляем запись в /etc/hosts, чтобы браузер понимал myapp.local

    192.168.49.2 myapp.local

  15. проверяем работу приложения

    curl -k https://myapp.local
    
  16. проверка

    kubectl get pods - Pod запущен, контейнер работает
    kubectl get svc - Service доступен
    kubectl get ingress - Ingress подключен, адрес указан.
    kubectl get secrets - TLS Secret есть.
    kubectl describe ingress - убедиться, что нет ошибок и TLS подключен.

![4](https://github.com/Ivan-Shkutov/kuber_2.2/blob/main/4.png)

![5](https://github.com/Ivan-Shkutov/kuber_2.2/blob/main/5.png)

![6](https://github.com/Ivan-Shkutov/kuber_2.2/blob/main/6.png)

![7](https://github.com/Ivan-Shkutov/kuber_2.2/blob/main/7.png)

![8](https://github.com/Ivan-Shkutov/kuber_2.2/blob/main/8.png)

- - - - - 
