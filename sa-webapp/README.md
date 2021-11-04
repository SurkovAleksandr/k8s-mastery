## Packaging the application
` $ mvn install`

## Running the application
` $ java -jar sentiment-analysis-web-0.0.1-SNAPSHOT.jar --sa.logic.api.url=http://localhost:5000 ` 

## Building the container
` $ docker build -f Dockerfile -t $DOCKER_USER_ID/sentiment-analysis-web-app . `

## Running the container
``` 
$ docker run -d -p 8080:8080 -e SA_LOGIC_API_URL='http://<container_ip or docker machine ip>:5000' $DOCKER_USER_ID/sentiment-analysis-web-app  
```
Создание образа:
> docker build -t surkovhub/sa_webapp:1.0 .

Создание контейнера:
> docker run -d --name sa_webapp -p 8080:8080 surkovhub/sa_webapp:1.0


#### Native docker support needs the Container IP
CONTAINER_IP: To forward messages to the sa-logic container we need to get  its IP. To do so execute:

` $ docker container list`

Copy the id of sa-logic container and execute:

` $ docker inspect <container_id> `

The Containers IP address is found under the property NetworkSettings.IPAddress, use it in the RUN command.

#### Docker Machine on a VM 
Get Docker Machine IP by executing:

` $ docker-machine ip `

Use this one in the command.


## Pushing the container
` $ docker push $DOCKER_USER_ID/sentiment-analysis-web-app `


## Замечания
- sa-webapp делает запросы к приложению sa-logic(Python приложение) по URL _http://localhost:5000_. Если sa-webapp будет запускаться в контейнере, а sa-logic будет запускаться не в контейнере, а на хосте, то URL надо изменить(смотреть переменную SA_LOGIC_API_URL). Как это делать можно посмотреть тут https://stackoverflow.com/questions/24319662/from-inside-of-a-docker-container-how-do-i-connect-to-the-localhost-of-the-mach
- 