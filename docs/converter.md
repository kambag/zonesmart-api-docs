---
tags: [converter]
---

# Конвертер листингов

API Zonesmart позволяет конвертировать листинги с одного маркетплейса в другой. Это осуществляется путем конвертации через базовый листинг.

## Конвертация в базовый листинг

Для конвертации в базовый листинг потребуется лишь обратиться к конкретному листингу по API и добавить в запрос /link/, это позволит конвертировать текущий листинг в базовый, путем его "линкования".

Пример запроса для eBay:

```json http
{
  "method": "POST",
  "url": "/v1/ebay/listing/{id}/link/"
}
```

После этого у данного листинга в поле base_listing будет указан id нового созданного листинга на его основе, с которым можно будет работать дальше.

## Открепление (unlink) базового листинга

Открепить базовый листинг от интеграционного можно сделать путем аналогичного запроса как для линкования, только вместо /link/ необходимо указать /unlink/.

Пример запроса для eBay:

```json http
{
  "method": "POST",
  "url": "/v1/ebay/listing/{id}/unlink/"
}
```

После выполнения запроса у листинга поле base_listing станет None, но при этом базовый листинг не удаляется, а лишь убирается связь между ними.

## Конвертация из базового листинга в интеграционный

Конвертация из базового листинга в итеграционный осуществляется путем обращения к базовому листингу.

```json http
{
  "method": "POST",
  "url": "/v1/zonesmart/listing/{id}/convert/"
}
```

При передаче запроса необходимо указать channel_id необходимого канала продаж, для которого будет создан новый листинг, например:

```json
{
  "channel_id": "0de8d802-604d-4bd5-a139-8565d699e9f5"
}
```

В ответ на запрос будет возвращен id нового созданного листинга на основе базового.
Зная название интеграции, к которой привязан канал продаж, мы можем с легкостью открыть необходимый запрос для просмотра нового листинга, например для eBay это будет:

```json http
{
  "method": "GET",
  "url": "/v1/ebay/listing/{id}/"
}
```

## Конвертация из одного маркетплейса в другой

Так как вся конвертация осуществляется через базовый листинг, то необходимо проделать описанные ранее шаги поочереди, чтобы получить из одного листинга интеграции другой.

Соответственно порядок будет следующий:

1. Линкуем (link) текущий листинг, чтобы на его основе создался базовый.
2. Конвертируем базовый в необходимую интеграцию, путем передачи id канала продаж.