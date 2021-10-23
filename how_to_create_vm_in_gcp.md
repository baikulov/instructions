tags:
[gcloud](https://github.com/search?q=user%3Abaikulov+repo%3Abaikulov%2Finstructions+tags%3A+gcloud+in%3Afile&type=code),
[instances](https://github.com/search?q=user%3Abaikulov+repo%3Abaikulov%2Finstructions+tags%3A+instances+in%3Afile&type=code)
# Как создать VM в GCP через gcloud CLI

Перед созданием VM необходимо определиться с её конфигурацией. Доступные опции указываются в виде флагов.
```
**--image-family=**<example>    
**--image-project=**<example>    
**--machine-type=**<example>    
**--zone=**<example>
**--boot-disk-size=**<10Gb>
**--tags=**<my_tag>
```

Необходимые значения можно выбрать среди списков, получаемых с помощью команд:

```
gcloud compute images list - список доступных images
gcloud compute zones list - список доступных гео-зон
gcloud compute machine-types list --zones=us-central1-a -список доступных типов VM. Флаг --zones нужен для уточнения количества строк.

```

**Виртуальную машину можно создать с помощью команды:**
```
gcloud compute instances create vm1  --image-family=ubuntu-1604-lts --image-project=ubuntu-os-cloud --machine-type=e2-medium --zone=us-central1-a --boot-disk-size=20Gb --tags=docker
```

**Чтобы остановить, запустить или удалить виртуальную машину нужны команды**
```bash
gcloud compute instances list
gcloud compute instances stop <vm_name>
gcloud compute instances start <vm_name>
gcloud compute instances delete <vm_name>
```

**Чтобы подключиться к виртуальной машине по SSH**
```
gcloud compute config-ssh (получить пример ssh запроса)
gcloud compute ssh --project "<my_project>" --zone "<zone>" "instance_name" (из WSL)
ssh r-server.us-central1-a.spry-compound-139714 (подключение SSH с доступом к папке)
sudo chown -R 79370 /home/79370/ (для сохранения изменений в папке)
```


**Чтобы создать инстанс с базой данных в Cloud SQL**
```
gcloud sql instances create mysql --database-version=MYSQL_5_7 --cpu=2 --memory=4GB --region=us-central1 --root-password=password1
```

### Полезные ссылки
[Справка по gcloud compute instances create](https://cloud.google.com/sdk/gcloud/reference/compute/instances/create)