---
tags: [authentication, auth, jwt]
---

# Авторизация

Для работы с API Zonesmart требуется авторизация с помощью [JWT](https://en.wikipedia.org/wiki/JSON_Web_Token) токенов.

## Получение токенов

Для получения токенов требуется передать email и пароль пользователя с помощью следующего запроса:

```json http
{
  "method": "post",
  "url": "/v1/zonesmart/auth/jwt/create/",
  "headers": {
    "Content-Type": "application/json"
  },
  "body": {
    "email": "admin@zonesmart.ru",
    "password": "4815162342"
  }
}
```

В ответ на запрос мы получим следующий ответ:

```json
{
    "refresh": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ0b2tlbl90eXBlIjoicmVmcmVzaCIsImV4cCI6MTU5OTY0NzYzNSwianRpIjoiZGI0ZDAwNzY4Mzk1NDIyOTk5NjdmODY5OTlhNzdiMzAiLCJ1c2VyX2lkIjoxLCJlbWFpbCI6ImFkbWluQHpvbmVzbWFydC5ydSIsInBob25lIjpudWxsLCJ1c2VybmFtZSI6ImFkbWluIiwiYWNjZXNzX3VudGlsIjpudWxsLCJoYXNfYWNjZXNzIjpmYWxzZX0.QxvdJNqDUWopTYJDoRMy5tdmv6lXxaDpZ02EO8ekH5Q",
    "access": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ0b2tlbl90eXBlIjoiYWNjZXNzIiwiZXhwIjoxNTkxODc4ODM1LCJqdGkiOiIzM2VjNWMyMmZmMTc0MmY5YmVlZTk2ZGUwYTZkYjcwNyIsInVzZXJfaWQiOjEsImVtYWlsIjoiYWRtaW5Aem9uZXNtYXJ0LnJ1IiwicGhvbmUiOm51bGwsInVzZXJuYW1lIjoiYWRtaW4iLCJhY2Nlc3NfdW50aWwiOm51bGwsImhhc19hY2Nlc3MiOmZhbHNlfQ.oTSKvSgI8hd5k_5t1LsedEgo2_rS53m5uuhuVw2hCro"
}
```

## Время жизни токенов

* access - 2 часа.
* refresh - 90 дней.

## Как использовать access токен

Для авторизации требуется передвать access токен в header запроса, например:

```json http
{
  "method": "get",
  "headers": {
    "Authorization": "JWT eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ0b2tlbl90eXBlIjoiYWNjZXNzIiwiZXhwIjoxNTkxODc4ODM1LCJqdGkiOiIzM2VjNWMyMmZmMTc0MmY5YmVlZTk2ZGUwYTZkYjcwNyIsInVzZXJfaWQiOjEsImVtYWlsIjoiYWRtaW5Aem9uZXNtYXJ0LnJ1IiwicGhvbmUiOm51bGwsInVzZXJuYW1lIjoiYWRtaW4iLCJhY2Nlc3NfdW50aWwiOm51bGwsImhhc19hY2Nlc3MiOmZhbHNlfQ.oTSKvSgI8hd5k_5t1LsedEgo2_rS53m5uuhuVw2hCro",
    "Content-Type": "application/json"
  },
  "url": "/users/me/"
}
```

В ответ на запрос мы получим информацию о нашем пользователе:
```json
{
    "id": 1,
    "email": "admin@zonesmart.ru"
}
```

## Как использовать refresh токен

Refresh токен может потребоваться, если время работы access токена истекло и требуется получить новый. Для этого необходимо сделать следующий запрос:

```json http
{
  "method": "get",
  "headers": {
    "Content-Type": "application/json"
  },
  "url": "/auth/jwt/refresh/",
  "body": {
    "refresh": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ0b2tlbl90eXBlIjoicmVmcmVzaCIsImV4cCI6MTU5OTY0NzYzNSwianRpIjoiZGI0ZDAwNzY4Mzk1NDIyOTk5NjdmODY5OTlhNzdiMzAiLCJ1c2VyX2lkIjoxLCJlbWFpbCI6ImFkbWluQHpvbmVzbWFydC5ydSIsInBob25lIjpudWxsLCJ1c2VybmFtZSI6ImFkbWluIiwiYWNjZXNzX3VudGlsIjpudWxsLCJoYXNfYWNjZXNzIjpmYWxzZX0.QxvdJNqDUWopTYJDoRMy5tdmv6lXxaDpZ02EO8ekH5Q"
  }
}
```

В ответ мы получим новый access токен:
```json
{
    "access": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ0b2tlbl90eXBlIjoiYWNjZXNzIiwiZXhwIjoxNTkxODc5MzEyLCJqdGkiOiI2NDBlNWEwNDg1MTI0YmRkOTk0MDIyYmNhOGJhYjIxOSIsInVzZXJfaWQiOjEsImVtYWlsIjoiYWRtaW5Aem9uZXNtYXJ0LnJ1IiwicGhvbmUiOm51bGwsInVzZXJuYW1lIjoiYWRtaW4iLCJhY2Nlc3NfdW50aWwiOm51bGwsImhhc19hY2Nlc3MiOmZhbHNlfQ.OnO5lxDEhM_JVEL2bt0ewZMWVlkikjicT_p7mSv5WOo"
}
```
