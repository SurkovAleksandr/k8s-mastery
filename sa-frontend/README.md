## Starting the Web App Locally
` $ yarn start `
Без yarn - npm start

## Building the application
` $ yarn build `

## Building the container
` $ docker build -f Dockerfile -t $DOCKER_USER_ID/sentiment-analysis-frontend . `

Создание образа:
> docker build -t surkovhub/sa_front:1.0 .

Создание контейнера:
> docker run -d --name sa_front -p 8081:80 surkovhub/sa_front:1.0


## Running the container
` $ docker run -d -p 80:80 $DOCKER_USER_ID/sentiment-analysis-frontend `

## Pushing the container
` $ docker push $DOCKER_USER_ID/sentiment-analysis-frontend `


## Особенности настроек nginx
`/usr/share/nginx/mydomain/html` - директория, в которой находятся статические файлы для отображение через nginx.
`/etc/nginx/sites-enabled/default -> /etc/nginx/sites-available/default` - символическая ссылка на конфигурацию по умолчанию. Если эту ссылку удалить, то nginx будет использовать конфигурацию `/etc/nginx/conf.d/default.conf`, в которой указывается отображать содержимое из директории `/usr/share/nginx/html`.

После установки nginx директорий `/etc/nginx/sites-enabled` и `/etc/nginx/sites-available` нет. Надо создать эти директории. Во второй создать файл конфигурации и сделать символическую ссылку из директории `sites-enabled` на созданный файл конфигурации. 

## Особенности работы с minikube
После установки minikube и kubectl для запуска пода(например sa-frontend) надо его создать в k8s: 
`kubectl create -f sa-frontend-pod.yaml`

После этого, если **не** проброшены порты, то это можно сделать, но получается ошибка(это не лучший вариант, т.к. после перезагрузки minikube надо заново пробрасывать порты): 
`kubectl port-forward sa-frontend 88:80`
Unable to listen on port 88: Listeners failed to create with the following errors: [unable to create listener: Error listen tcp4 127.0.0.1:88: bind: permission denied unable to create listener: Error listen tcp6 [::1]:88: bind: permission denied]
error: unable to listen on any of the requested ports: [{88 80}]

Пытался найти приложение, которое использует 88 порт - не получилось(`sudo lsof -nP -i | grep 88`), поэтому изменил порт:
u-user@u-host:~/IdeaProjects/Kubernetes/k8s-mastery/resource-manifests$ `kubectl port-forward sa-frontend 8888:80`
Forwarding from 127.0.0.1:8888 -> 80
Forwarding from [::1]:8888 -> 80

Приведенный выше вариант пробрасывает порты только до одного пода. Чтобы работали оба пода надо создать сервис с типом LoadBalancer.
`kubectl create -f service-sa-frontend-lb.yaml`

Чтобы посмотреть порт k8s, на который надо слать запросы с хоста можно использовать команду(порт 30835):
```
$ kubectl get svc
NAME             TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
kubernetes       ClusterIP      10.96.0.1      <none>        443/TCP        2d20h
sa-frontend-lb   LoadBalancer   10.97.26.197   <pending>     80:30835/TCP   15s
```
Можно так же воспользоваться командой `minikube service list`:
|-------------|----------------|--------------|---------------------------|
|  NAMESPACE  |      NAME      | TARGET PORT  |            URL            |
|-------------|----------------|--------------|---------------------------|
| default     | kubernetes     | No node port |
| default     | sa-frontend-lb |           80 | http://192.168.49.2:30835 |
| default     | sa-web-app-lb  |           80 | http://192.168.49.2:30566 |
| kube-system | kube-dns       | No node port |
|-------------|----------------|--------------|---------------------------|

После создание сервиса можно запустить браузер с нужным URL(`sa-frontend-lb` название сервиса - свойство name):
`minikube service sa-frontend-lb`
