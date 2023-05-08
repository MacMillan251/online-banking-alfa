# Онлайн банк
Spring Boot/Spring Data/Spring Security/Hibernate/MySQL/REST

Проект имитирует систему онлайн-банкинга. Он позволяет регистрироваться/входить в систему, вносить/выводить деньги со счетов, добавлять/редактировать получателей,
переводить деньги между счетами и получателями, просматривать транзакции, назначать встречи.

Есть две роли - пользователь и администратор. 

В папке проекта находится sql дамп с предварительно заполненными данными. 
Имя пользователя и пароль для базы данных: root

Вы можете войти в систему с помощью:
User - password
Admin - password

## Шаги развертывания на Docker:

###### Скопировать содержимое Git на локальную машину

```
git clone https://github.com/ravikalla/online-bank.git && \
cd online-bank && \
git checkout master && \
```

###### Создайте образ Docker для приложения.
```
docker build -t macmillan251/alfa-online:latest .
```
###### Запустить MySQL в Docker контейнере
```
docker run -d --name=bankmysql -v /var/lib/mysql:/var/lib/mysql --env=MYSQL_ROOT_PASSWORD=root -p 3306:3306 mysql:5.7.22
```
###### Выполнить SQL-скрипты в MySQL
```
cd online-bank
docker exec -i bankmysql mysql -uroot -proot < sql_dump/onlinebanking.sql
```
###### Запустить Docker-образ приложения.
```
docker run -d --name cloudbank -p 8888:8888 --link bankmysql:localhost -t macmillan251/alfa-online:latest
```
Получите доступ к приложению, перейдя по URL "[http://localhost:8888!](http://localhost:8888)"

## Шаги развертывания без Docker:
###### Сборка приложения
```
mvn clean build
```
###### Настройка БД
 * Запустите [MySQL сервер!](https://dev.mysql.com/downloads/mysql/)
 * Используйте [MySQLWorkbench!](https://www.mysql.com/products/workbench/)
 * Запуск [SQL скриптов!](https://github.com/ravikalla/online-bank/blob/master/sql_dump/onlinebanking.sql) в базе данных MySQL

###### Запуск приложения
```
java -jar target/online-bank-0.0.1-SNAPSHOT.jar
```

## Настройка CI/CD:

###### Создайте образ Jenkins с Maven.
```
sudo chmod 777 /var/run/docker.sock && \
mkdir -p /root/jenkins_home && \
chmod -R 777 /root/jenkins_home && \
cp Dockerfile-Jenkins-Maven ../Dockerfile && \
cd .. && \
docker build -t macmillan251/jenkins-maven-docker:v0.1 .
```
###### Запуск сервера Jenkins на Docker
```
docker run --detach -v /var/run/docker.sock:/var/run/docker.sock -v $(which docker):$(which docker) --name jenkins -p 9080:8080 -p 50000:50000 -v /root/jenkins_home:/var/jenkins_home macmillan251/jenkins-maven-docker:v0.1
```
###### Установите проект "alfa-online-bank" в Jenkins:
 * Войдите в Jenkins и настройте проект конвейера с исходным кодом из [Ссылка на GIT-репо OnlineBank!](https://github.com/MacMillan251/online-banking-alfa.git)
 * Запустите задание для сборки и развертывания приложения
