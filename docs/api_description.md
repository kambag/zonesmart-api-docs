---
tags: [general]
---

# Описание API Zonesmart


## Введение

### Локальные и удаленные CRUD-вызовы
Под локальными CRUD-вызовами понимаются вызовы к API Zonesmart для создания/получения/обновления/удаления сущностей (экземпляров моделей БД) в системе Zonesmart (операции в БД). 

Под удаленными CRUD-вызовами понимаются вызовы к API маркетплейса для публикации на маркетплейсе/загрузки из маркетплейса/обновления в маркетплейсе/удаления с маркетплейса сущностей данного маркетплейса через API Zonesmart.


### Статусы сущностей
Все сущности, для которых доступны как локальные, так и удаленные CRUD-вызовы, обладают статусом. Возможные значения статуса: 'draft', 'ready_to_be_published', 'published' и 'update_required'.

В зависимости от статуса для сущностью могут быть доступны различные операции (соответственно и вызовы API).

Статус 'draft' (черновик) означает, что сущность нельзя опубликовать на маркетплейсе, так как у нее не заполнены какие-то нужные поля. Для сущностей с этим статусом доступны только локальные CRUD-вызовы, но не операция публикации на маркетплейсе.

Статус 'ready_to_be_published' означает, что сущность создана локально, все ее необходимые для публикации на маркетплейсе поля заполнены, но она еще не была опубликована.

Статус 'published' означает, что сущность опубликована на маркетплейсе и полностью синхронизирована, то есть значения соответствующих полей локально (в БД) и в маркетплейсе одинаковы. Для сущности с этим статусом доступна операция удаления с маркетплейса. 

Статус 'update_required' означает, что сущность опубликована на маркетплейсе, но для нее требуется синхронизация, так как она обновлена локально (в БД), но еще не обновлена на маркетплейсе, то есть значения каких-то соответствующих полей различаются. Для сущности с данным статусом доступны операции обновления на маркетплейсе и удаления с маркетплейса.


## Описание папок документации

Ниже приводится объяснение назначения папок документации, содержащих описание тематически связанных эндпоинтов API. По ходу описания последовательно вводится специфичная терминология Zonesmart API и приводятся примеры работы с ним.


### Zonesmart

В данном разделе приводится описание папок с эндпоинтами, не относящимися к конкретному маркетплейсу.


#### User
Локальные CRUD-вызовы для пользователя Zonesmart, верификация телефона и email, смена пароля. 

Не создав пользователя, невозможно совершать вызовы к API Zonesmart, требующие авторизации, так как получить токен авторизации в системе возможно лишь в обмен на логин и пароль пользователя.

Важные вызовы:

1) [Получение информации о своем пользователе](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1users~1me~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1users~1me~1/get&group=master)


#### Auth
Вызовы для получения и обновления токенов авторизации в системе. Аутентификация клиента основана на стандарте JSON Web Token (JWT). Для авторизованного запроса к API Zonesmart требуется передача JWT-токена. Токен имеет время жизни и требует периодического обновления. Для создания токена нужны email и пароль зарегистрированного активного аккаунта.

Важные вызовы:

1) [Создание токена](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1auth~1jwt~1create~1/post?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1auth~1jwt~1create~1/post&group=master)

2) [Обновление токена](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1auth~1jwt~1refresh~1/post?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1auth~1jwt~1refresh~1/post&group=master)


#### Marketplace
Получение доступных для интеграции маркетплейсов.

Маркетплейсы существуют в БД изначально, их не требуется создавать.

Важные вызовы:

1) [Получение списка маркетплейсов](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1marketplace~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1marketplace~1/get&group=ebay_listing)
2) [Получение маркетплейса](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1marketplace~1%7Bid%7D~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1marketplace~1{id}~1/get&group=ebay_listing)


#### MarketplaceUserAccount
Получение созданных аккаунтов маркетплейсов пользователя. Аккаунт маркетплейса - сущность, связывающая пользователя маркетплейс.

