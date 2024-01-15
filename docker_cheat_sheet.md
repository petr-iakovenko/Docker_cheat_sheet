
# Docker

--- 
#### *Команды Docker (лайф-хак)*
Для запуска команды в команде bash - `docker stop $(docker ps -q)` *- первой выполнится комманда в $() и подставит значения в текущую.*  
Работа с запущенным контейнером:   
`docker cp <путь_к_файлу> <имя_контейнера>:<путь_в_контейнере_куда_скопировать>` - копирование содержимого папки в контейнер. 

*Пример:  
`docker cp dir_host/ container_name:/app_container` - из папки локальный папки сисмтемы `dir_host/` скопируются все файлы в папку `/app_container` внутри контейнера `container_name`. Проверяем наличие файла в контейнере через `docker exec...`.*

---
---

### Команды Docker

---
#### + системные:
`docker system df` - выводит статистику по образам и контейнерам (занимаемая память и т.д.).

---
####  + c образами:
`docker images` или `docker image ls` - просмотреть список образов.*  

`docker rmi <образ> [образ...]` или `docker images rm <образ> [образ...]` *- удалить образ(ы).

---
####  + для работы с контейнерами:
`docker -pull <образ>` - скачать из репозитория образ (DockerHub).  

`docker run <образ>` - создать и запустить контейнер на основе указанного образа.  

`docker run --name <имя> <образ>` - создать и запустить контейнер и при создании присвоить имя контейнеру на основе указанного образа.   

`docker run --name <имя> -d <образ>` - создать и запустить в фоновом режиме контейнер и при создании присвоить имя контейнеру на основе указанного образа.  

`docker run --rm <образ>` - удалять контейнер после завершения его работы на основе указанного образа.  

`docker run -it <образ>` или `docker run -i -t <образ>` - создать и запустить контейнер и войти в интерактивный режим с контейнером на основе указанного образа.  

`docker ps` - вывести список только запущенных контейнеров.  

`docker ps -a` или `docker ps --all` - вывести список всех контейнеров.  

`docker ps -q` или `docker ps --quite` - вывести только id контейнеров.  

`docker stop <имя_контейнера>` или `docker stop <id_контейнера>` - остановка запущенного указанного контейнера.

`docker start <имя_контейнера>` или `docker start <id_контейнера>` - запуск остановленного указанного контейнера.  

`docker rm <имя_контейнера>` или `docker rm <id_контейнера>` - удалить указанный контейнер.  

`docker exec <имя_контейнера> <имя_команды> <id_контейнера>` или `docker exec <id_контейнера> <имя_команды>` - запустить команду в работающем контейнере.  

`docker exec -it <имя_контейнера> <имя_команды> <id_контейнера>` или `docker exec -it <id_контейнера> <имя_команды>` - войти в интерактивный режим в запущенный контейнер с запуском указанной команды.  

*Пример:  
`docker exec -it <id_контейнера> bash` - войти в интерактивный режим в запущенный контейнер с запуском оболочки bash.*  

Для выхода из bash запущенного контейнера: `exit` 

`docker commit <контейнер>` - создать образ на основе контейнера.  

`docker images rm <имя_контейнера> <id_контейнера>` - удалить образ(ы).  
 
`docker commit <имя_контейнера> <имя_нового_образа> <тэг>` - создать образ с указанным именем на основе изменений.



---
---
### DockerFile

---
#### + наполнение Dockerfile: 
`FROM <имя_образа>:<тег>` - имя образа из которого будет собираться новый образ.  

`RUN apt-get update && apt-get install -y vim wget curl git` - запуск скрипта установки и обновления `vim wget curl git`, `-y` - ОС по умолчанию соглашается на установку.`&&` - выполнить сразу следующую команду.  

`ADD ...` - добавляем файл(или ссылку) с нашей файловой системы в образ в указанный путь.  

*Пример:*  
``` 
FROM ubuntu:22.04 

WORKDIR /zip 
ADD archive.tar.xz 

WORKDIR /url 
ADD https://airflow.apache.org/docs/apache-airflow/2.4.0/docker-compose.yaml
```
*— добавление архива и файла по ссылке.*  

`ADD test.txt /my_dir/` - добавляем файл `test.txt` с нашей файловой системы в образ в указанный путь `/my_dir/`.  

