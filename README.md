# Задание:
1. Подготовить Docker-образы ElasticSearch, Kibana, Logstash, Filebeat 6.7.2 и 7.7.0 версий
2. С помощью ansible уметь моментально разворачивать стек ELKF обоих версий
3. Filebeat:
  - собрать логи из файла test.log
  - из контейнера filebeat с помощью module: docker
  - написать 2 конфига для output: в файл и в logstash
4. Logstash
- настроить input на прием через порт
- в filter:
	- обработать логи из test.log (разделить строку на следующие поля: Time, Priority, Logger, RandomNumber, RandomSymbols, Message)
	- из логов контейнера filebeat: убрать лишние поля (log.file.path, prospector.type, docker.container.id, docker.container.image); переименовать: host.name => host, message => Message; добавить в tags значение из docker.container.name (убрать соответственно docker.container.name)
- в output логи из test.log отправить в ElasticSearch в индекс test-%Y.%m.%d, а логи файлбита записать в файл logstash-output.log
5. В ElasticSearch подготовить template для индекса test-* для 6.7.2 и 7.7.0 версий.
6. В Kibana:
- сохранить поисковый запрос по индексу test-* за последние 12 часов, где выведены все поля, обработанные в Logstash
- создать визуализацию, в которой указывается кол-во документов для каждого значения поля Priority
- такую же визуализацию, но уже для поля Logger
- создать dashboard, в котором будут содержаться все выше перечисленные запросы и визуализации
# Команды для запуска

#### Клонирование репозитория
```bash
git clone https://github.com/studentNV/test_task.git
cd test_task
```
#### Развертка стека ELKF версии 6.7.2
```bash
ansible-playbook --extra-vars "docker_image_version=6.7.2" -i inventory.yml site.yml -t create_containers -D
```
#### Развертка стека ELKF версии 7.7.0
```bash
ansible-playbook --extra-vars "docker_image_version=7.7.0" -i inventory.yml site.yml -t create_containers -D
```
После развертки **web** интерфейс будет доступен по **url** http://locahost:5601/

#### Остановка и удаление всех контейнеров ELKF
```bash
ansible-playbook -i inventory.yml site.yml -t stop_and_delete_containers -D
```
На хостовой машине предполагается предустановленные следующие программы:
- Docker version 24.0.2
- Ansible [core 2.14.6]
	- python version = 3.10.6
	-  jinja version = 3.0.3

Тестировал на данных версиях и с ОС debian 22.04.2 LTS

# Схема работы
Исходя из задания, я построил схему для понимания того, что именно требуется развернуть.
![image](https://github.com/studentNV/test_task/assets/95025513/6287b535-2591-4146-9660-6a514f16d561)
# Пояснения
- Используется два контейнера с образом **filebeat**, потому что как я понял у **filebeat** может быть только один **output** -> https://discuss.elastic.co/t/configure-file-beat-to-multiple-output/235471, а по заданию `3.3` **output** нужно два.
- Файл `.logs_file/logs_file_filebeat/filebeat` - это необработанные логи с контейнера `filebeat_logstash`, задание `3.3`, на схеме под 3 номером.
- Файл `.logs_file/logs_file_logstah/logstash-output.log` - это логи контейнера `filebeat_logstash` задание `4.2.2`, обработанные **logshash**, задание `4.3`, на схеме под 7 номером.

# Пример работы программы
### ELKF версии 6.7.2
![image](https://github.com/studentNV/test_task/assets/95025513/2636e3f5-9ef7-4be4-925c-3a2a992de722)

### ELKF версии 7.7.0
![image](https://github.com/studentNV/test_task/assets/95025513/aa9d009b-3d44-4f2e-a23b-d2e5eabe7a1d)
