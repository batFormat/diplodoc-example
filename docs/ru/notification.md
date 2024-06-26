# Нотификации

Если вы хотите оперативно отслеживать состояние заказа, можно подписаться на HTTP-уведомления, или вебхуки. Это вид нотификации через оповещения на сайте, сформированные с помощью HTTP-запроса.

Вебхуки пригодятся, когда объект API меняется без вашего участия. Для получения вебхуков нужно указать адрес для уведомлений

{% note info %}

Поддерживается только статичный адрес, передайте его менеджеру Terpay

{% endnote %}

Все нотификации будут отправлены на ваш адрес используя POST метод

Вебхуки отправляются на события:

- **PAYMENT_STATUS_CHANGE** — по заявке изменился статус

## Подлинность

Обязательно проверяйте подлинность полученных HTTP-нотификаций, с помощью проверки подписи тела запроса

Где передается подпись?
Как собрать подпись на своей стороне?

## Пример

```json
{
  "event": "PAYMENT_STATUS_CHANGE",
  "data": { "sum": 10000, "status": "completed" }
}
```
