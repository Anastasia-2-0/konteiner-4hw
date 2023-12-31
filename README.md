Создаем Dockerfile(уменьшение образа достигнуто за счет использования alpine и меньшего количества слоев:

![alt text](/images/1_0.jpg)

Создаем конфигурационный файл mariadb my.cnf(можно взять с просторов интернета или установить mariadb и взять из корневых папок):

![alt text](/images/1_1_1.jpg)
![alt text](/images/1_1_2.jpg)
![alt text](/images/1_1_3.jpg)
![alt text](/images/1_1_4.jpg)

Собираем образ и запускаем контейнер:

![alt text](/images/1_2.jpg)

![alt text](/images/1_3.jpg)

где флаг -v share_folder:/var/lib/mysql/ создает внешнею папку для хранение данных БД вне контейнера.

Для наглядности:

![alt text](/images/1_4.jpg)

Заходим в mariadb(как на прошлом семинаре заходили):

![alt text](/images/1_5.jpg)

к сожалению эта команда не сработала, попробуем иначе:

![alt text](/images/1_6.jpg)

Создали вновь пользователя и дали ему полные права для БД userdata и db1.

Открываем новый терминал и создаем phpmyadmin:

![alt text](/images/1_7.jpg)

Запустили phpmyadmin (в контейнере) и через веб проверили работоспособность:

![alt text](/images/1_8.jpg)

к сожалению в доступе к БД db1 было отказано, но userdata работает.

Вернемся в терминал и посмотрим появиться ли БД userdata:

![alt text](/images/1_9.jpg)

Какие испольховались команды:
Dockerfile:
# Используем базовый образ Alpine

FROM alpine:latest

# Устанавливаем и настраиваем mariadb

RUN apk update && apk add mariadb mariadb-client

RUN mkdir /run/mysqld && chown mysql /run/mysqld

COPY my.cnf /etc/mysql/my.cnf

# Дополнительно уменьшаем образ

RUN rm -rf /var/cache/apk/*

# Открываем порт 3306 для коммуникации с другими сущностями

EXPOSE 3306

# Запускаем mariadb

CMD ["mysqld_safe"]

# my.cnf
[mysqld]

# datadir=/data/mysql

# socket=/run/mysqld/mysql.sock

bind-address=0.0.0.0



# Запуск марии
sudo docker run -d -p 8085:3306 -v /path/to/external/folder:/data/mysql -e MARIADB_ROOT_PASSWORD=test123 --name my-mariadb my-mariadb

# Запуск phpmyadmin