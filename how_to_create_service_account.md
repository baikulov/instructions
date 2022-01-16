tags:
[gcloud](https://github.com/search?q=user%3Abaikulov+repo%3Abaikulov%2Finstructions+tags%3A+gcloud+in%3Afile&type=code),
[bigquery](https://github.com/search?q=user%3Abaikulov+repo%3Abaikulov%2Finstructions+tags%3A+bigquery+in%3Afile&type=code)

# Как создать сервисный аккаунт в GCP

### Зачем нужен сервисный аккаунт?

Прежде всего для того, чтобы разграничить области доступа к проекту. Например, у вас есть ETL-сервис, собирающий данные из сторонних источников, BI-сервис ежедневно работающий с таблицами и какой-нибудь шедулер, гоняющий запросы по расписанию. Вы можете использовать во всех сервисах свой личный логин, а можете для каждого создать отдельный сервисный аккаунт. Кроме того, это позволит отследить затраты по каждому из сервисов.

### 1. Создание сервисного аккаунта

Первым шагом открываем **Navigation Menu** и переходим в раздел **Iam & Admin** где выбираем подраздел **Service Accounts**.

<!-- добавляем картинку -->
![alt text](https://github.com/baikulov/instructions/blob/master/images/service_account.png)

Жмём **CREATE SERVICE ACCOUNT** и попадаем в интерфейс создания аккаунтов

В разделе **Service account details** заполняем название и описание(опционально) будущего сервисного аккаунта.

Следующим шагом выбираем необходимые роли и права для аккаунта. Например, для BigQuery.

<!-- добавляем картинку -->
![alt text](https://github.com/baikulov/instructions/blob/master/images/service_account_create.png)


Жмём **DONE** и переходим в общий интерфейс сервисных аккаунтов.

Теперь нам нужно скачать json-файл с credentials. Для этого переходим внутрь созданного аккаунта и выбираем вкладку **KEYS**. Жмём **ADD KEY** и выбираем **Create new key**, оставляем опцию  **JSON** по умолчанию и сохраняем файл к себе на компьютер.
 