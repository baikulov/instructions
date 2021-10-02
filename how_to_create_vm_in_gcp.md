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
gcloud compute instances create vm1  --image-family=ubuntu-1604-lts --image-project=ubuntu-os-cloud --machine-type=f1-micro --zone=us-central1-a --boot-disk-size=20Gb --tags=docker
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
gcloud compute ssh --project "<my_project>" --zone "<zone>" "instance_name"
```