Аккаунт маркетплейса создается автоматически при подключении пользователем своей учетной записи в одном из маркетплейсов. Например, при подключении пользователем двух своих личных учетных записей eBay автоматически будут созданы 2 аккаунта маркетплейса, ссылающихся на маркетплейс eBay и данного пользователя (различия будут в полях 'id' и 'username').
 
Аккаунтов маркетплейсов у пользователя может быть неограниченно много при том условии, что они все соответствуют разным учетным записям, то есть одну и ту же учетную запись нельзя подключить более одного раза.


Важные вызовы:

1) [Получение списка аккаунтов маркетплейсов](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1account~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1account~1/get&group=ebay_listing)
2) [Получение аккаунта маркетплейса](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1account~1%7Bid%7D~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1account~1{id}~1/get&group=ebay_listing)


#### Domain
Получение доменов маркетплейсов. Домены привязаны к маркетплейсам и имеют уникальный идентификатор 'code'. У любого маркетплейса есть хотя бы один домен (Etsy и AliExpress имеют единственный домен), а некоторые маркетплейсы имеют много доменов для разных регионов (например, Amazon и eBay).

Домены маркетплейса существуют в БД изначально, их не требуется создавать.

Важные вызовы:
1) [Получение списка доменов](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1marketplace~1%7Bmarketplace_id%7D~1domain~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1marketplace~1{marketplace_id}~1domain~1/get&group=ebay_listing)
2) [Получение домена](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1marketplace~1%7Bmarketplace_id%7D~1domain~1%7Bid%7D~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1marketplace~1{marketplace_id}~1domain~1{id}~1/get&group=ebay_listing)


#### Channel
Локальные CRUD-вызовы для каналов продаж аккаунтов маркетплейсов. 

Канал продаж - сущность, связывающая аккаунт маркетплейса и домен маркетплейса. У аккаунта маркетплейса может быть только по одному каналу продаж для каждого домена маркетплейса. К примеру, в eBay нельзя создать 2 канала продаж для немецкого домена 'ebay.de', привязанных к одному и тому же аккаунту маркетплейса, но можно создать по одному каналу продаж домена 'ebay.de' для каждого аккаунта маркетплейса eBay.

Для работы с маркетплейсами eBay и Amazon требуется создать хотя бы один канал продаж. Каналы продаж для аккаунтов Etsy и AliExpress создаются автоматически при подключении аккаунта (по одному каналу на каждый аккаунт, так как на этих маркетплейсах по одному домену).

Важные вызовы:
1) [Получение списка каналов продаж](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1channel~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1channel~1/get&group=ebay_listing)
2) [Получение канала продаж](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1channel~1%7Bid%7D~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1channel~1{id}~1/get&group=ebay_listing)
3) [Создание канала продаж](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1channel~1/post?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1channel~1/post&group=ebay_listing)
4) [Удаление канала продаж](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1channel~1%7Bid%7D~1/delete?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1channel~1{id}~1/delete&group=ebay_listing)


#### BaseListing
Локальные CRUD-вызовы для базовых листингов и их базовых продуктов. 

Базовый листинг - сущность, которая служит для хранения информации о товаре и используется для создания листингов. На основе базового листинга можно создать неограниченное число листингов для любого маркетплейса без повторного ввода повторяющейся информации, вроде названия и описания товара.

К любому базовому листингу должен быть привязан хотя бы один базовый продукт. Базовый продукт содержит информацию, которая может различаться между вариациями листинга, вроде SKU, цены и количества. Если товар имеет вариации, то к базовому листингу должно быть привязано равное количество базовых продуктов. 

К базовому продукту могут быть привязаны атрибуты, представляющие из себя пары "ключ-значение". У всех продуктов, привязанных к листингу, должен быть хотя бы один атрибут, по которому они различаются, то есть атрибут с одним и тем же ключем, но разными значениями.

