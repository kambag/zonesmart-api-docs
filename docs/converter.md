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

## Загрузка и выгрузка листингов c помощью файлов (фидов)

С помощью API можно загружать и выгружать листинги в формате YML (для Yandex.Market) и CSV (для всех остальных).

Далее будет описано скачивание и загрузка листингов для базовых листингов и доступных на данный момент интеграций.

### Базовый листинг

Скачивание:

```json http
{
  "method": "get",
  "url": "v1/zonesmart/listing/file_download/"
  "headers": {
    "Content-Type": "text/csv"
  }
}
```

Загрузка:

```json http
{
  "method": "post",
  "url": "v1/zonesmart/listing/file_upload/"
  "headers": {
    "Content-Type": "multipart/form-data"
  }
}
```

Пример фида базового листинга: [base_listing_feed.csv](../base_listing_feed.csv)

Фид базового листинга содержит следующую структуру:

sku |	parent_sku | title |	description |	price |	currency |	quantity | category_name |	product_code |	product_code_type |	condition |	condition_note |	brand	| main_image_url |	attribute_name_1 |	attribute_name_2 |	attribute_name_3 |	attribute_name_4 |	attribute_name_5 |	attribute_value_1 |	attribute_value_2 |	attribute_value_3 |	attribute_value_4 |	attribute_value_5 |	extra_image_url_1 |	extra_image_url_2 |	extra_image_url_3 |	extra_image_url_4 |	extra_image_url_5 |	extra_image_url_6 |	extra_image_url_7 |	extra_image_url_8 |	extra_image_url_9 |	extra_image_url_10 |	extra_image_url_11 |	extra_image_url_12 |	package_weight |	package_length |	package_height |	package_width |	package_unit_of_measure |	package_unit_of_weight
---------|----------
PRODUCT-SKU-1	| | Футболка | Простое описание футболки	|	49.9 | USD |	4 |	cloth |	978-0-8232-0299-7 |	3 |	USED |	Просто описание состояния товара | Nike |	https://picsum.photos/id/17/500/500 |	Цвет	| Размер | Ткань | | | Зеленый | XL | Хлопок | | |	https://picsum.photos/id/8/500/500 |	https://picsum.photos/id/13/500/500 |	https://picsum.photos/id/10/500/500 | | | | | | | | | | 1 |	30 | 5 |	40 |	CM | KG

#### sku

Уникальный идентификатор товара.

Тип поля: **обязательное**.

Максимальная длина - 255 символов.

#### parent_sku

Уникальный идентификатор родительского товара, который используется для обозначения нескольких вариаций одного и того же продукта.

Для использования необходимо указать у всех вариаций (из группы) одинаковый parent_sku.

Тип поля: **условное**. Поле обязательно для вариативных листингов.

#### title

Наименование продукта. Для вариативных товаров должен быть указан только у родительского товара.

Тип поля: **обязательное**.

Максимальная длина - 255 символов.

#### description

Описание продукта.

Тип поля: **обязательное**.

Максимальная длина - 500000 символов.

#### price

Цена товара.

Тип поля: **обязательное**.

Может быть как целым числом, так и числом с плавающей точкой: 10, 10.5, 10.99 и так далее.

#### currency

Валюта листинга. Следует указывать только в родительском товаре для вариативных листингов.

Допустимые коды валют: AED, AFN, ALL, AMD, ANG, AOA, ARS, AUD, AWG, AZN, BAM, BBD, BDT, BGN, BHD, BIF, BMD, BND, BOB, BRL, BSD, BTN, BWP, BYR, BZD, CAD, CDF, CHF, CLP, CNY, COP, CRC, CUP, CVE, CZK, DJF, DKK, DOP, DZD, EGP, ERN, ETB, EUR, FJD, FKP, GBP, GEL, GHS, GIP, GMD, GNF, GTQ, GYD, HKD, HNL, HRK, HTG, HUF, IDR, ILS, INR, IQD, IRR, ISK, JMD, JOD, JPY, KES, KGS, KHR, KMF, KPW, KRW, KWD, KYD, KZT, LAK, LBP, LKR, LRD, LSL, LTL, LYD, MAD, MDL, MGA, MKD, MMK, MNT, MOP, MRO, MUR, MVR, MWK, MXN, MYR, MZN, NAD, NGN, NIO, NOK, NPR, NZD, OMR, PAB, PEN, PGK, PHP, PKR, PLN, PYG, QAR, RON, RSD, RUB, RWF, SAR, SBD, SCR, SDG, SEK, SGD, SHP, SLL, SOS, SRD, STD, SYP, SZL, THB, TJS, TMT, TND, TOP, TRY, TTD, TWD, TZS, UAH, UGX, USD, UYU, UZS, VEF, VND, VUV, WST, XAF, XCD, XOF, XPF, YER, ZAR, ZMW, ZWL.

