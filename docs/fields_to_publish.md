---
tags: [endpoint, entity]
---

# Endpoint fields_to_publish
Возвращает список полей, необходимые для публикации entity на маркетплейсе. Так же указывает, готов ли указанный entity к публикации и заполненость необходимых полей.
В случае, если необходимое для публикации поле находится в связанной модели, возвращает id каждого связанного объекта.

## Пример запроса
```json http
{
  "method": "GET",
  "url": "/v1/ebay/listing/{listing_id}/fields_to_publish/"
}
```

## Пример ответа
```json
{
  "ready_to_be_published": false,
  "title": true,
  "main_image": true,
  "products": [
      {
        "id": "uuid первого связаного объекта",
        "price": false,
        "quantity": true,
      },
      {
        "id": "uuid второго связаного объекта",
        "price": true,
        "quantity": true,
      }
    ]
}
```
