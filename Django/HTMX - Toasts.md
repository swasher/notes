# HTMX: Toasts

Owner: Alexey Swasher
Last edited time: December 5, 2023 11:18 PM

https://blog.benoitblanchon.fr/django-htmx-toasts/
https://blog.benoitblanchon.fr/django-htmx-messages-framework/

## Шаблон

На странице в самом конце должен быть шаблон для будущих тостов:

```html
<div data-toast-container class="toast-container position-fixed top-0 end-0 p-3">
  <div data-toast-template class="toast align-items-center border-0" role="alert" aria-live="assertive" aria-atomic="true">
    <div class="d-flex">
      <div data-toast-body class="toast-body"></div>
      <button type="button" class="btn-close btn-close-white me-2 m-auto" data-bs-dismiss="toast" aria-label="Close"></button>
    </div>
  </div>
</div>
```

## View

Вьюха в ответ на htmx вызов делает ответ типа

```python
from django.contrib import messages
def order_edit(request, pk=None):
    messages.success(request, f"Order {order.order} save successfully")
    return HttpResponse(status=200)

```

В котором в ответ включается стандартный джанговский message

## Middleware

Лежит в `core/middleware/toast_middleware.py`

Для каждого запроса, сделанного с помощью HTMX (обнаруженного с помощью заголовка HX-Request), это middleware помещает все сообщения в заголовок HX-Trigger ответа. В нашем случае этот заголовок после middleware будет содержать:

`Hx-Trigger: {"messages": [{"message": "Order 23-0220 save successfully", "tags": "text-white bg-success"}]}`

## Js

На каждую страницу нужно включить `js`, который лежит в статике.
Я поместил в `base.html`

`<script src="{% static 'js/toasts.js' %}" defer></script>`

Он содержит простой код для прослушивания события и вызова отрисовки toasta

```jsx
htmx.on("messages", (event) => {
  console.log(event.detail.value[0])
  event.detail.value.forEach(createToast)
})

function createToast(message) {
    тут js код, который собственно показывает toast'ы
}
```

## [Settings.py](http://settings.py/)

Чтобы тосты имели правильный цвет в стиле bootstrap:

```python
from django.contrib import messages
MESSAGE_TAGS = {
    messages.DEBUG: "bg-light",
    messages.INFO: "text-white bg-primary",
    messages.SUCCESS: "text-white bg-success",
    messages.WARNING: "text-dark bg-warning",
    messages.ERROR: "text-white bg-danger",
}
```
