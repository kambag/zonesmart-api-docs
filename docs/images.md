---
tags: [images, image]
---

# Изображения

Работа с изображениями осуществляется путем первоначальной загрузки изображения отдельным запросом, чтобы затем можно было использовать id для добавления в изображения листингов.

# Пример

В первую очередь перед добавлением изображения к продукту нам необходимо его загрузить.

Загрузка изображения осуществляется двумя типами:
* Загрузка через url.
* Загрузка с помощью файла.

Пример запроса для загрузки через url:
```json http
{
  "method": "post",
  "url": "/v1/zonesmart/image/",
  "headers": {
    "Content-Type": "application/json"
  },
  "body": {
	  "url": "https://zonesmart.fra1.digitaloceanspaces.com/zonesmart/media_debug/images/a925eff8-c720-4a35-a8b4-4ea4351fc779.jpg",
	  "description": "Простое описание изображение (необязательно)"
  }
}
```

В ответ на запрос мы получим следующий ответ:
```json
{
    "id": "8308ed88-c5fd-4cde-8d25-ae813d74367c",
    "is_removed": false,
    "file": null,
    "url": "https://zonesmart.fra1.digitaloceanspaces.com/zonesmart/media_debug/images/a925eff8-c720-4a35-a8b4-4ea4351fc779.jpg",
    "description": "simple image description",
    "marketplace_image_id": null
}
```

Пример запроса для загрузки через файл с помощью CURL:
```curl
curl --location --request POST 'https://zonesmart.su/api/v1/zonesmart/image/' \
--header 'Authorization: JWT eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ0b2tlbl90eXBlIjoiYWNjZXNzIiwiZXhwIjoxNTkxODc1Njg1LCJqdGkiOiIzZTg0YjU4MTM4OTM0MzYyOWNlMjA2OGJjMTI1NGI5MSIsInVzZXJfaWQiOjEsImVtYWlsIjoiYWRtaW5Aem9uZXNtYXJ0LnJ1IiwicGhvbmUiOm51bGwsInVzZXJuYW1lIjoiYWRtaW4iLCJhY2Nlc3NfdW50aWwiOm51bGwsImhhc19hY2Nlc3MiOmZhbHNlfQ.6NISzmXXOZlABtVUkPXU2LD2XmwJKTmj30eaHd49vj0' \
--header 'Content-Type: application/json' \
--form 'file=@/Users/stefanitsky/Downloads/image.jpg'
```

В ответ на запрос мы получим аналогчиный ответ как и при загрузке через url, но теперь ссылка на файл будет в ключе file:
```json
{
    "id": "7ec0c083-2b88-4b25-b9c0-416a27ff92ad",
    "is_removed": false,
    "file": "https://zonesmart.fra1.digitaloceanspaces.com/zonesmart/media_debug/images/c7fc04e5-f0d9-4442-8de0-bd368c261bec.jpg",
    "url": null,
    "description": null,
    "marketplace_image_id": null
}
```

Теперь id изображения может быть использован для создания или обновления листингов (eBay, Etsy, базовых и так далее).

Пример использования id загруженных изображений при создании продукта к базовому листингу:
```json http
{
  "method": "post",
  "url": "/v1/zonesmart/listing/b458fd2d-26c4-4557-a9f0-0007c20b191a/product/",
  "headers": {
    "Content-Type": "application/json"
  }
  "body": {
	  "sku": "TSHIRT-3",
	  "price": 5000,
	  "main_image": "7ec0c083-2b88-4b25-b9c0-416a27ff92ad",
	  "extra_images": [
		  "9ae6c934-d515-49f7-a6a2-b069929f77b5",
    	"242ea663-559c-4d00-8fbb-fe09e8a9f88f"
	  ]
  }
}
```

В ответ получаем следующий ответ:
```json
{
    "id": "41503988-6965-4008-9d2c-0bc000dd46f4",
    "created": "2020-06-11T12:53:03.969215+03:00",
    "modified": "2020-06-11T12:53:03.969471+03:00",
    "is_removed": false,
    "sku": "TSHIRT-3",
    "quantity": 0,
    "price": 5000.0,
    "product_code": null,
    "condition": null,
    "condition_note": null,
    "listing": "b458fd2d-26c4-4557-a9f0-0007c20b191a",
    "main_image": "https://zonesmart.fra1.digitaloceanspaces.com/zonesmart/media_debug/images/c7fc04e5-f0d9-4442-8de0-bd368c261bec.jpg",
    "product_code_type": null,
    "extra_images": [
        "https://zonesmart.fra1.digitaloceanspaces.com/zonesmart/media_debug/images/9e68f986-8600-4987-8cfb-434f4d1f3417.jpg",
        "https://zonesmart.fra1.digitaloceanspaces.com/zonesmart/media_debug/images/d3275324-6629-4dfc-85e6-372af2d8ee11.jpg"
    ]
}
```