`WORKDIR <./work/my_project>` - стартовая рабочая область при работе в интерактивном режиме контейнера.  
`COPY ...` - копирование указанного файла из указанной директории в образ для работы с файлом внутри будущего контейнера в рабочей области контейнера.

*Пример:  
`COPY <script.sh> </WORKDIR/script.sh>` - копирование `script.sh` из директории в которой находимся в образ для работы с файлом внутри будущего контейнера в рабочей области контейнера `WORKDIR`.*  

`RUN ...` - выполняем команду.  

*Пример:  
`RUN echo "Hello"` - вывести "Hello".  
`RUN touch hello.sh && echo "echo 'Hello from container'" > hello.sh` - создать файл `&&` записать в созданный файл.*  

`ENTRYPOINT ...` - запуск прописанной команды при запуске контейнера вывести "Hello".  
`CMD ...` - запуск прописанной команды при запуске контейнера.  

*Пример особенности работы `ENTRYPOINT` и `CMD`:*  
```
FROM ubuntu:22.04

COPY script.sh script.sh

ENTRYPOINT ["bash", "script.sh"]

CMD ["World"]
```
*- первая выполнится `ENTRYPOINT ["bash", "script.sh"]`, а в качестве аргумента по умолчанию в скрипт `script.sh` будет принято `CMD ["World"]`.*

*https://docs.docker.com/engine/reference/builder/#understand-how-cmd-and-entrypoint-interact - docs*

`VOLUME` - для переноса/сохранения файлов из контейнера в файловую систему.
- монтирование папок, находящихся в специально отведенном месте(хранилище docker) -`/var/lib/docker/volumes/` 
- используется, чтобы сохранить данные из контейнера  

`docker volume ls` - показывает все созданные volume.  
`docker volume create <имя>` - создать volume с именем `<имя>`.  
`docker volume rm <имя>` - удалить volume с именем `<имя>`.  
`docker volume prune` - удалить все не используемые volume.
```
docker run --rm -d -v <имя_volume>:<путь_в_контейнере> <имя_образа>
```

*Пример с VOLUME* 
```
name_user@Macbook:~/Files$ docker run --rm -d -v todo_result_vol:/app/todo_result tg_bot_files  
```
*- прокидывание содержимого папки-volume `todo_result_vol` через volume в папку `/app/todo_result`при создании контейнера из образа `tg_bot_files`, при этом данный volume `todo_result_vol` будет храниться на локальной машине и в случае удаления контейнера данный volume `todo_result_vol` присвоется к новому контейнеру образа`tg_bot_files` при указывании присваивания volume как в примере выше.*  
`docker volume inspect <имя_volume>` - выводить json с данными по volume, где в `mountpoint` показан абсолютный путь к volume. По нему можно попасть только с правами **root** `sudo ls <путь>` / `sudo cat ls <путь/имя_файла>`.


`BIND MOUNT` - для переноса секретных файлов из файловой системы в контейнер.
- монтирование файлов / папок, который находятся в любом месте контейнера. То есть при поднятии контейнере перенести файл(указывается полный путь) в контейнер. 
- используется, чтобы прокинуть файлы / папки в контейнер
```
docker run --rm -d -v <путь_на_хосте>:<путь_в_контейнере> <имя_образа>

docker run --rm -d -v <путь_на_хосте>:<путь_в_контейнере>:ro <имя_образа>
```
-`:ro` оставляет возможность только читать данные без возможности редактирования и удаления.  


*Пример с BIND MOUNT*  
```
name_user@Macbook:~/Files$ docker run --rm -d -v $(pwd)/todo_result:/app/todo_result tg_bot_files  
```

*- прокидывание ВСЕГО содержимого папки `/todo_result` через bind mount в папку `/app/todo_result`при создании контейнера из образа `tg_happy_files`*  

```
name_user@Macbook:~/Files$ docker run --rm -d -v $(pwd)/todo_result:/app/todo_result:ro tg_bot_files  
```

**отличия VOLUME и BIND MOUNT** 
- VOLUMES не зависят от хоста с точки зрения прописывания обсолютного пути, который может поменяться(Mac, Win)
- VOLUMES более безопасны
- VOLUMES находятся в специально отведенном месте
- VOLUMES позволяют связывать только папки
- BIND MOUNT используется для передачи в контейнер токенов и других секретных данных
- BIND MOUNT способен навредить защищенным файлам системы если имеет к ней доступ (docker контейнер работает в root)  