Продемонстрируем workflow на примере футболок. Если пользователь хочет продавать футболки на нескольких маркетплейсах, то сперва он должен создать по базовому листингу для футболок разных брендов (различающихся в том числе названием и описанием). Если футболки одного бренда различаются размером и цветом, то для каждой из вариаций должен быть создан базовый продукт с привязанными к нему двумя атрибутами: "цвет-значение" и "размер-значение" (для двух размеров и трех цветов будет таким образом 2*3=6 базовых продуктов с двумя атрибутами каждый). Если футболки одного бренда не имеют вариаций, то нужно создать только один продукт, причем привязывать к нему атрибуты допустимо, но необязательно.

Важные вызовы:
1) [Получение списка базовых листингов]()
2) [Получение базового листинга]()
3) [Создание базового листинга]()
4) [Обновление базового листинга]()
5) [Удаление базового листинга]()
6) [Создание листинга маркетплейса на основе базового листинга]()


#### Order
Получение заказов со всех подключенных аккаунтов маркетплейсов и создание отправлений для них (если это поддерживается API маркетплейса).

Заказ (базовый заказ) - сущность, содержащая информацию о заказе, полученном с какого-либо маркетплейса, причем набор полей данной модели не зависит от того, к какому маркетплейсу относится заказ (проводится маппинг соответствующих базовых полей заказов с неизбежной потерей потерей специфичных, но не существенных, для маркетплейса полей). Базовый заказ содержит информацию о дате, статусе, стоимости и покупателе. 

К каждому заказу привязан хотя бы один элемент заказа, соответствующий одному заказанному товару. Есть покупатель заказал (в рамках одного заказа) несколько различных товаров (листингов какого-то маркетплейса), то для каждого из товаров создается по элементу заказа. Элемент заказа содержит информацию о названии, SKU, цене и заказанном количестве единиц товара. Если товар из заказа представлен в системе Zonesmart в виде базового листинга, то заказ привязывается к этому листингу, что открывает дополнительные возможности для его обработки.

Отправление заказа - это сущность, содержащая информацию о посылке, которую продавец отправляет покупателю, сделавшему заказ на маркетплейсе. Отправление может содержать все элементы заказа в полном объеме, либо только часть заказа. К отправлению заказа привязаны элементы отправления, каждый из которых привязан к одному из элементов заказа и содержит количество единиц данного элемента заказа, содержащееся в отправлении.

Для того чтобы заказ считался полностью обработанным, нужно локально создать одно или несколько отправлений, содержащие суммарно в своих элементах все элементы заказа в полном объеме, а затем отметить каждое из этих отправлений отправленными (статус изменится с "draft" на "shipped"), то есть создать отправления удаленно.

В зависимости от маркетплейса, заказ либо возможно отправить только полностью одним отправлением, либо по частям несколькими отправлениями.

Ограничения по созданию отправлений на разных маркетплейсах:

1) Ограничения eBay. Можно создать несколько отправлений, но нельзя дробить элемент заказа на несколько отправлений, то есть иметь более одного отправления, элементы которых ссылались бы на один и тот же элемент заказа. Таким образом, элемент заказа eBay нужно отправлять одним отправлением в полном объеме.

2) Ограничения Etsy. Весь заказ должен быть отправлен одним отправлением. Таким образом, для обработки заказа Etsy нужно создать единственное отправление, элементы которого содержали бы все элементы заказа в полном объеме.


