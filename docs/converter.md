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

Для загрузки также требуется передать marketplace_user_account_id, помимо самого фида.

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

Уникальный идентификатор группы для вариативного листинга.

Тип поля: **необязательное**.

Максимальная длина: 30 символов.

#### Variation Specific Name 1...5

Наименование отличительной характеристики товара. Примеры: цвет, размер, тип ткани.

Тип поля: **необязательное**.

#### Variation Specific Value 1...5

Значение отличительной характеристики товара.

Тип поля: **необязательное**.

#### Title

Наименование товара.

Тип поля: **обязательное**.

#### Subtitle

На текущий момент не используется.

Тип поля: **необязательное**.

#### Product Description

Описание товара.

Тип поля: **необязательное**.

#### Additional Info

На текущий момент не используется.

Тип поля: **необязательное**.

#### Group Picture URL

Ссылка на групповое изображение товара.

Тип поля: **необязательное**.

#### Picture URL 1...12

Изображения товара. Можно указать до 12 штук (лимит в системе Ebay).

Тип поля: **необязательное**.

#### UPC

Универсальный код товара.

Тип поля: **необязательное**.

#### ISBN

Международный стандартный книжный номер.

Тип поля: **необязательное**.

#### EAN

Eвропейский номер товара.

Тип поля: **необязательное**.

#### MPN

Код производителя.

Тип поля: **необязательное**.

#### Brand

Наименование производителя (бренда).

Тип поля: **необязательное**.

#### ePID

Идентификатор товара в системе Ebay.

Тип поля: **необязательное**.

#### Attribute Name 1...25

Наименование общей характеристики товара. Используется в вариативных листингах для указания общих характеристик продукта.

Тип поля: **необязательное**.

#### Attribute Value 1...25

Значение общей характеристика товара.

Тип поля: **необязательное**.

#### Condition

Состояние товара.

Может принимать следующие значения:

* NEW - новое.
* LIKE_NEW - как новое.
* NEW_OTHER - новое с особенностями упаковки.
* NEW_WITH_DEFECTS - новое с дефектами.
* MANUFACTURER_REFURBISHED - отремонтированное производителем.
* SELLER_REFURBISHED - отремонтированное продавцом.
* USED_EXCELLENT - подержанное, в отличном состоянии.
* USED_VERY_GOOD - подержанное, в очень хорошем состоянии.
* USED_GOOD - подержанное, в хорошем состоянии.
* USED_ACCEPTABLE - подержанное, в удовлетворительном состоянии.
* FOR_PARTS_OR_NOT_WORKING - неполноценно функционирующее.

Тип поля: **необязательное**. Если не указано, то по стандарту будет указано NEW.

#### Condition Description

Описание состояния товара.

Тип поля: **необязательное**.

Максимальная длина: 1000 символов.

#### Measurement System

Система измерения габаритов посылки.

На текущий момент не используется.

Тип поля: **необязательное**.

#### Length

Длина посылки.

На текущий момент не используется.

Тип поля: **необязательное**.

#### Width

Ширина посылки.

На текущий момент не используется.

Тип поля: **необязательное**.

#### Height

Высота посылки.

На текущий момент не используется.

Тип поля: **необязательное**.

#### Weight Major

На текущий момент не используется.

Тип поля: **необязательное**.

#### Weight Minor

На текущий момент не используется.

Тип поля: **необязательное**.

#### Package Type

На текущий момент не используется.

Тип поля: **необязательное**.

#### Total Ship To Home Quantity

Количество доступных товаров в наличии.

Тип поля: **необязательное**. Если не указано, то по стандарту будет указан 0.

#### Channel ID

Уникальный идентификатор канала (сайта) Ebay.

Может принимать следующие значения:

