---
tags: [listing, ebay]
---

# Листинги Ebay

Листинг Ebay состоит из следующих моделей:
* Listing - основная модель листинга, которая содержит основную информацию.
* Product - продукт листинга, который содержит основную информацию о продукте.
* Specification = характеристика продукта (вариативная).
* Property - характеристика листинга (общая).
* Compatibility - характеристика запчасти (только для транспортных категорий).

Далее будут более подробно описана работа с каждой из моделей.

## Listing

Для создания листинга необходимо выполнить следующий запрос:

```json http
{
  "method": "post",
  "url": "v1/ebay/listing/",
  "headers": {
    "Content-Type": "application/json"
  },
  "body": {
	  "channel": "a270cfbe-948d-47a9-a0a8-e8bb736765dc",
	  "listing_sku": "TEST_LISTING_SKU",
	  "title": "Футболка"
  }
}
```

Пример запроса на создание:

```json
{
  "channel": "a270cfbe-948d-47a9-a0a8-e8bb736765dc",
  "listing_sku": "TEST_LISTING_SKU",
  "title": "Футболка"
}
```

> channel, listing_sku и title это обязательные поля для того, чтобы создать листинг.

В ответ на запрос будет возвращен созданный листинг:

```json
{
    "id": "fca4ff9b-8ef2-4c85-b361-d4690d96d305",
    "channel": "a270cfbe-948d-47a9-a0a8-e8bb736765dc",
    "base_listing": null,
    "listing_sku": "TEST_LISTING_SKU",
    "title": "Футболка",
    "listing_description": null,
    "location": null,
    "category": null,
    "localizedFor": "en_US",
    "group_image": null,
    "currency": null,
    "fulfillmentPolicy": null,
    "paymentPolicy": null,
    "returnPolicy": null,
    "bestOfferEnabled": false,
    "auto_accepted_price": null,
    "auto_declined_price": null
}
```
