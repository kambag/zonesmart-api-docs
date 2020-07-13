---
tags: [endpoint, entity]
---

# Endpoint fields_meta
Возвращает словарь, который содержит описание полей и связей.

### Для полей:
  Всегда возвращает:
  * **type** - Тип данных, который хранится в данном поле.
  * **verbose_name** - Удобночитаемое название поля.
  * **required** - Является ли поле объязательным для заполнения.

  В зависимости от типа поля:
  * **max_length** - Максимальная длина строки.
  * **choices** - Список значений, которые принимает данное поле.

### Для связанных моделей:
  Всегда возвращает:
  * **required** - Является ли связь объязательной.
  * **verbose_name** - Удобночитаемое название модели.
  * **many** - Может ли быть несколько связанных объектов.
  * **fields** - Описание полей связанной модели.

## Type
* CharField - cтроковое поле для хранения коротких или длинных строк.
* TextField - большое текстовое поле.
* URLField - поле CharField для URL.
* EmailField - поле CharField для хранения правильного email-адреса.
* DateField - дата, представленная в виде объекта datetime.date Python.
* DateTimeField - дата и время, представленные объектом datetime.datetime Python.
* FileField - поле для загрузки файла.
* DecimalField - десятичное число с фиксированной точностью.
* FloatField - число с плавающей точкой.
* IntegerField - позволяет хранить числа от -2147483648 до 2147483647.
* PositiveIntegerField - как и поле IntegerField, но значение должно быть больше или равно нулю (0).
* SmallIntegerField - позволяет хранить числа от -32768 до 32767.
* PositiveSmallIntegerField - как и поле SmallIntegerField, но значение должно быть больше или равно нулю (0).
* BigIntegerField - позволяет хранить числа от -9223372036854775808 до 9223372036854775807.
* UUIDField - поле для сохранения UUID.
* ForeignKey - связь многое-к-одному, принимает uuid.
* ManyToManyField - связь многие-ко-многим, принимает список uuid.
* OneToOneField - связь один-к-одному, принимает uuid.

## Пример запроса
```json http
{
  "method": "GET",
  "url": "/v1/ebay/listing/fields_meta/"
}
```

## Пример ответа

```json
{
  "title": {
    "type": "CharField",
    "verbose_name": "Название",
    "required": true,
    "max_length": 64,
  },
  "product": {
    "required": false,
    "verbose_name": "Товар",
    "many": true,
    "fields": {
      "price": {}
    }
  }
}
```
