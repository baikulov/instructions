<!-- добавляем не больше трёх тегов -->
tags:
[gcloud](https://github.com/search?q=user%3Abaikulov+repo%3Abaikulov%2Finstructions+tags%3A+gcloud+in%3Afile&type=code),
[bigquery](https://github.com/search?q=user%3Abaikulov+repo%3Abaikulov%2Finstructions+tags%3A+bigquery+in%3Afile&type=code)

<!-- добавляем картинку -->
![alt text](https://github.com/baikulov/instructions/blob/master/images/test_image.png)

# Мониторинг проекта в BigQiery

Для управления проектом в BigQuery необходимо настроить мониторинг по нескольким направлениям:
- контроль за исполнением запросов от пользователей и сервисных аккаунтов
- контроль за размером проекта, количеством датасетов и таблиц
- контролю за размерами таблиц, актуальностью данных в них # будет позже


## Контроль за исполнением запросов пользователей

Данный мониторинг поможет ответить на вопросы:
- какие sql-запросы пишут пользователи
- сколько стоит каждый запрос
- какие таблицы или запросы стоит оптимизировать
- кто является лидером по расходам

## Настройка экспорта логов в таблицу BigQuery

Находим в **Navigation Menu** раздел **Logging** и переходим в подраздел **Logs Router**.
Подробнее о настройк логов тут - https://cloud.google.com/logging/docs/export/configure_export_v2

<!-- картинка -->
![alt text](https://github.com/baikulov/instructions/blob/master/images/start_logging_bq.jpg)


Выбираем **CREATE SINK** и в пункте **Sink details** указываем название и описание нашего сборщика логов.
<!-- картинка -->
![alt text](https://github.com/baikulov/instructions/blob/master/images/sink_details.jpg)


Вторым шагом выбираем место назначения в **Sink destination**, это место куда будут складываться логи. Выбираем свой проект и датасет, в котором будет храниться таблица.
<!-- картинка -->
![alt text](https://github.com/baikulov/instructions/blob/master/images/sink_destionation.jpg)


На этапе **Choose logs to include in sink** мы указываем фильтры, позволяющие получать данные только по выполненным запросам в BigQuery

```
resource.type="bigquery_resource"
protoPayload.methodName="jobservice.jobcompleted"
```
<!-- картинка -->
![alt text](https://github.com/baikulov/instructions/blob/master/images/choose_logs.jpg)

После этого сохраняем наш Sink. Теперь в указанном вами датасете появится таблица **cloudaudit_googleapis_com_data_access** с нужными данными.


Последним этапом пишем запрос, позволяющий получить из этой таблицы нужные данные и сохраняем его как view

```sql
SELECT
    EXTRACT(DATETIME FROM timestamp AT TIME ZONE "Europe/Moscow") as datetime, # время выполнения запроса
	protopayload_auditlog.authenticationInfo.principalEmail as user, # аккаунт,совершивший запрос
    d.projectId as project, # проект, в котором находилась целевая таблица
    d.datasetId as dataset, # датасет, в котором находилась целевая таблица
    d.tableId as table, # целевая таблица
    cast(5.0* (protopayload_auditlog.servicedata_v1_bigquery.jobCompletedEvent.job.jobStatistics.totalProcessedBytes / 1000000000000) as numeric) as queryCostInUSD, # стоимость запроса в долларах из прайса 5$ за 1Tb
    protopayload_auditlog.servicedata_v1_bigquery.jobCompletedEvent.job.jobStatistics.totalBilledBytes, # кол-во оплачиваемых байт
    protopayload_auditlog.servicedata_v1_bigquery.jobCompletedEvent.job.jobStatistics.totalProcessedBytes, # кол-во прочитанных байт
    cast(protopayload_auditlog.servicedata_v1_bigquery.jobCompletedEvent.job.jobStatistics.totalProcessedBytes / 1000000 as numeric) as processed_mb, # кол-во прочитанных мегабайт
    cast(protopayload_auditlog.servicedata_v1_bigquery.jobCompletedEvent.job.jobStatistics.totalProcessedBytes / 1000000000 as numeric) as processed_gb, # кол-во прочитанных гигабайт
    cast(protopayload_auditlog.servicedata_v1_bigquery.jobCompletedEvent.job.jobStatistics.totalProcessedBytes / 1000000000000 as numeric) as processed_tb, # кол-во прочитанных террабайт
    protopayload_auditlog.servicedata_v1_bigquery.jobCompletedEvent.job.jobConfiguration.query.query # текст запроса
FROM `<ваш_проект>.<ваш_датасет>.cloudaudit_googleapis_com_data_access`
LEFT JOIN UNNEST(protopayload_auditlog.servicedata_v1_bigquery.jobCompletedEvent.job.jobStatistics.referencedTables) as d
WHERE 
	protopayload_auditlog.servicedata_v1_bigquery.jobCompletedEvent.job.jobStatistics.totalProcessedBytes 
				IS NOT NULL
ORDER BY timestamp Desc
```

Теперь эту таблицу можно подключить к визуализации.

### Контроль за размером таблиц и актуальностью данных в них

Для создания постоянной таблицы с метаданными по проекту необходимо выполнить следующий запрос:

```sql
# создаём переменные. Первую в качестве массива, чтобы потом применить к ней OFFSET
DECLARE
  schemas ARRAY<string>;
DECLARE
  query string;

# устанавливаем значения переменных. В качестве переменной schemas ставим результат запроса. А в качестве query выбираем первое значение из schemas
SET
  schemas = ARRAY(
  SELECT
    STRING_AGG( CONCAT("select * from `<your_project>.", schema_name, ".__TABLES__` "), "union all \n")
  FROM
    `<your_project>`.INFORMATION_SCHEMA.SCHEMATA);

SET
  query = schemas[OFFSET(0)];

# выполняем DDL для перезаписи таблицы. Выполняем весь запрос по расписанию и получаем актуальную таблицу
EXECUTE IMMEDIATE CONCAT("CREATE OR REPLACE TABLE `<your_project>.<example_dataset>.<your_table>` AS ", query);
```

Выполняем этот запрос и получаем результирующую таблицу с полями:
- **project_id**. Название проекта
- **dataset_id**. Название датасета
- **table_id**. Название таблицы
- **creation_time**. Время создания таблицы(timestamp формат)
- **last_modified_time**. Время последнего изменения таблицы(timestamp формат)
- **row_count**. Количество строк в таблице
- **size_bytes**. Размер таблицы в байтах
- **type**. Тип таблицы(table, view и т.д.)

Данную таблицу можно подключить к визуализации

### ERD-диаграмма слоя витрин

Для построения такой диаграммы быстро и без особых затрат надо воспользоваться кодом

```
SELECT
 table_name, ddl
FROM
 `<your_project>`.<dataset>.INFORMATION_SCHEMA.TABLES
```
Он вернёт DDL-таблиц в датасете, который потом можно импортировать в любой ERD-диаграммер(с предварительной обработкой из-за разницы в схемах данных)