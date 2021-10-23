# Запуск Airflow и PostgreSQL БД на виртуальной машине GCP

### Создание виртуальной машины в GCP
Создание виртуальной машины полностью описано в файле **[Как создать VM в GCP через gcloud CLI]**(https://github.com/baikulov/instructions/blob/master/how_to_create_vm_in_gcp.md)

Необходимо установить Docker и Docker-Compose на сайте baikulov.pro

### Формирование docker-compose.yaml
Скачиваем репо https://github.com/bitnami/bitnami-docker-airflow
Настраиваем ssh и права доступа к директории
Перед запуском редактируем volume в yaml-файле и пробрасываем папку для dagов

### Запуск Airflow и построение DAG
Потестировать python-скрипт с подключением к источнику и складывать в базу
Потом вставить в DAG и настроить расписание
### 