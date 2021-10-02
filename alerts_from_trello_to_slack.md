# Как настроить оповещения из Trello в Slack

Для того, чтобы настроить автоматические оповещения необходимо:
- Создать отдельный канал в Slack
- Подключить Butler к рабочей области с этим каналом
- Создать правила в разделе "Автоматизация"

### Правила для отправки оповещений

**При добавлении в карточку**
```
when I am added to a card, post message "*Пользователь `{username}` добавил Вас в задачу* \n \n *\"{triggercardname}\"* \n {triggercardlink}" to Slack channel "#trello_alerts" in workspace "SNTO" as Butler
```

**При упоминании в комментариях к карточке**
```
*Пользователь `{username}` упомянул Вас в комментарии к задаче* \n \n *"{triggercardname}"* \n  {triggercardlink}. \n \n *Текст комментария* \n "{commenttext} " \n \n *Ссылка на комментарий* \n {commentlink}
```

**При упоминании в описании к карточке**
```
*Пользователь `{username}` упомянул Вас в описании к задаче* \n \n *"{triggercardname}"* \n {triggercardlink}. \n \n *Текст описания* \n "{carddescription}"
```