`EXPOSE` - указывает на связь между хостом и контейнеров через прописывание портов (в командах `-p`, в слоях обозначение `EXPOSE`)
```
docker run -p <порт_на_хосте>:<порт_в_контейнере> <образ>

docker run -p <IP_адрес_на_хосте>:<порт_на_хосте>:<порт_в_контейнере> <образ> 

docker run -p <порт_на_хосте_1>:<порт_в_контейнере_1> -p <порт_на_хосте_2>:<порт_в_контейнере_2> ...
```
*- прокидывание портов до нескольких раз с помощью `-p`, по умолчанию IP-адрес на хосте задается 0.0.0.0*

`ENV` - объявление переменных в контейнере (в командах `-e`, в слоях обозначение `ENV`)
```
docker run -e <НАЗВАНИЕ_ПЕРЕМЕННОЙ>=<значение> <образ>
```

*Пример:*
```
APP_TOKEN = os.environ.get("APP_TOKEN")
```
*- запись в Python, для получения токена из `ENV`* 
```
docker run --rm -it -e APP_TOKEN=x ....
```
*- где `-e` является объявлением переменной `TOKEN=x`.  
в терминале можно прочитать значение `x` с помощью `echo $TOKEN`.*  


#### + прописываение сетей для взаимодействия между контейнерами и хостом
`docker network ls` - получение списка сетей(в т.ч. кастомных). 
```
docker run -d --rm --name <имя_конетйнера> --net=<тип_сети> <имя_образа>
```
*- где `--net=<тип_сети>` указывает на тип сети*  
**Виды сетей**  
`none` -  .  
`host` -  .   
`bridge` - (по умолчанию) неудобно, что приходится выяснять ip-адреса контейнеров.  
**Кастомные сети**  
`docker inspect <название_или_ID_объекта>` — получить информацию об объектах докера (контейнер, образ, вольюм, сеть)
`docker network create <имя_сети>` - создает новую сеть.  
`docker network rm <имя_сети>` - удалить указанную сеть.  
*Пример:  
Связь друх контейнеров с друг другом*
```
docker run --rm -d \
--name postgres_database \
--net=db_net \
-e POSTGRES_USER=user_docker \
-e POSTGRES_PASSWORD=user_docker \
-e POSTGRES_DB=user_docker \
postgres:latest 
```
*- разворачиваем БД Postgres в кастомной сети `db_net`. Не указывали порт*
```
docker run --rm -d \
--name back_end \
--net=db_net \
-p 8000:8000 \ 
-e HOST=postgres_database \
6_back
```
*- разворачиваем бэк с указыванием сети `db_net`. `HOST=postgres_database` переменная окружения(имя хоста) для коннекта к БД (строчка из скрипта для коннекта к БД -`host:os.environ.get("HOST"),`).  
Пример взят из https://lab.karpov.courses/learning/102/ 6 урок 7 лекция)*  
Полезные ссылки по сетям docker:  
*https://docs.docker.com/network/  - docs*   
*https://habr.com/ru/articles/333874/*


---

#### + команда для сборки образа Docker:  

`touch .dockerignore` - создаем ignore(токены, ключи,  лишние файлы) при формировании образа.
`docker build -t <имя_образа>:<тэг> .<путь_к_Dockerfile>` - собрать образ с указанным именем и тегом.  
*ВАЖНО! В вышеописанной команде Dockerfile не должен называться по другому!*  


---
---
### Команды bash

---
#### + работа с файлом:
`touch <имя_файла>` - создать файл.  

`mv <текущее_имя_файла> <новое_имя_файла>` - переименовать файл находясь в директории с файлом.

`vi <имя_файла>` - открыть файл в редакторе vi.  

`pwd` - команда показывающая абсолютный путь к текущей директории.  
*Пример использования в командах:
`docker run --rm -d -v $(pwd)/name_container.....` - передача абсолютного пути с помощью `$()`*

---
#### + работа в редакторе `vi`:
`i`- ввод текста.   

`x`- удаление символа под курсором.  

`dd`- удаление строки под курсором.

`Esc`- выход из режима.  

