# Установить Linux Oracle на VirtualBox:


Установить образ Linux

![image](https://github.com/user-attachments/assets/1d698457-fe0e-4e1f-9f55-6aad6c269ca4)

Выделить 4 ядра

Выделить 4096 МБ оперативной памяти

![image](https://github.com/user-attachments/assets/6dcc2669-aefc-470c-a133-163215089fbf)


# Установка docker

Устанавливает утилиту wget на систему

`sudo yum install wget`

![image](https://github.com/user-attachments/assets/3239b0dc-37c1-4c15-b1d3-1a7bfec95093)

Скачиваем файл репозитория

`sudo wget -P /etc/yum.repos.d/ https://download.docker.com/linux/centos/docker-ce.repo`

![image](https://github.com/user-attachments/assets/f329b74f-10dc-49b8-9b1c-26091a27e1cf)

Устанавливаем docker

`sudo yum install docker-ce docker-ce-cli containerd.io`

![image](https://github.com/user-attachments/assets/57d6f0d6-7a5a-4050-9570-f87852cdefaa)

Запускаем его и разрешаем автозапуск

`sudo systemctl enable docker --now`

![image](https://github.com/user-attachments/assets/f3ad9197-bfcb-4117-a77a-eaf1d22dbf35)


# Установка compose

Для начала нужно убедиться в наличии пакета curl

`sudo yum install curl`

![image](https://github.com/user-attachments/assets/d819eeeb-44a9-4a4a-8e97-c5666de0d3be)


Объявление переменной COMVER, полученной в результате curl запроса, хранящей в себе номер последней
версии Docker Compose

`COMVER=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep 'tag_name' | cut -d\" -f4)`

Теперь скачиваем скрипт docker-compose последней версии, используя объявленную ранее переменную и помещаем его в каталог /usr/bin

`sudo curl -L "https://github.com/docker/compose/releases/download/$COMVER/docker-compose-$(uname -s)-$(uname -m)" -o /usr/bin/docker-compose`

![image](https://github.com/user-attachments/assets/45d3058b-6975-497c-9da6-47606a2275e1)


Предоставление прав на выполнение файла docker-compose

`sudo chmod +x /usr/bin/docker-compose`

Проверка установленной версии Docker Compose

`sudo docker-compose --version`

![image](https://github.com/user-attachments/assets/e07a4840-7860-4552-8e8d-5901c4a93548)

# Делаем grafana

Установка git

`sudo yum install git`

![image](https://github.com/user-attachments/assets/1fafc932-0906-47cf-8f95-8d2a6c736b1e)


Этот код скачивает содержимое репозитория skl256/grafana_stack_for_docker

`sudo git clone https://github.com/skl256/grafana_stack_for_docker.git`

![image](https://github.com/user-attachments/assets/26c8955b-d03a-46c2-9eec-8b5d0e82f253)


Заходит в папку - cd

`cd grafana_stack_for_docker`

cd .. - возвращает в папку выше

![image](https://github.com/user-attachments/assets/5ed09383-32b1-4a8f-b940-44a9ff1cd5e4)


(После этого можно вставлять готовый docker-compose)

Cоздаем папки двумя разными способами

`sudo mkdir -p /mnt/common_volume/swarm/grafana/config`

`sudo mkdir -p /mnt/common_volume/grafana/{grafana-config,grafana-data,prometheus-data,loki-data,promtail-data}`

Выдаем права

`sudo chown -R $(id -u):$(id -g) {/mnt/common_volume/swarm/grafana/config,/mnt/common_volume/grafana}`

Создаем файл

`sudo touch /mnt/common_volume/grafana/grafana-config/grafana.ini`

Копирование файлов

`sudo cp config/* /mnt/common_volume/swarm/grafana/config/`

Переименовывание файла

`sudo mv grafana.yaml docker-compose.yaml`

![image](https://github.com/user-attachments/assets/7f01e5ce-8ce3-439d-9013-332e20f6dd41)


Собрать докер (нужно запускать из папки где docker-compose.yaml)

`sudo docker compose up -d`

Опустить докер - sudo docker compose stop

![image](https://github.com/user-attachments/assets/ba2421bc-44cb-4811-a1c8-817d87bff209)



# Начинаем чистку файлов

Команда открывает файл docker-compose.yaml в текстовом редакторе vi с правами суперпользователя

`sudo vi docker-compose.yaml`

Что-бы что-то изменить в тесковом редакторе нужно нажать insert на клавиатуре

Затем в docker-compose нужно вставить node-exporter и удалить ненужные файлы (но у нас уже вставлен готовый докер)

![image](https://github.com/user-attachments/assets/44d39597-94bb-4632-8e6c-f2f5ba51f9c0)

выйти не сохраняясь из vim - esc -> :q!

выйти сохраняясь из vim - esc -> :wq!

Заходим в другую папку 

`sudo cd /mnt/common_volume/swarm/grafana/config`

Открываем файл prometheus.yaml в текстовом редакторе vi с правами суперпользователя

`sudo vi prometheus.yaml`

![image](https://github.com/user-attachments/assets/9ed6287d-a3a6-47dd-a5ac-c5bb1af1cf40)


Далее нужно исправить targets: на exporter:9100

![image](https://github.com/user-attachments/assets/6fa47729-6b23-4deb-ab7f-e79109ed7e58)


# Делаем grafana на сайте

переходим на сайтlocalhost:3000
Пользователь и пароль GRAFANA:admin
Code graph:3000
Код прометеуса:http://prometheus:9090
в меню выбираем вкладку Dashboards и создаем Dashboard
ждем кнопку +Добавить визуализацию, а после «Настроить новый источник данных»
выбираем Prometheus
Связь
http://prometheus:9090
Аутентификация
Базовая аутентификация
Пользователь:admin
Пароль:admin
Нажимаем на Save & test и надо зелёную показать галочку
в меню выбираем вкладку Dashboards и создаем Dashboard
ждем кнопку «Импорт панели управления»
Найдите и импортируйте панели мониторинга для распространенных приложений на сайте grafana.com/dashboards: 1860 //ждем кнопку Load
Выбираем Prometheus, ждем кнопку «Импортировать».




# Делаем VictoriaMetrics

Для начала зайдем в нужную папку

`cd grafana_stack_for_docker`

Открываем docker-compose

`sudo vi docker-compose.yaml`

После prometheus вставляем vmagent (но у нас уже вставлен готовый докер)

![image](https://github.com/user-attachments/assets/dafc71d3-040c-43d8-84d4-36b568ecbaf8)

Ангелина помоги
виктория метрик 8428









