Часто бывает, что после действий пользователя нужно обновить данные на странице, не связанные прямо с элементами, с которыми взаимоедействовал пользователь.

Это легко сделать, используя следующую технику.

Нам нужно сделать две вещи: первое, включить событие в ответ Django, и второе - добавить прослушку события на ту часть HTML, которая должна обновиться.

Добавляем событие в ответ Django:

```python
from django_htmx.http import trigger_client_event

@require_http_methods(["GET", "POST"])
def my_veiw(request, pk):
    response = HttpResponse(status=204)
    # or
    response = render(...)
    trigger_client_event(response, "myEventName", {}, )
    return response

def table(request):
    return render(request, 'table.html')
```

Далее в темплейте мы привязываем событие на некий элемент:

```
<table hx-get="{% url 'table' %}" hx-trigger="load, myEventName from:body">

</table>

```

Таблица будет отрисовываться при загрузке страницы, и при каждом вызове функции `my_view`