* EBAY_US - US site (ebay.com).
* EBAY_MOTORS_US - US eBay Motors site (ebay.com/motors).
* EBAY_CA - Canada (English) site (ebay.ca).
* EBAY_GB - UK site (ebay.co.uk).
* EBAY_AU - Australia site (ebay.com.au).
* EBAY_AT - Austria site (ebay.at).
* EBAY_BE_FR - French version of the Belgium site (befr.ebay.be).
* EBAY_FR - France site (ebay.fr).
* EBAY_DE - Germany site (ebay.de).
* EBAY_IT - Italy site (ebay.it).
* EBAY_BE_NL - Dutch version of the Belgium site (https://www.benl.ebay.be/).
* EBAY_NL - Netherlands site (ebay.nl).
* EBAY_ES - Spain site (ebay.es).
* EBAY_CH - Switzerland site (ebay.ch).
* EBAY_TW - Taiwan site (ebay.com/tw).
* EBAY_CZ - Czech Republic site (ebay.com/sch/Czech-Republic).
* EBAY_DK - Denmark site (ebay.com/sch/Denmark).
* EBAY_FI - Finland site (ebay.com/sch/Finland).
* EBAY_GR - Greece site (ebay.com/sch/Greece).
* EBAY_HK - Hong Kong site (ebay.com.hk).
* EBAY_HU - Hungary site (ebay.com/sch/Hungary).
* EBAY_IN - India site (ebay.in).
* EBAY_ID - Indonesia site (id.ebay.com).
* EBAY_IE - Ireland site (ebay.ie).
* EBAY_IL - Israel site (ebay.com/sch/Israel).
* EBAY_MY - Malaysia site (ebay.com/my).
* EBAY_NZ - New Zealand site (ebay.com/sch/New-Zealand).
* EBAY_NO - Norway site (ebay.com/sch/Norway).
* EBAY_PH - Philippines site (ebay.ph).
* EBAY_PL - Poland site (ebay.pl).
* EBAY_PT - Portugal site (ebay.com/sch/Portugal).
* EBAY_PR - Puerto Rico site (ebay.com/sch/Puerto-Rico).
* EBAY_RU - Russia site (ebay.com/sch/Russia).
* EBAY_SG - Singapore site (ebay.com/sg).
* EBAY_ZA - South Africa site (ebay.com/sch/South-Africa).
* EBAY_SE - Sweden site (ebay.com/sch/Sweden).
* EBAY_TH - Thailand site (export.ebay.co.th).
* EBAY_VN - Vietnam site (ebay.vn).
* EBAY_CN - China site (ebay.com/sch/China).
* EBAY_PE - Peru site (ebay.com/sch/Peru).
* EBAY_CA_FR - Canada (English) site (https://www.cafr.ebay.ca/).
* EBAY_JP - Japan site (ebay.co.jp).

Тип поля: **обязательное**.

#### Category

Уникальный идентификатор категории в системе Ebay.

Тип поля: **необязательное**.

#### Shipping Policy

Название политики доставки.

Тип поля: **необязательное**.

#### Payment Policy

Название политики оплаты.

Тип поля: **необязательное**.

#### Return Policy

Название политики возврата.

Тип поля: **необязательное**.

#### List Price

Цена товара.

Тип поля: **необязательное**.

#### Max Quantity Per Buyer

Ограничение числа товаров на один заказ.

Тип поля: **необязательное**.

#### Strikethrough Price

На текущий момент не используется.

Тип поля: **необязательное**.

#### Minimum Advertised Price

На текущий момент не используется.

Тип поля: **необязательное**.

#### Minimum Advertised Price Handling

На текущий момент не используется.

Тип поля: **необязательное**.

#### Store Category Name 1..2

На текущий момент не используется.

Тип поля: **необязательное**.

#### Sold Off Ebay

На текущий момент не используется.

Тип поля: **необязательное**.

#### Sold On Ebay

На текущий момент не используется.

Тип поля: **необязательное**.

#### Apply Tax

На текущий момент не используется.

Тип поля: **необязательное**.

#### SKU

Уникальный идентификатор товара.

Тип поля: **обязательное**.

#### VAT Percent

На текущий момент не используется.

Тип поля: **необязательное**.

#### Include eBay Product Details

На текущий момент не используется.

Тип поля: **необязательное**.

#### Domestic Shipping P1...P4 Cost

На текущий момент не используется.

Тип поля: **необязательное**.

#### Domestic Shipping P1...P4 Additional Cost

На текущий момент не используется.

Тип поля: **необязательное**.

#### Domestic Shipping P1...P4 Surcharge

На текущий момент не используется.

Тип поля: **необязательное**.

#### International Shipping P1...P5 Cost

На текущий момент не используется.

Тип поля: **необязательное**.

#### International Shipping P1...P5 Additional Cost

На текущий момент не используется.

Тип поля: **необязательное**.

#### International Shipping P1...P5

На текущий момент не используется.

Тип поля: **необязательное**.

#### TemplateName

На текущий момент не используется.

Тип поля: **необязательное**.

#### CustomFields

На текущий момент не используется.

Тип поля: **необязательное**.

#### Eligible For EbayPlus

На текущий момент не используется.

Тип поля: **необязательное**.

#### LocationID

Идентификатор склада в системе Ebay.

Тип поля: **необязательное**.

#### FulfillmentType

На текущий момент не используется.

Тип поля: **необязательное**.

#### Available

На текущий момент не используется.

Тип поля: **необязательное**.

#### FulfillmentTime

На текущий момент не используется.

Тип поля: **необязательное**.

#### Compatible Product 1..2

Используется для обозначения товаров, которые являются запчастями для транспорта.

Пример: Make=Toyota|Model=Corolla|Year=2000

Тип поля: **необязательное**.

### Листинг Etsy

Скачивание:

```json http
{
  "method": "get",
  "url": "v1/etsy/listing/file_download/"
  "headers": {
    "Content-Type": "text/csv"
  }
}
```

Загрузка:

```json http
{
  "method": "post",
  "url": "v1/etsy/listing/file_upload/"
  "headers": {
    "Content-Type": "multipart/form-data"
  }
}
```

> При загрузке необходимо передать ID канала (channel_id), к которому необходимо привязать листинги из фида.

Пример фида листинга Etsy: [etsy_listing_feed.csv](../etsy_listing_feed.csv)

Структура фида листинга Etsy:

sku |	parent_sku | title | description | quantity | price |	currency_code |	category_id |	state |	who_made |	when_made |	is_supply |	language |	is_digital |	file_data |	is_customizable |	non_taxable |	shipping_template_id |	shop_section_id |	tags |	styles |	materials |	property_name_1 |	property_values_1 |	property_name_2 |	property_values_2 |	attribute_name_1 |	attribute_values_1 |	attribute_name_2 |	attribute_values_2 |	attribute_name_3 |	attribute_values_3 |	attribute_name_4 |	attribute_values_4 |	attribute_name_5 |	attribute_values_5 |	main_image_url |	variation_image_url |	extra_image_url_1 |	extra_image_url_2 |	extra_image_url_3 |	extra_image_url_4 |	extra_image_url_5 |	extra_image_url_6 |	extra_image_url_7 |	extra_image_url_9 |	extra_image_url_8
---------|----------|---------
etsy_product_sku | | Наименование товара | Описание товара | 1 | 1.0 | USD | 257 | i_did | made_to_order|	FALSE |	en-US |	FALSE |	FALSE |	84628853766 |	27895125 |	test,free |	retrowave,funk |	leather,wood |	Primary color |	Blue |			Primary color |	Black | https://picsum.photos/id/15/500/500	| https://picsum.photos/id/4/500/500

#### sku

Уникальный идентификатор товара.

Максимальная длина: 50 символов.

Тип поля: **обязательное**.

#### parent_sku

Идентификатор родительского товара для вариативного листинга.

Тип поля: **условное**. Требуется для вариативного листинга.

#### title

Наименование товара.

Тип поля: **обязательное**.

#### description

Описание товара.

Максимальная длина: 4000 символов.

Тип поля: **обязательное**.

#### quantity

Количество товаров в наличии.

Тип поля: **необязательное**. По умолчанию 0.

#### price

Цена товара.

Тип поля: **обязательное**.

#### currency_code

Валюта товара.

Доступна только одна валюта - USD.

Тип поля: **необязательное**.

#### category_id

Уникальный идентификатор категории в системе Etsy.

Тип поля: **необязательное**.

#### state

Стутус листинга.

Доступные варианты:

* active - активный.
* draft - черновик.

Тип поля: **необязательное**.

#### who_made

Кто является производителем.

Доступные значения:

* i_did - товар личного производства.
* collective - товар, который произведен группой лиц.
* someone_else - товар, который произведен сторонним лицом.

Тип поля: **необязательное**. По умолчанию "someone_else".

#### when_made

Временной промежуток, когда товар был произведен.

Доступные значения:

* made_to_order
* 2020_2020
* 2010_2019
* 2001_2009
* before_2001
* 2000_2000
* 1990s
* 1980s
* 1970s
* 1960s
* 1950s
* 1940s
* 1930s
* 1920s
* 1910s
* 1900s
* 1800s
* 1700s
* before_1700

Тип поля: **необязательное**. По умолчанию "made_to_order".

#### is_supply

Товар является сырьем для производства.

Доступные значения: TRUE или FALSE.

Тип поля: **необязательное**. По умолчанию FALSE.

#### language

Код языка локализации.

Тип поля: **необязательное**. По умолчанию en-US.

#### is_digital

Товар является цифровым.

Доступные значения: TRUE или FALSE.

Тип поля: **необязательное**. По умолчанию FALSE.

#### file_data

Описание цифрового товара.

Максимальная длина: 500 символов.

Тип поля: **необязательное**.

#### is_customizable

Товар является кастомизируемым.

Доступные значения: TRUE или FALSE.

Тип поля: **необязательное**. По умолчанию FALSE.

#### non_taxable

Товар не облагается налогом.

Доступные значения: TRUE или FALSE.

Тип поля: **необязательное**.

#### shipping_template_id

Идентификатор политики доставки в системе Etsy.

Тип поля: **необязательное**.

#### shop_section_id

Идентификатор магазина в системе Etsy.

Тип поля: **необязательное**.

#### tags

Список тегов, которые относятся к товару.

Тип поля: **необязательное**.

#### styles

Список стилей, которые относятся к товару.

Тип поля: **необязательное**.

#### materials

Список материалов, из которых произведен товар.

Тип поля: **необязательное**.

#### property_name_1...2

Наименование отличительной характеристики товара.

Например: цвет или размер.

Тип поля: **необязательное**.

#### property_values_1...2

Значение отличительной характеристики товара.

Тип поля: **необязательное**.

#### attribute_name_1...5

Наименование общей характеристики товара.

Например: производитель, материал.

Тип поля: **необязательное**.

#### attribute_values_1...5

Значение общей характеристики товара.

Тип поля: **необязательное**.

#### main_image_url

Ссылка на главное изображение товара.

Тип поля: **необязательное**.

#### variation_image_url

Ссылка на вариативное изображение товара (используется для вариативных листингов, когда товары отличаются, например по цвету).

Тип поля: **необязательное**.

#### extra_image_url_1...8

Ссылка на дополнительное изображение товара.

Тип поля: **необязательное**.

### Листинг Amazon

Скачивание:

```json http
{
  "method": "get",
  "url": "v1/amazon/listings/file_download/"
  "headers": {
    "Content-Type": "text/csv"
  }
}
```

Загрузка:

```json http
{
  "method": "post",
  "url": "v1/amazon/listings/file_upload/"
  "headers": {
    "Content-Type": "multipart/form-data"
  }
}
```

> При загрузке необходимо передать ID канала (channel_id), к которому необходимо привязать листинги из фида.

Пример фида листинга Amazon: [amazon_listing_feed.csv](../amazon_listing_feed.csv)

Структура фида листинга Amazon:

title | description | condition_type | condition_note | sku | asin | price | quantity | main_image_url | attribute_name_1 | attribute_name_2 | attribute_name_3 | attribute_name_4 | attribute_name_5 | attribute_value_1 | attribute_value_2 | attribute_value_3 | attribute_value_4 | attribute_value_5 | extra_image_url_1 | extra_image_url_2 | extra_image_url_3 | extra_image_url_4 | extra_image_url_5 | extra_image_url_6 | extra_image_url_7 | extra_image_url_8 | extra_image_url_9 | extra_image_url_10 | parent_sku
---------|----------|---------
Наименование товара | Описание товара | NEW | Описание состояния товара |	AMAZON_TEST_PRODUCT_SKU_31 | VdQjcPNJSa | 8733.41 |	25 |	https://picsum.photos/id/13/500/500 |	Color |	Size | | | | Red | Green | | | | https://picsum.photos/id/3/500/500 |	https://picsum.photos/id/11/500/500 | | | | | | | | | PARENT_SKU_1

#### title

Наименование товара.

Максимальная длина: 255 символов.

Тип поля: **обязательное**.

#### description

Описание товара.

Максимальная длина: 500000 символов.

Тип поля: **необязательное**.

#### condition_type

Тип состояния товара.

Доступные значения:

* 11 - новое.
* 9 - не использовано.
* 1 - использовано, как новое.
* 2 - использовано, очень хорошее состояние.
* 3 - использовано, хорошее состояние.
* 4 - использовано, приемлимое состояние.
* 10 - восстановленное.
* 5 - коллекционное, как новое.
* 6 - коллекционное, очень хорошее состояние.
* 7 - коллекционное, хорошее состояние.
* 8 - коллекционное, приемлимое состояние.

Тип поля: **необязательное**. По умолчанию 11 (новое).

#### condition_note

Описание состояния товара.

Максимальная длина: 1000 символов.

Тип поля: **необязательное**.

#### sku

Уникальный идентификатор товара.

Максимальная длина: 50 символов.

Тип поля: **необязательное**.

#### parent_sku

Уникальный идентификатор родительского товара.

Используется для обозначения товаров из одной группы (вариаций).

Тип поля: **необязательное**.

#### asin

Уникальный идентификатор товара в системе Amazon.

Максимальная длина: 50 символов.

Тип поля: **необязательное**.

#### price

Цена товара.

Тип поля: **необязательное**.

#### quantity

Количество товара в наличии.

Тип поля: **необязательное**.

#### main_image_url

Ссылка на главное изображение товара.

Тип поля: **необязательное**.

#### extra_image_url_1...10

Ссылка на дополнительное изображение товара.

Тип поля: **необязательное**.

#### attribute_name_1...5

Наименование характеристики товара.

Например: цвет или размер.

Тип поля: **необязательное**.

#### attribute_value_1...5

Значение характеристики товара.

Тип поля: **необязательное**.

### Листинги Yandex.Market

Загрузка:

```json http
{
  "method": "post",
  "url": "v1/yandex_market/listings/<marketplace_user_account_id>/upload_feed/"
  "headers": {
    "Content-Type": "multipart/form-data"
  }
}
```

> Для загрузки требуется указать marketplace_user_account_id аккаунта YandexMarket в URL.

YandexMarket использует формат XML для работы с фидами, более подробно с форматом можно ознакомится в [официальной документации](https://yandex.ru/support/partnermarket/export/yml.html).

В системе ZoneSmart фид загружается без дополнительных преобразований, как есть. Соответственно, если у вас есть полностью рабочий и составленный фид по официальной документации Yandex.Market, то вы без проблем можете загрузить его в нашу систему.

Пример фида листинга YandexMarket: [yandex_market_listing_feed.xml](../yandex_market_listing_feed.xml)