`:wq!`- сохранение файла и выход из редактора`vi`.  

---
---
### Docker-compose  
*https://docs.docker.com/engine/reference/commandline/compose/ - docs*  
*https://docs.docker.com/compose/compose-file/compose-file-v3/ - docs*  
Инструмент для запуска многоконтейнерных приложений docker, описывающие как должны подниматься приложение и их взаимодействие.  
Для работы с Docker-compose необходимо находиться в той же папки с файлом. 
#### + команды Docker для  Docker-compose.yml:
`docker-compose up` - поднять контейнеры из файла Docker-compose.yml. 
`docker-compose up -d` — поднять контейнеры из файла Docker-compose.yml в фоновом режиме.
`docker-compose stop` — остановить поднятые контейнеры.
`docker-compose start` — запустить остановленные контейнеры.
`docker-compose ps` - вывод всех docker-compose.
`docker-compose down` - останавливает все docker-compose и удаляет.  
*Внимание! Все созданные volume останутся после команды `docker-compose down`*
#### + синтаксис .yml:
Состоит из `<ключ>:<значение>` и отступов для разделения на блоки(отступы).  

Примеры:   
Запись в .yml:   
```
name:Petr 
age:32  
```
Вид:  
`{'name': 'Petr', 'age': 32}`

Запись в .yml: 
```
name: Petr Olga  

    или  

name:
    Petr
    Olga  
```
Вид:  
`{'name': 'Petr Olga'}`

Запись в .yml:
```
name: |
    Petr
    Olga
```
Вид:  
`{'name': 'Petr\nOlga\n'}`

Запись в .yml:
```
name: 
    - Petr
    - Olga  
```
Вид:  
`{'name': ['Petr', 'Olga']}`

Запись в .yml:  
```
name: 
    - Petr: 32
    - Olga: 32
```
Вид:  
`{'name': [{'Petr': 32}, {'Olga': 32}]}`  

Запись в .yml:  
```
name: 
    - Petr: 32
      Olga: 32
    - Oleg: 32
      Yana: 32
```
Вид:  
`{'name': [{'Petr': 32, 'Olga': 32}, {'Oleg': 32, 'Yana': 32}]}`

#### + шаблоны в .yml:
`&` - обозначает "якорь" (то что используется несколько раз)
`<<: *` - ссылка на "якорь" ()

Запись в .yml связки `&` и `<<: *`:  
```
junior:
    &junior 
    position: junior
    salary: 55000
    
team:
    backend:
        - Peter:
            <<: *junior
        - Olga:
            <<: *junior    
```
Вид:  
```
{
    'junior': 
        {'position': 'junior', 'salary': 55000}  
}


{
    team': {
        'backend': [
            {'Peter': {'position': 'junior', 'salary': 55000}}, 
            {'Yana': {'position': 'junior', 'salary': 55000}}
        ]
    }
}
```

`healthcheck ... ` - инструкции, которые Docker может использовать для проверки работоспособности запущенного контейнера. Используется в Docker-compose.   

*Пример из docker-compose:*
```
healthcheck:
    test: ["CMD", "curl", "--fail", "localhost:8000/test"]
    interval: 5s
    timeout: 5s
    retries: 3 
```

`deploy/replicas ...` - — указываем количество контейнеров у сервиса.
`depends_on ...` — определяем зависимость между сервисами.
`restart ...` — указываем поведение сервиса при падении.

**Документация по docker-compose:**

docker-compose - https://docs.docker.com/compose/compose-file/ 

build - https://docs.docker.com/compose/compose-file/#build

image - https://docs.docker.com/compose/compose-file/#image

container_name - https://docs.docker.com/compose/compose-file/#container_name

volumes - https://docs.docker.com/compose/compose-file/#volumes

environment - https://docs.docker.com/compose/compose-file/#environment

networks - https://docs.docker.com/compose/compose-file/#networks

ports - https://docs.docker.com/compose/compose-file/#ports

restart - https://docs.docker.com/compose/compose-file/#restart

deploy - https://docs.docker.com/compose/compose-file/#deploy

replicas - https://docs.docker.com/compose/compose-file/deploy/#replicas

depends_on - https://docs.docker.com/compose/compose-file/#depends_on

healthcheck - https://docs.docker.com/compose/compose-file/#healthcheck