Тип поля: **необязательное**. Если currency не указан, то по стандарту будет установлена валюта RUB.

#### quantity

Количество товара в наличии.

Тип поля: **необязательное**. Если quantity не указан, то по стандарту будет установлен 0.

#### category_name

Наименование категории.

Тип поля: **необязательное**.

#### product_code

Уникальный код товара.

Тип поля: **необязательное**.

Максимальная длина - 20 символов.

#### product_code_type

Тип уникального кода товара.

Тип поля: **необязательное**.

Может принимать одно из возможных значений:

ASIN - 1.
ISBN - 2.
UPC - 3.
EAN - 4.
GCID - 5.
JAN - 6.
GTIN - 7.

#### condition

Тип состояния товара.

Тип поля: **необязательное**.

Может быть одним из следующих типов: NEW (новый), USED (Б/У), REFURBISHED (отремонтировано), OTHER (другое).

#### condition_note

Описание состояния товара.

Тип поля: **необязательное**.

Максимальная длина - 255 символов.

#### brand

Наименование бренда.

Тип поля: **необязательное**.

Максимальная длина - 255 символов.

#### main_image_url

Ссылка на основное изображение товара.

Тип поля: **необязательное**.

#### attribute_name_1...5

Наименования характеристик товара (цвет, размер, материал и так далее).

Тип поля: **необязательное**.

Максимальная длина - 255 символов.

#### attribute_value_1...5

Значения характеристик товара (зеленый, XL, хлопок и так далее).

Тип поля: **необязательное**.

Максимальная длина - 255 символов.

#### extra_image_url_1...12

Ссылки на дополнительные изображения товара.

Тип поля: **необязательное**.

#### package_weight

Вес посылки.

Тип поля: **необязательное**.

#### package_length

Длина посылки.

Тип поля: **необязательное**.

#### package_height

Высота посылки.

Тип поля: **необязательное**.

#### package_width

Ширина посылки.

Тип поля: **необязательное**.

#### package_unit_of_measure

Единица измерения габаритов посылки (длина, высота, ширина).

Тип поля: **необязательное**.

Может быть одним из следующих значений:

* IN - дюйм.
* FT - фут.
* CM - сантиметр.
* M - метр.

#### package_unit_of_weight

Единица измерения веса посылки.

Тип поля: **необязательное**.

Может быть одним из следующих значений:

* GR - грамм.
* KG - килограмм.
* LB - фунт.
* OZ - унция.

### Листинг Ebay

Скачивание:

```json http
{
  "method": "get",
  "url": "v1/ebay/listing/file_download/"
  "headers": {
    "Content-Type": "text/csv"
  }
}
```

Загрузка:

```json http
{
  "method": "post",
  "url": "v1/ebay/listing/file_upload/"
  "headers": {
    "Content-Type": "multipart/form-data"
  }
}
```

Пример фида листинга Ebay: [ebay_listing_feed.csv](../ebay_listing_feed.csv)

Структура фида листинга Ebay:

