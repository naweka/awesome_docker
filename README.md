# awesome docker commands

Решил собрать частоиспользуемые и просто полезные команды в cheatsheet какой-то

## 🔶 Подготовка среды

### 🔸 Создание `Dockerfile`

💡 Лучше, конечно, от рута не запускать
```
FROM python:3.9-slim-buster
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
CMD ["python", "main.py"]
```

### 🔸 Создание `docker-compose.yml`

```
services:
  app:
    build: .  # Dockerfile в текущей директории
    environment:
      - PYTHONUNBUFFERED=1 # Отключаем буферизацию вывода Python
```

## 🔶 Создание контейнера

Собрать + запустить — `Dockerfile` + `docker-compose.yml`, `docker-compose up --build -d`, где -d это detached _(не запускать в текущей консоли)_

Посмореть айдишку — `docker ps -a`, где -a это просмотр даже остановленных контейнеров

Создать образ на основе контейнера — `docker commit 285050f6f6ed img_name`

Сохранить образ на диск — `docker save -o path_to_image.tar img_name`

Загрузить образ с диска — `docker load -i path_to_image.tar`

Проверить наличие образа — `docker images`

Запустить контейнер — `docker run -ti -d -v /etc/timezone:/etc/timezone:ro -v /etc/localtime:/etc/localtime:ro --restart=unless-stopped img_name`, где --restart=unless-stopped это рестарт контейнера при рестарте ОС

## 🔶 Манипуляции с контейнером

Посмотреть логи _(консольный вывод)_ — `docker logs 285050f6f6ed -f`, где -f это follow, просмотр логов в реальном времени

Зайти в контейнер — `docker exec -ti 285050f6f6ed /bin/bash`

Скопировать файл/папку в контейнер — `docker cp path_to_file.bin 285050f6f6ed:/path_inside_container.bin`

Скопировать файл/папку из контейнера — `docker cp 285050f6f6ed:/path_inside_container.bin path_to_file.bin`

## 🔶 Частые кейсы

### 🔸 Зайти в «сломанный» контейнер

Часто встречается, что контейнер при старте запускает приложуху, которая сразу же падает и контейнер стопается.

В этом случае можно создать образ на основе содержимого контейнера и поменять ему entrypoint:

```
docker commit efc9c462478e temp_img
docker run -it -p 8080:8080 --entrypoint /bin/bash temp_img
```

### 🔸 Подключить левый контейнер к сетке

Для отладки контейнеров также может понадобиться, например, подсосаться к СУБД в другом контейнере с сеткой.

Можно добавить левый контейнер в сетку таким образом:

```
docker network connect network_name efc9c462478e
```

А найти сетку и убедиться в том, что СУБД именно в нужной сетке через

```
docker network list
docker network inspect network_name <-- выведет какие контейнеры тусят в этой сетке
```

### 🔸 Установить NET8

У меня Debian/Ubuntu (`cat /etc/os-release`), на котором `apt-get install -y dotnet-sdk-8.0` не мог найти дотнет, что бы я не делал.

Но решение нашлось:

```
apt-get install -y gpg
wget -O - https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor -o microsoft.asc.gpg
mv microsoft.asc.gpg /etc/apt/trusted.gpg.d/
wget https://packages.microsoft.com/config/ubuntu/22.04/prod.list
mv prod.list /etc/apt/sources.list.d/microsoft-prod.list
apt-get update && apt-get install -y dotnet-sdk-8.0
```