Важные вызовы:
1) [Получение списка заказов](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1order~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1order~1/get&group=ebay_listing)
2) [Получение заказа](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1order~1%7Bid%7D~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1order~1{id}~1/get&group=ebay_listing)
3) [Скачивание заказов с подключенных маркетплейсов](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1order~1sync~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1order~1sync~1/get&group=ebay_listing)
4) [Локальное создание отправления заказа](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1order~1%7Border_id%7D~1shipment~1/post?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1order~1{order_id}~1shipment~1/post&group=order)
5) [Локальное обновление отправления заказа](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1order~1%7Border_id%7D~1shipment~1%7Bshipment_id%7D~1/put?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1order~1{order_id}~1shipment~1{shipment_id}~1/put&group=order)
6) [Локальное удаление отправления заказа](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1order~1%7Border_id%7D~1shipment~1%7Bshipment_id%7D~1/delete?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1order~1{order_id}~1shipment~1{shipment_id}~1/delete&group=order)
7) [Удаленное создание отправления заказа](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1order~1%7Border_id%7D~1shipment~1%7Bshipment_id%7D~1mark_as_shipped~1/post?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1order~1{order_id}~1shipment~1{shipment_id}~1mark_as_shipped~1/post&group=order)
8) [Получение неотправленных элементов заказа](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1order~1%7Border_id%7D~1shipment~1items_to_ship~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1zonesmart~1order~1{order_id}~1shipment~1items_to_ship~1/get&group=order)


### Ebay

В данном разделе приводится описание папок с эндпоинтами, относящимися к маркетплейсу eBay. 


#### Ebay Account
Локальные CRUD-вызовы для аккаунта eBay. 

Для работы с маркетплейсом пользователь должен добавить в систему один или несколько своих аккаунтов данного маркетплейса (например, два аккаунта eBay, зарегистрированные на два разных email). 

Подключение аккаунта eBay осуществляется по протоколу OAuth. Пользователь перенаправляется на сайт eBay, где он должен авторизоваться через подключаемый аккаунт и принять условия предоставления доступа приложения Zonesmart к своему аккаунту, нажав соответствующую кнопку. После дачи согласия, пользователь перенаправляется обратно на сайт Zonesmart, завершая подключение аккаунта. Интеграция будет не будет успешной, если аккаунт не является активным (например, был деактивирован сайтом eBay) или уже был до этого подключен другим пользователем Zonesmart.

Для каждого подключенного аккаунта eBay создается единственный аккаунт маркетплейса (связь "один к одному"), который также удаляется при удалении аккаунта eBay из системы.

Аккаунт eBay хранит токены доступа к API eBay, полученные при подключении (авторизации приложения Zonesmart в учетной записи eBay пользователя). Токен доступа к eBay API обновляется автоматически вплоть до устаревания токена обновления, после чего требуется обновление аккаунта eBay (по сути повторное подключение по протоколу OAuth).

После подключения аккаунта eBay автоматически происходит скачивание всех связанных с ним сущностей, вроде листингов, складов и политик eBay.

Не подключив хотя бы одного аккаунта, невозможно взаимодействовать с eBay.

Важные вызовы:
1) [Получение списка аккаунтов eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1account~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1account~1/get&group=ebay_listing)
2) [Получение аккаунта eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1account~1%7Bid%7D~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1account~1{id}~1/get&group=ebay_listing)
3) [Получение ссылки для авторизации приложения на eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1account~1get_login_url~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1account~1get_login_url~1/get&group=ebay_listing)
4) [Подключение или обновление аккаунта eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1account~1complete~1/post?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1account~1complete~1/post&group=ebay_listing)
5) [Удаление аккаунта eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1account~1%7Bid%7D~1/delete?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1account~1{id}~1/delete&group=ebay_listing)


#### Ebay Category
Получение категорий товаров для различных доменов eBay и их аспектов.

Аспекты категории представляют из себя возможные ключи и значения для атрибутов продуктов листингов eBay. Для каждой категории аспекты свои, причем некоторые из них могут быть обязательными для заполнения при создании листинга eBay. Таким образом, получение аспектов eBay может понадобиться при создании листинга eBay после того, как пользователь выберет категорию eBay.

Категории доступны в базе данных без предварительного скачивания или создания и обновляются автоматически.

