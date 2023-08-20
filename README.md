# ContainL3

I. Устанавливаем Докер.
1) Обновляем списки пакетов:
  sudo apt update
2)Установите пакеты, которые позволят использовать репозиторий по HTTPS:
  sudo apt install apt-transport-https ca-certificates curl software-properties-common
3) Добавьте официальный GPG-ключ Docker:
  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
Для других дистрибутивов, замените URL на соответствующий.
4) Добавьте репозиторий Docker к списку источников пакетов:
  echo "deb [signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
5) Обновите список пакетов, чтобы включить информацию о пакетах Docker из добавленного репозитория:
  sudo apt update
6) Устанавливаем Docker:
  sudo apt install docker-ce
7) Добавьте вашего пользователя в группу docker, чтобы избежать использования sudo для запуска Docker команд:
  sudo usermod -aG docker $USER
8) Запускаем следующую команду, чтобы применить изменения в текущем сеансе:
  newgrp docker
9) Теперь вы должны быть готовы использовать Docker через терминал. Вы можете проверить его работу, выполнив команду:
  docker --version

II. Тестируем.
1) Запускаем контейнер с использованием образа "cowsay".
   docker run docker/whalesay cowsay Hello, Docker!
   docker run docker/whalesay cowsay -f elephant "Hello, Docker!"
   docker run docker/whalesay cowsay -f kangaroo "Hello, Docker!"
команды запустят контейнеры с различными рисунками животных с использованием cowsay.

III. Тестируем команды.
1) Создание и запуск контейнеров:
docker run: Запускает контейнер из образа.
docker start: Запускает остановленный контейнер.
docker stop: Останавливает работающий контейнер.
docker restart: Перезапускает контейнер.
docker exec: Выполняет команду внутри запущенного контейнера.
2) Управление контейнерами:
docker rm $(docker ps -aq): удалит все остановленные контейнеры
docker ps: Просмотр списка запущенных контейнеров.
docker ps -a: Просмотр списка всех контейнеров (включая остановленные).
docker rm: Удаляет контейнер.
docker logs: Просмотр логов контейнера.
) Работа с образами:
docker images: Просмотр списка образов.
docker pull: Загрузка образа с Docker Hub.
docker build: Сборка образа из Dockerfile.
docker rmi: Удаляет образ.

IV. Хранение данных в контейнерах Docker.
1) Хранение файлов в контейнерах Docker. 
  а) Запустим контейнер из образа Ubuntu и войдем в него:
    docker run -it -h GB --name gb-test ubuntu:22.10
  б) Посмотрим содержимое корневой директории:
    ls -l /
  в) Создадим новую директорию в корне:
    mkdir /example
  г) Создадим файл "passwords.txt" и добавим в него какие-либо данные.
    touch /example/passwords.txt
    echo "123test" >> /example/passwords.txt

2) Остановка контейнера и запуск его снова.
  docker stop gb-test
  docker start gb-test
  docker exec -it gb-test bash
  cat /example/passwords.txt
Наши данные сохранятся, так как мы не пересоздавали контейнер.

3) Удаление контейнера и создание его заново, используя те же команды:
  docker stop gb-test
  docker rm gb-test
  docker run -it -h GB --name gb-test ubuntu:22.10

В этот раз наши данные будут утеряны, так как контейнер был удален.

4) Использование внешнего хранилища.
  а) Создаем папку, которую мы будем монтировать в контейнер:
    mkdir ~/docker-mount-example
  б) В этой папке создаем файл test.txt и наполните его данными:
    nano docker-mount-example/test.txt с текстом "This is the host test.txt file" или
    echo "This is the host test.txt file" > ~/docker-mount-example/test.txt
   в) В директории my создайте файл test.txt, который также понадобится для монтирования в контейнер, но с другим
   содержимым:
     nano my/test.txt с текстом "This is the root test.txt file" или
     echo "This is the root test.txt file" > ~/test.txt
   г) Создаем контейнер из образа ubuntu:22.10 с именем хоста GB  и именем gb-test, смонтируем ранее созданную папку с
   хоста в контейнер:
    docker run -it -h GB --name gb-test -v ~/docker-mount-example:/container-mount ubuntu:22.10
   д) Посмотрим содержимое файла
     cat /container-mount/test.txt
     -This is the host test.txt file
   д) Смонтируем созданный ранее текстовый файл из директории my внутрь смонтированной папки в контейнере:
    docker run -it -h GB --name gb-test -v ~/docker-mount-example:/container-mount -v ~/test.txt:/container-mount/test.txt ubuntu:22.10
   е) Посмотрим содержимое текстового файла в контейнере:
  cat /container-mount/test.txt
  -This is the root test.txt file

Мы создали контейнер и монтировали папку docker-mount-example внутрь контейнера. Затем мы монтировали файл test.txt из  директории my внутрь этой папки в контейнере. При просмотре содержимого файла в контейнере, вы увидите данные из файла в директории my.
 
Мы рассмотрели, как монтировать папки и файлы в контейнерах Docker, и почему изменения внутри контейнера могут повлиять на файлы хостовой системы. Это позволяет эффективно управлять данными в контейнерах и избегать потери информации.
![L3_1](https://github.com/PavelE13/ContainL3/assets/94640966/a140ce7d-0817-43bf-bcf8-8fbeb727f6e5)
![L3_2](https://github.com/PavelE13/ContainL3/assets/94640966/7beb6cdb-69d2-48ae-9945-e8d4057509bf)
![L3_3](https://github.com/PavelE13/ContainL3/assets/94640966/c90ae90a-d2c2-4391-a30e-afcd485ea3d1)
![L3_4](https://github.com/PavelE13/ContainL3/assets/94640966/47c87c65-274a-4eb0-a329-dfc86e2c58f3)
![L3_5](https://github.com/PavelE13/ContainL3/assets/94640966/6e14fde4-2ab8-4289-8d00-b976f2b1bbcf)
Использование внешнего хранилища
![L3_6](https://github.com/PavelE13/ContainL3/assets/94640966/49bc88d5-e160-4e4a-8e60-b14e9e733a6b)