Localized For |	Variation Group ID |	Variation Specific Name 1 |	Variation Specific Name 2 |	Variation Specific Name 3 |	Variation Specific Name 4 |	Variation Specific Name 5 |	Variation Specific Value 1 |	Variation Specific Value 2 |	Variation Specific Value 3 |	Variation Specific Value 4 |	Variation Specific Value 5 |	Title |	Subtitle |	Product Description |	Additional Info |	Group Picture URL |	Picture URL 1 |	Picture URL 2 |	Picture URL 3 |	Picture URL 4 |	Picture URL 5 |	Picture URL 6 |	Picture URL 7 |	Picture URL 8 |	Picture URL 9 |	Picture URL 10 |	Picture URL 11 |	Picture URL 12 |	UPC |	ISBN |	EAN |	MPN |	Brand |	ePID |	Attribute Name 1 |	Attribute Value 1 |	Attribute Name 2 |	Attribute Value 2 |	Attribute Name 3 |	Attribute Value 3 |	Attribute Name 4 |	Attribute Value 4 |	Attribute Name 5 |	Attribute Value 5 |	Attribute Name 6 |	Attribute Value 6 |	Attribute Name 7 |	Attribute Value 7 |	Attribute Name 8 |	Attribute Value 8 |	Attribute Name 9 |	Attribute Value 9 |	Attribute Name 10 |	Attribute Value 10 |	Attribute Name 11 |	Attribute Value 11 |	Attribute Name 12 |	Attribute Value 12 |	Attribute Name 13 |	Attribute Value 13 |	Attribute Name 14 |	Attribute Value 14 |	Attribute Name 15 |	Attribute Value 15 |	Attribute Name 16 |	Attribute Value 16 |	Attribute Name 17 |	Attribute Value 17 |	Attribute Name 18 |	Attribute Value 18 |	Attribute Name 19 |	Attribute Value 19 |	Attribute Name 20 |	Attribute Value 20 |	Attribute Name 21 |	Attribute Value 21 |	Attribute Name 22 |	Attribute Value 22 |	Attribute Name 23 |	Attribute Value 23 |	Attribute Name 24 |	Attribute Value 24 |	Attribute Name 25 |	Attribute Value 25 |	Condition	| Condition Description |	Measurement System |	Length	| Width |	Height |	Weight Major |	Weight Minor |	Package Type |	Total Ship To Home Quantity |	Channel ID |	Category |	Shipping Policy |	Payment Policy |	Return Policy	| List Price |	Max Quantity Per Buyer	| Strikethrough Price |	Minimum Advertised Price |	Minimum Advertised Price Handling |	Store Category Name 1 |	Store Category Name 2 |	Sold Off Ebay	| Sold On Ebay | Apply Tax |	SKU |	VAT Percent |	Include eBay Product Details |	Domestic Shipping P1 Cost |	Domestic Shipping P1 Additional Cost |	Domestic Shipping P1 Surcharge |	Domestic Shipping P2 Cost |	Domestic Shipping P2 Additional Cost |	Domestic Shipping P2 Surcharge |	Domestic Shipping P3 Cost |	Domestic Shipping P3 Additional Cost |	Domestic Shipping P3 Surcharge	| Domestic Shipping P4 Cost |	Domestic Shipping P4 Additional Cost |	Domestic Shipping P4 Surcharge |	International Shipping P1 Cost |	International Shipping P1 Additional Cost |	International Shipping P1 Surcharge |	International Shipping P2 Cost |	International Shipping P2 Additional Cost |	International Shipping P2 Surcharge |	International Shipping P3 Cost |	International Shipping P3 Additional Cost |	International Shipping P3 Surcharge |	International Shipping P4 Cost |	International Shipping P4 Additional Cost |	International Shipping P4 Surcharge |	International Shipping P5 Cost |	International Shipping P5 Additional Cost	| International Shipping P5 Surcharge |	TemplateName |	CustomFields |	Eligible For EbayPlus |	LocationID |	FulfillmentType |	Available |	FulfillmentTime |	Compatible Product 1 |	Compatible Product 2
---------|----------
en_US |	test_multiple_listing_sku_19 |	Color | | | | | Black | | | | | Наименование товара |	| Описание товара | |		https://picsum.photos/id/17/500/500 |	https://picsum.photos/id/15/500/500 |	https://picsum.photos/id/6/500/500 |	https://picsum.photos/id/10/500/500 |	https://picsum.photos/id/7/500/500 | | | | | | | | | | | | | | | Brand |	Zonesmart	| MPN |	1231241A-V | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | NEW | Описание состояния товара | | | | | | | | 1 | EBAY_US | 177845 | Fulfillment policy name | Payment policy name | Return policy name | 1.0 | | | | | | | | | | test_product_sku_28 | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | Location ID | | | | | |

#### Localized For

Локализация листинга в системе Ebay.

Тип поля: **необязательное**.

Доступные значения: en_US, en_CA, en_GB, en_AU, de_AT, fr_BE, fr_FR, de_DE, it_IT, nl_BE, nl_NL, es_ES, de_CH, zh_TW, zh_HK, en_IE, en_PH, pl_PL, ru_RU, th_TH, fr_CA.

#### Variation Group ID
#### Variation Specific Name 1...5
#### Variation Specific Value 1...5
#### Title
#### Subtitle
#### Product Description
#### Additional Info
#### Group Picture URL
#### Picture URL 1...12
#### UPC
#### ISBN
#### EAN
#### MPN
#### Brand
#### ePID
#### Attribute Name 1...25
#### Attribute Value 1...25
#### Condition
#### Condition Description
#### Measurement System
#### Length
#### Width
#### Height
#### Weight Major
#### Weight Minor
#### Package Type
#### Total Ship To Home Quantity
#### Channel ID
#### Category
#### Shipping Policy
#### Payment Policy
#### Return Policy
#### List Price
#### Max Quantity Per Buyer
#### Strikethrough Price
#### Minimum Advertised Price
#### Minimum Advertised Price Handling
#### Store Category Name 1..2
#### Sold Off Ebay
#### Sold On Ebay
#### Apply Tax
#### SKU
#### VAT Percent
#### Include eBay Product Details
#### Domestic Shipping P1...P4 Cost
#### Domestic Shipping P1...P4 Additional Cost
#### Domestic Shipping P1...P4 Surcharge
#### International Shipping P1...P5 Cost
#### International Shipping P1...P5 Additional Cost
#### International Shipping P1...P5
#### TemplateName
#### CustomFields
#### Eligible For EbayPlus
#### LocationID
#### FulfillmentType
#### Available
#### FulfillmentTime
#### Compatible Product 1..2