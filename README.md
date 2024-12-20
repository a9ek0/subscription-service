# Subscription service

Микросервис управляющий пользователями Adobe
и предоставляющие сессионый доступ к подписке.
В рамках сессии пользователю (не Adobe) по мере расходования подписки
выдаются новые Adobe аккаунты.

В процессе работы сервис переодически проверяет наличие подписки у аккаунтов
закрепленных за активными сессиями и если подписка слетает,
то создает новый аккаунт и вызывает webhook,
что бы уведомить пользователя микросервиса

Сеовис подписок взаимодействует с
[Adobe API](https://github.com/Adobe-for-Alex/adobe-api)

# Как запустить проект?

```sh
$ git clone ...
$ pnpm i
$ pnpm dev
```

> [!WARNING]
> `pnpm dev` - поднимает необходимое окружение в Docker
> После работы стоит выполнить `pnpm dev:down`, что бы выключить это окружение

# Как сделать миграцию с Prisma?

> [!NOTE]
> Все действия должны производится в окружении разработки, которое поднимается коммандой `pnpm dev:up` или `pnpm dev`

- Меняешь `prisma/schema.prisma` как тебе необходимо
- Выполняешь `pnpm prisma:migrate:new`
- Меняешь сгенерированный SQL файл в `prisma/migrations/` как тебе необходимо (можешь и не менять, но учти, что Prisma любит дропать все подряд)
- Выполняешь `pnpm prisma:migrate:apply`

# API

## POST `/sessions`

Открыть новую сессию

### Response

```json
"some-session-id"
```

## GET `/sessions/{id}`

Получить данные аккаунта закрепленного за сессией

### Response

```json
{
  "email": "account-email",
  "password": "account-password"
}
```

## DELETE `/sessions/{id}`

Закрыть сессию

# Webhook

> [!NOTE]
> URL для webhook указывается через переменную
> окружения `SESSION_UPDATED_WEBHOOK_URL`

## POST `SESSION_UPDATED_WEBHOOK_URL`

Отправить пользователю новые данные аккаунты

### Body

```json
{
  "session": "some-session-id",
  "email": "new-account-email",
  "password": "new-account-password"
}
```

# Основные технологии

- [Express](https://www.npmjs.com/package/express)
- [Prisma](https://www.npmjs.com/package/prisma)

# Как внести свои измменения?

- Форкаешь репозиторий
- Клонишь свой форк
- Создаешь отдельную ветку для своиз изменений
(это важно для избежания гемороя при параллельной разработке нескольких фич)
- Комитишь изменения в свою ветку
- Оформляешь Pull Request
- Если твои изменения исправляют какие-то проблемы из раздела Issue,
то добавь их номера в описании PR в следующем виде:
```
Fixes #1
Fixes #2
Fixes #3
```
[Почему так стоит делать](https://docs.github.com/en/issues/tracking-your-work-with-issues/using-issues/linking-a-pull-request-to-an-issue)

