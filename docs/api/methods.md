# Методы

Для работы с заявкой используется три основных метода. Они помогут сгенерировать ссылку на платежный кабинет, сгенерировать ссылку на платежный метод, и получить актуальный статус

<!-- Вы можете отправлять не более 25 запросов в секунду, иначе вернется ошибка. В ответе в заголовке будет Retry-After, количество секунд ожидания и статус — код 429 -->

### Статус ответа

- 200 — в случае успешного сценария
- 400 — в случае некорректного формата запроса
- 401 — аутентификация не пройдена: передан неверный токен
- 403 — авторизация не пройдена: пытаетесь работать с чужой заявкой
- 422 — ошибка бизнес-логики: в текущем состоянии заявки нельзя выполнить это действие
- 500 — внутренняя ошибка сервера

## Create invoice — генерация ссылки на платежный кабинет

Этот метод позволяет создать платежный кабинет клиента на определенный продукт и в ответ получить идентификатор заявки и ссылку на платежный кабинет.
Переходя по ссылке клиенту будут доступны подключенные партнеру методы оплаты.

```
POST https://{partner_id}.terpay.ru/api/v1/invoice
```

### Параметры заявки

{% list tabs %}

- Основные

  #|
  || **Описание** | **Праметр** ||
  || ##**merchant_id**##
  string(36), required
  | Идентификатор компании ||
  || ##**order_number**##
  string, required
  | Номер заказа в системе партнера ||
  || ##**product**##
  object, required
  | Данные продукта (Описано в табе Product) ||
  || ##**name**##
  string, required
  | Имя клиента ||
  || ##**phone**##
  string, optional
  | Телефон клиента ||
  || ##**email**##
  string, required
  | Email клиента ||
  |#

- Product

  #|
  || **Описание** | **Праметр** ||
  || ##**id**##
  string(36), required
  | Идентификатор продукта ||
  || ##**price**##
  number, required
  | Цена ||
  |#

{% endlist %}

### Параметры ответа

#|
|| **Описание** | **Праметр** ||
|| ##**invoice_id**##
string(36), required
| Идентификатор заявки ||
|| ##**order_number**##
string(36), required
| Номер заказа ||
|| ##**link**##
string, required
| Ссылка на заявку ||
|#

### Пример

```bash
curl -v -XPOST -H 'Content-type: application/json' -d '{
    "order_number": "a98fb02b-1395-4970-9ca6-eac426c79e18",
    "merchant_id": "018cc670-f8d9-71e0-a1df-4b946026dcba",
    "product": {
        "id": "018cc672-759f-7213-8923-a1d08966925d",
        "price": 100500
    },
    "name": "Максим",
    "email": "bat@format.ru"
}' 'https://like.terpay.ru/api/v1/invoice'
```

## Create invoice action — генерация ссылки на платежный метод

Этот метод позволяет сгенерировать ссылку на платежный метод.
В ответ можно получить ссылку на платежный метод.

{% note info %}

Переходя по ссылке клиент попадает на сторону платежного провайдера.

{% endnote %}

```
POST https://{partner_id}.terpay.ru/api/v1/invoice/{action}
```

Доступные параметры для передачи в `action` вы можете запросить у менеджера Terpay

### Параметры заявки

{% list tabs %}

- Основные

  #|
  || **Описание** | **Праметр** ||
  || ##**merchant_id**##
  string(36), required
  | Идентификатор компании ||
  || ##**order_number**##
  string, optional
  | Номер заказа в системе партнера ||
  || ##**product**##
  object, required
  | Данные продукта (Описано в табе Product) ||
  || ##**name**##
  string, required
  | Имя клиента ||
  || ##**phone**##
  string, optional
  | Телефон клиента ||
  || ##**email**##
  string, required
  | Email клиента ||
  |#

- Product

  #|
  || **Описание** | **Праметр** ||
  || ##**id**##
  string(36), required
  | Идентификатор продукта ||
  || ##**price**##
  number, required
  | Цена ||
  |#

{% endlist %}

### Параметры ответа

#|
|| **Описание** | **Праметр** ||
|| ##**invoice_id**##
string(36), required
| Идентификатор заявки ||
|| ##**link**##
string, required
| Ссылка платежного провайдера ||
|#

### Пример

```bash
curl -v -XPOST -H 'Content-type: application/json' -d '{
    "order_number": "a98fb02b-1395-4970-9ca6-eac426c79e18",
    "merchant_id": "018cc670-f8d9-71e0-a1df-4b946026dcba",
    "product": {
        "id": "018cc672-759f-7213-8923-a1d08966925d",
        "price": 100500
    },
    "name": "Максим",
    "email": "bat@format.ru"
}' 'https://like.terpay.ru/api/v1/invoice/018cc658-eb49-734a-959b-80edb773cbb4'
```

## Info — текущее состояние заявки

Метод позволяет получить информацию о платеже.

```
GET https://{partner_id}.terpay.ru/api/v1/invoice/{order_number}
```

Где `order_number` – это номер заказа

### Параметры заявки

#|
|| **Описание** | **Праметр** ||
|| ##**orderNumber**##
string, required
| Номер заказа ||
|| ##**Authorization**##
header, optional
| Bearer Authentication ||
|#

### Параметры ответа

{% list tabs %}

- Основные

  #|
  || **Описание** | **Праметр** ||
  || ##**id**##
  string, required
  | Номер заказа. Соответствует orderNumber ||
  || ##**sum**##
  string, required
  | Стоимость ||
  || ##**status**##
  object, required
  | Статус (Описано в табе Status) ||
  || ##**product**##
  object, required
  | Продукт (Описано в табе Product) ||
  || ##**contact**##
  object, required
  | Контакт (Описано в табе Contact) ||
  |#

- Status

  #|
  || **Описание** | **Праметр** ||
  || ##**code**##
  string, required
  | Код статуса ||
  || ##**title**##
  string, required
  | Название статуса ||
  || ##**changed_at**##
  string, required
  | Дата изменения статуса ||
  |#

- Contact

  #|
  || **Описание** | **Праметр** ||
  || ##**name**##
  string, required
  | Имя ||
  || ##**phone**##
  string, required
  | Телефон ||
  || ##**email**##
  string, required
  | Email ||
  |#

- Product

  #|
  || **Описание** | **Праметр** ||
  || ##**id**##
  string, required
  | Идентификатор продукта ||
  || ##**name**##
  string, required
  | Название продукта ||
  || ##**price**##
  numeric, required
  | Стоимость продукта ||
  |#

{% endlist %}

### Пример

```bash
curl -v -H 'Authorization: Bearer <TOKEN>' -H 'Content-type: application/json' 'https://like.terpay.ru/api/v1/invoice/{orderNumber}'
```