Важные вызовы:
1) [Получение списка категорий eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1category~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1category~1/get&group=ebay_listing)
2) [Получение категории eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1category~1%7Bid%7D~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1category~1{id}~1/get&group=ebay_listing)
3) [Получение аспектов категории eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1category~1%7Bid%7D~1aspect~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1category~1{id}~1aspect~1/get&group=ebay_listing)


#### Ebay Location
Локальные и удаленные CRUD-вызовы для складов eBay. Склады можно создавать и публиковать на eBay через систему Zonesmart, либо создавать на сайте eBay и загружать в Zonesmart. Автоматически с eBay склады загружаются только при подключении нового аккаунта eBay. То же относится к политикам eBay.

Не имея локально (путем скачивания с eBay или путем локального создания и загрузки на eBay) хотя бы одного склада eBay, невозможно создать листинг eBay.

Важные вызовы:
1) [Получение списка складов eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1location~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1location~1/get&group=ebay_listing)
2) [Получение склада eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1location~1%7Bid%7D~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1location~1{id}~1/get&group=ebay_listing)
3) [Скачивание складов с eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1location~1remote_download_list~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1location~1remote_download_list~1/get&group=ebay_listing)


#### Ebay Fulfillment Policy
Локальные и удаленные CRUD-вызовы для политик фулфилмента (доставки) eBay.

Не имея локально (путем скачивания с eBay или путем локального создания и загрузки на eBay) хотя бы одной политики фулфилмента eBay, невозможно создать листинг eBay.

Важные вызовы:
1) [Получение списка политик фулфилмента eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1policy~1fulfillment~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1policy~1fulfillment~1/get&group=ebay_listing)
2) [Получение политики фулфилмента eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1policy~1fulfillment~1%7Bid%7D~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1policy~1fulfillment~1{id}~1/get&group=ebay_listing)
3) [Скачивание политик фулфилмента с eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1policy~1fulfillment~1remote_download_list~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1policy~1fulfillment~1remote_download_list~1/get&group=ebay_listing)


#### Ebay Payment Policy
Локальные и удаленные CRUD-вызовы для политик оплаты eBay.

Не имея локально (путем скачивания с eBay или путем локального создания и загрузки на eBay) хотя бы одной политики оплаты eBay, невозможно создать листинг eBay.

Важные вызовы:
1) [Получение списка политик оплаты](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1policy~1payment~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1policy~1payment~1/get&group=ebay_listing)
2) [Получение политики оплаты eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1policy~1payment~1%7Bid%7D~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1policy~1payment~1{id}~1/get&group=ebay_listing)
3) [Скачивание политик оплаты с eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1policy~1payment~1remote_download_list~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1policy~1payment~1remote_download_list~1/get&group=ebay_listing)


#### Ebay Return Policy
Локальные и удаленные CRUD-вызовы для политик возврата eBay.

Не имея локально (путем скачивания с eBay или путем локального создания и загрузки на eBay) хотя бы одной политики возврата eBay, невозможно создать листинг eBay.

Важные вызовы:
1) [Получение списка политик возврата eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1policy~1return~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1policy~1return~1/get&group=ebay_listing)
2) [Получение политики возврата eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1policy~1return~1%7Bid%7D~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1policy~1return~1{id}~1/get&group=ebay_listing)
3) [Скачивание политик возврата с eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1policy~1return~1remote_download_list~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1policy~1return~1remote_download_list~1/get&group=ebay_listing)


#### Ebay Listing
Локальные и удаленные CRUD-вызовы для листингов eBay. Для создания листингов необходимо использовать категории, склады и все политики eBay, существующие в системе Zonesmart. Для работы с листингом eBay необходимо, чтобы он был привязан к базовому листингу. Листинг eBay можно создать на основе базового листинга, либо скачать с аккаунта eBay пользователя и привязать к базовому листингу пользователя. 

Связь между базовым листингом используется для управления ценами и количеством единиц публикуемых на маркетплейсах листингов, относящихся к одному и тому же реальному продаваемому пользователем товару.

