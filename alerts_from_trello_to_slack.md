tags:
[slack](https://github.com/search?q=user%3Abaikulov+repo%3Abaikulov%2Finstructions+tags%3A+slack+in%3Afile&type=code),
[trello](https://github.com/search?q=user%3Abaikulov+repo%3Abaikulov%2Finstructions+tags%3A+trello+in%3Afile&type=code),
[monitoring](https://github.com/search?q=user%3Abaikulov+repo%3Abaikulov%2Finstructions+tags%3A+monitoring+in%3Afile&type=code)

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