# Подписки на события

В Zonesmart реализована система подписок на некоторые типы событий. При возникновении события все активные подписчики получают уведомление на указанные ими URL. Уведомление представляет из себя POST-запрос с информацией о заказе в теле, имеющем ту же структуру, что и результат [запроса на получение заказа](https://zonesmart.stoplight.io/docs/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1order~1%7Bid%7D~1/get). Для каждого типа события пользователь может указать не более трёх URL.

В данный момент поддерживаются подписки на следующие события:
- Появление нового заказа
- Обновление статуса существующего заказа

Эндпоинты:

1) [Создание подписки](https://zonesmart.stoplight.io/docs/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1event~1/post)

2) [Получение списка подписок](https://zonesmart.stoplight.io/docs/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1event~1/get)

3) [Удаление подписки](https://zonesmart.stoplight.io/docs/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1event~1%7Bid%7D~1/delete)

4) [Обновление подписки](https://zonesmart.stoplight.io/docs/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1event~1%7Bid%7D~1/patch)

5) [Активация подписки](https://zonesmart.stoplight.io/docs/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1event~1%7Bid%7D~1activate~1/post)

6) [Деактивация подписки](https://zonesmart.stoplight.io/docs/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1event~1%7Bid%7D~1deactivate~1/post)