Подобно базовому листингу, листинг eBay имеет привязанные к нему продукты eBay, а они в свою очередь - привязанные к ним атрибуты типа "ключ-значение". Между продуктами и атрибутами базового листинга и продуктами и атрибутами созданного на его основе листинга eBay существует однозначное соответствие. 

Важные вызовы:

* Локальные вызовы:
1) [Получение списка листингов eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1/get&group=ebay_listing) (вместе с продуктами и атрибутами)
2) [Получение листинга eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1%7Bid%7D~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1{id}~1/get&group=ebay_listing) (вместе с продуктами и атрибутами)
3) [Локальное создание листинга eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1/post?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1/post&group=ebay_listing) (без создания продуктов и атрибутов)
4) [Локальное создание продукта eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1%7Blisting_id%7D~1product~1/post?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1{listing_id}~1product~1/post&group=ebay_listing) (без создания атрибутов)
5) [Локальное создание атрибута продукта eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1%7Blisting_id%7D~1product~1%7Bproduct_id%7D~1specification~1/post?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1{listing_id}~1product~1{product_id}~1specification~1/post&group=ebay_listing)
6) [Локальное обновление листинга eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1%7Bid%7D~1/put?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1{id}~1/put&group=ebay_listing) (без обновления продуктов и атрибутов)
7) [Локальное обновление продукта eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1%7Blisting_id%7D~1product~1%7Bproduct_id%7D~1/put?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1{listing_id}~1product~1{product_id}~1/put&group=ebay_listing) (без обновления атрибутов)
8) [Локальное обновление атрибута продукта eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1%7Blisting_id%7D~1product~1%7Bproduct_id%7D~1specification~1%7Bid%7D~1/put?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1{listing_id}~1product~1{product_id}~1specification~1{id}~1/put&group=ebay_listing)
9) [Локальное удаление листинга eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1%7Bid%7D~1/delete?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1{id}~1/delete&group=ebay_listing) (вместе с продуктами и атрибутами)
10) [Локальное удаление продукта eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1%7Blisting_id%7D~1product~1%7Bproduct_id%7D~1/delete?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1{listing_id}~1product~1{product_id}~1/delete&group=ebay_listing) (вместе с атрибутами)
11) [Локальное удаление атрибута продукта eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1%7Blisting_id%7D~1product~1%7Bproduct_id%7D~1specification~1%7Bid%7D~1/delete?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1{listing_id}~1product~1{product_id}~1specification~1{id}~1/delete&group=ebay_listing)

* Удаленные вызовы:
1) [Скачивание листингов с eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1remote_download_list~1%7Bchannel_id%7D~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1remote_download_list~1{channel_id}~1/get&group=ebay_listing)
2) [Создание или обновление листинга на eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1%7Bid%7D~1remote_sync~1/post?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1{id}~1remote_sync~1/post&group=ebay_listing)
3) [Удаление листинга с eBay](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1%7Bid%7D~1remote_delete~1/delete?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1{id}~1remote_delete~1/delete&group=ebay_listing)

* Другое:
1) [Привязывание листинга eBay к базовому листингу](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1%7Bid%7D~1link~1/post?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1{id}~1link~1/post&group=ebay_listing)
2) [Отвязывание листинга eBay от базового листинга](https://stoplight.io/p/docs/gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1%7Bid%7D~1unlink~1/get?srn=gh/kambag/zonesmart-api-docs/reference/zonesmart.yaml/paths/~1v1~1ebay~1listing~1{id}~1unlink~1/get&group=ebay_listing)


### Etsy

В данном разделе приводится описание папок с эндпоинтами, относящимися к маркетплейсу Etsy.


#### Etsy Account


#### Etsy Category


#### Etsy Shipping Template


#### Etsy Listing


### Amazon

В данном разделе приводится описание папок с эндпоинтами, относящимися к маркетплейсу Amazon.


#### Amazon Account


#### Amazon Category

