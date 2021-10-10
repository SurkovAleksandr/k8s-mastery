## Starting the Web App Locally
` $ yarn start `

## Building the application
` $ yarn build `

## Building the container
` $ docker build -f Dockerfile -t $DOCKER_USER_ID/sentiment-analysis-frontend . `

## Running the container
` $ docker run -d -p 80:80 $DOCKER_USER_ID/sentiment-analysis-frontend `

## Pushing the container
` $ docker push $DOCKER_USER_ID/sentiment-analysis-frontend `


## Особенности настроек nginx
`/usr/share/nginx/mydomain/html` - директория, в которой находятся статические файлы для отображение через nginx.
`/etc/nginx/sites-enabled/default -> /etc/nginx/sites-available/default` - символическая ссылка на конфигурацию по умолчанию. Если эту ссылку удалить, то nginx будет использовать конфигурацию `/etc/nginx/conf.d/default.conf`, в которой указывается отображать содержимое из директории `/usr/share/nginx/html`.

После установки nginx директорий `/etc/nginx/sites-enabled` и `/etc/nginx/sites-available` нет. Надо создать эти директории. Во второй создать файл конфигурации и сделать символическую ссылку из директории `sites-enabled` на созданный файл конфигурации. 

