# awesome_docker
Репо по командам для докера

🔸 Подготовка среды

Создание `Dockerfile`

```
FROM python:3.9-slim-buster
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
CMD ["python", "main.py"]
```

Создание `docker-compose.yml`

```
services:
  app:
    build: .  # Dockerfile в текущей директории
    environment:
      - PYTHONUNBUFFERED=1 # Отключаем буферизацию вывода Python
```

🔸 Создание контейнера

Собрать + запустить — `Dockerfile` + `docker-compose.yml`, `docker-compose up --build -d`, где -d это detached _(не запускать в текущей консоли)_

Посмореть айдишку — `docker ps -a`, где -a это просмотр даже остановленных контейнеров

Создать образ на основе контейнера — `docker commit 285050f6f6ed img_name`

Сохранить образ на диск — `docker save -o path_to_image.tar img_name`

Загрузить образ с диска — `docker load -i path_to_image.tar`

Проверить наличие образа — `docker images`

Запустить контейнер — `docker run -ti -d --restart=unless-stopped img_name`, где --restart=unless-stopped это рестарт контейнера при рестарте ОС

🔸 Манипуляции с контейнером

Посмотреть логи _(консольный вывод)_ — `docker logs 285050f6f6ed -f`, где -f это follow, просмотр логов в реальном времени

Зайти в контейнер — `docker exec -ti 285050f6f6ed /bin/bash`

Скопировать файл/папку в контейнер — `docker cp path_to_file.bin 285050f6f6ed:/path_inside_container.bin`

Скопировать файл/папку из контейнера — `docker cp 285050f6f6ed:/path_inside_container.bin path_to_file.bin`
