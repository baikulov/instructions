# Как настроить CD с помощью webhook

### 1. apt install webhook - ставим библиотеку в Linux
Дока тут - https://github.com/adnanh/webhook

### 2. Создаём файл конфига
```bash
[
  {
    "id": "deploy",  // id ендпоинта
    "execute-command": "/home/ant/deploy.sh" // путь к исполняемому файлу
  }
]
```
### 3. Создаём файл deploy.sh

echo "Test" // какая-то команда

### 4. Запускаем конфиг в работу
webhook -hooks webhook.json -verbose

По умолчанию слушает порт 9000. Ендпоинт висит на адресе host:9000/hooks/{id}

### 5. настраиваем работу скрипта, как службу

```bash
cd /etc/systemd/system/
nano webhook.service // создаём конфиг для сервиса
```

Добавляем конфиг
```
[Unit]
Description=Webhook Service
After=network.target

[Service]
Type=simple
ExecStart=/path/to/bin/webhook -hooks /path/to/webhook.json -verbose
Restart=always
User=your_username
Group=your_group

[Install]
WantedBy=multi-user.target
```
### 6. Перезапускаем демона
```
sudo systemctl start webhook
sudo systemctl enable webhook
sudo systemctl daemon-reload
sudo systemctl restart webhook.service
sudo systemctl status webhook // проверяем статус
sudo journalctl -u webhook.service // проверяем логи
```
