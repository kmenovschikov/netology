**Задача 1**

Сценарий выполнения задачи:

- Установите docker и docker compose plugin на свою linux рабочую станцию или ВМ.

- Если dockerhub недоступен создайте файл /etc/docker/daemon.json с содержимым: {"registry-mirrors": \["https://mirror.gcr.io", "https://daocloud.io", "https://c.163.com/", "https://registry.docker-cn.com"\]}

- Зарегистрируйтесь и создайте публичный репозиторий с именем "custom-nginx" на [https://hub.docker.com](https://hub.docker.com/) (ТОЛЬКО ЕСЛИ У ВАС ЕСТЬ ДОСТУП);

- скачайте образ nginx:1.29.0;

- Создайте Dockerfile и реализуйте в нем замену дефолтной индекс-страницы(/usr/share/nginx/html/index.html), на файл index.html с содержимым:

\<html\>

\<head\>

Hey, Netology

\</head\>

\<body\>

\<h1\>I will be DevOps Engineer!\</h1\>

\</body\>

\</html\>

- Соберите и отправьте созданный образ в свой dockerhub-репозитории c tag 1.0.0 (ТОЛЬКО ЕСЛИ ЕСТЬ ДОСТУП).

- Предоставьте ответ в виде ссылки на <https://hub.docker.com/>\<username_repo\>/custom-nginx/general .

**Решение**

**sudo nano Dockerfile**

FROM nginx:1.29.0

COPY index.html /usr/share/nginx/html/index.html

**docker build -t kmenovschikov/custom-nginx:1.0.0 .**

**docker push kmenovschikov/custom-nginx:1.0.0**

[**https://hub.docker.com/repository/docker/kmenovschikov/custom-nginx/general**](https://hub.docker.com/repository/docker/kmenovschikov/custom-nginx/general)

**------------------------------------------------------------------------------------------------------------------------------------------**

**Задача 2**

1.  Запустите ваш образ custom-nginx:1.0.0 командой docker run в соответствии с требованиями:

- имя контейнера "ФИО-custom-nginx-t2"

- контейнер работает в фоне

- контейнер опубликован на порту хост системы 127.0.0.1:8080

2.  Не удаляя, переименуйте контейнер в "custom-nginx-t2"

3.  Выполните команду date +"%d-%m-%Y %T.%N %Z" ; sleep 0.150 ; docker ps ; ss -tlpn \| grep 127.0.0.1:8080 ; docker logs custom-nginx-t2 -n1 ; docker exec -it custom-nginx-t2 base64 /usr/share/nginx/html/index.html

4.  Убедитесь с помощью curl или веб браузера, что индекс-страница доступна.

В качестве ответа приложите скриншоты консоли, где видно все введенные команды и их вывод.

**Решение**

1.  docker run --name kmenovschikov-custom-nginx-t2 -d -p 127.0.0.1:8080:80 kmenovschikov/custom-nginx:1.0.0

2.  docker stop kmenovschikov-custom-nginx-t2

docker rename kmenovschikov-custom-nginx-t2 custom-nginx-t2

docker start custom-nginx-t2

![](./media/image1.png)

![](./media/image2.png)

**------------------------------------------------------------------------------------------------------------------------------------------**

**Задача 3**

1.  Воспользуйтесь docker help или google, чтобы узнать как подключиться к стандартному потоку ввода/вывода/ошибок контейнера "custom-nginx-t2".

2.  Подключитесь к контейнеру и нажмите комбинацию Ctrl-C.

3.  Выполните docker ps -a и объясните своими словами почему контейнер остановился.

4.  Перезапустите контейнер

5.  Зайдите в интерактивный терминал контейнера "custom-nginx-t2" с оболочкой bash.

6.  Установите любимый текстовый редактор(vim, nano итд) с помощью apt-get.

7.  Отредактируйте файл "/etc/nginx/conf.d/default.conf", заменив порт "listen 80" на "listen 81".

8.  Запомните(!) и выполните команду nginx -s reload, а затем внутри контейнера curl http://127.0.0.1:80 ; curl http://127.0.0.1:81.

9.  Выйдите из контейнера, набрав в консоли exit или Ctrl-D.

10. Проверьте вывод команд: ss -tlpn \| grep 127.0.0.1:8080 , docker port custom-nginx-t2, curl http://127.0.0.1:8080. Кратко объясните суть возникшей проблемы.

11. Удалите запущенный контейнер "custom-nginx-t2", не останавливая его.(воспользуйтесь --help или google)

В качестве ответа приложите скриншоты консоли, где видно все введенные команды и их вывод.

**Решение**

1.  docker attach custom-nginx-t2

Ctrl+C

> Когда вы нажимаете Ctrl+C, ваш терминал генерирует сигнал SIGINT и передаёт его процессу, который управляет контейнером. В Linux этот процесс имеет PID 1. По умолчанию этот сигнал означает прерывание (interrupt) — запрос на остановку. Если процесс не установил собственный обработчик сигнала, система по умолчанию завершает его работу, что приводит к остановке всего контейнера.

![](./media/image3.png)

2.  docker start custom-nginx-t2

docker exec -it custom-nginx-t2 bash

apt-get update && apt-get install nano

![](./media/image4.png)

![](./media/image5.png)

Nginx был перенастроен на порт 81, а контейнер пробрасывает 80-ый порт в хост-систему, поэтому возникает ошибка.

3.  docker rm -f custom-nginx-t2

**------------------------------------------------------------------------------------------------------------------------------------------**

**Задача 4**

- Запустите первый контейнер из образа ***centos*** c любым тегом в фоновом режиме, подключив папку текущий рабочий каталог \$(pwd) на хостовой машине в /data контейнера, используя ключ -v.

- Запустите второй контейнер из образа ***debian*** в фоновом режиме, подключив текущий рабочий каталог \$(pwd) в /data контейнера.

- Подключитесь к первому контейнеру с помощью docker exec и создайте текстовый файл любого содержания в /data.

- Добавьте ещё один файл в текущий каталог \$(pwd) на хостовой машине.

- Подключитесь во второй контейнер и отобразите листинг и содержание файлов в /data контейнера.

В качестве ответа приложите скриншоты консоли, где видно все введенные команды и их вывод.

**Решение**

docker run --name cent -d -v ./:/data centos:centos7 sleep infinity

docker exec cent touch /data/from_centos.txt

docker run --name debian -d -v ./:/data debian:latest sleep infinity

touch from_host.txt

docker exec debian ls /data

![](./media/image6.png)

------------------------------------------------------------------------------------------------------------------------------------------

**Задача 5**

1.  Создайте отдельную директорию(например /tmp/netology/docker/task5) и 2 файла внутри него. "compose.yaml" с содержимым:

version: "3"

services:

portainer:

network_mode: host

image: portainer/portainer-ce:latest

volumes:

\- /var/run/docker.sock:/var/run/docker.sock

"docker-compose.yaml" с содержимым:

version: "3"

services:

registry:

image: registry:2

ports:

\- "5000:5000"

И выполните команду "docker compose up -d". Какой из файлов был запущен и почему? (подсказка: <https://docs.docker.com/compose/compose-application-model/#the-compose-file> )

2.  Отредактируйте файл compose.yaml так, чтобы были запущенны оба файла. (подсказка: <https://docs.docker.com/compose/compose-file/14-include/>)

3.  Выполните в консоли вашей хостовой ОС необходимые команды чтобы залить образ custom-nginx как custom-nginx:latest в запущенное вами, локальное registry. Дополнительная документация: <https://distribution.github.io/distribution/about/deploying/>

4.  Откройте страницу "[https://127.0.0.1:9000](https://127.0.0.1:9000/)" и произведите начальную настройку portainer.(логин и пароль адмнистратора)

5.  Откройте страницу "<http://127.0.0.1:9000/#!/home>", выберите ваше local окружение. Перейдите на вкладку "stacks" и в "web editor" задеплойте следующий компоуз:

version: '3'

services:

nginx:

image: 127.0.0.1:5000/custom-nginx:latest

ports:

\- "9090:80"

6.  Перейдите на страницу "<http://127.0.0.1:9000/#!/2/docker/containers>", выберите контейнер с nginx и нажмите на кнопку "inspect". В представлении \<\> Tree разверните поле "Config" и сделайте скриншот от поля "AppArmorProfile" до "Driver".

7.  Удалите любой из манифестов компоуза(например compose.yaml). Выполните команду "docker compose up -d". Прочитайте warning, объясните суть предупреждения и выполните предложенное действие. Погасите compose-проект ОДНОЙ(обязательно!!) командой.

В качестве ответа приложите скриншоты консоли, где видно все введенные команды и их вывод, файл compose.yaml , скриншот portainer c задеплоенным компоузом.

**Решение**

1.  По умолчанию Docker Compose сначала ищет файл **compose.yaml**. И только если такого файла нет, он проверяет **docker-compose.yaml**

2.  

version: "3"

include:  
   
  \- docker-compose.yaml  

services:

portainer:

network_mode: host

image: portainer/portainer-ce:latest

volumes:

\- /var/run/docker.sock:/var/run/docker.sock

registry:

image: registry:2

ports:

\- "5000:5000"

3.  docker compose up -d

> docker tag kmenovschikov/custom-nginx:1.0.0 localhost:5000/custom-nginx
>
> docker push localhost:5000/custom-nginx:latest
>
> Настраиваем localhost:9000

![](./media/image7.png)

4.  Stacks – Add stack – Web editor

> version: "3"
>
> services:
>
> nginx:
>
> image: 127.0.0.1:5000/custom-nginx:latest
>
> ports:
>
> \- "9090:80"

![](./media/image8.png)

5.  Удаляем docker-compose.yaml

> docker compose up -d

![](./media/image11.png)

6.  docker compose down

![](./media/image10.png)
