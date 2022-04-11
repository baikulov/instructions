tags:
[telegram](https://github.com/search?q=user%3Abaikulov+repo%3Abaikulov%2Finstructions+tags%3A+telegram+in%3Afile&type=code),
[GitHub](https://github.com/search?q=user%3Abaikulov+repo%3Abaikulov%2Finstructions+tags%3A+GitHub+in%3Afile&type=code),
[monitoring](https://github.com/search?q=user%3Abaikulov+repo%3Abaikulov%2Finstructions+tags%3A+monitoring+in%3Afile&type=code)

# Как настроить оповещения из GitHub Actions в Telegram
Для того, чтобы настроить автоматические оповещения необходимо:
- Добавить секреты в репозиторий
- Добавить job в текущей workflow 
- Указать сообщение с использованием env

### Добавляем секреты в репозиторий

Добавить нужно всего два секрета:
**TELEGRAM_TO** - id чата,в который нужно послать оповещение
**TELEGRAM_TOKEN** - токен бота, который будет отправлять сообщения

Как узнать id чата или получить токен для бота можно нагуглить за пару минут. 

### Добавляем job в текущий workflow

Просто добавляем нужный код в yml-файл

```yml

 alert:  # произвольное название
    needs: [dbt]  # указываем зависимость от предыдущего задания
    name: Alert  # произвольное название
    runs-on: ubuntu-latest
    if: ${{ failure() }}  # условие запуска для задания. Если задание, от которого зависит текущее, завершилось с ошибкой
    steps:
      - name: Alert to telegram  # произвольное название
        uses: appleboy/telegram-action@master 
        with:
          to: ${{ secrets.TELEGRAM_TO }}  # Тут будет наш id чата, который мы указали ранее
          token: ${{ secrets.TELEGRAM_TOKEN }}  # тут наш токен, который мы указали ранее
          message: ...  # тут будет сообщение
```

### Указать сообщение с использованием env

Сообщение может быть произвольным. В код yml-файла добавляем ещё один параметр на один уровень с параметром токена

```yml
message: |
            GitHub Actions failed!

            ${{ github.actor }} created commit:
            Commit message: ${{ github.event.commits[0].message }}

            In workflow: ${{ 	github.workflow }}

            Repository: ${{ github.repository }}
            
            See changes: https://github.com/${{ github.repository }}/commit/${{github.sha}}
```

И получаем нечто подобное:
```text
GitHub Actions failed!

baikulov created commit:
Commit message: test alerting to tg

In workflow: dbt_tests

Repository: baikulov/snto_dbt

See changes: https://github.com/baikulov/snto_dbt/commit/463bf9fd6b62827bd87ebbaa93f4ff892f22dff3

GitHub (https://github.com/baikulov/snto_dbt/commit/463bf9fd6b62827bd87ebbaa93f4ff892f22dff3)
test alerting to tg · baikulov/snto_dbt@463bf9f
Contribute to baikulov/snto_dbt development by creating an account on GitHub.
```