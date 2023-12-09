Модальная форма HTMX + Bootstrap 5
=================================

Идея взята отсюда https://blog.benoitblanchon.fr/django-htmx-modal-form/

## Делаем кнопку, которая вызывает модал

```
<!-- Button trigger modal form-->
<button hx-get="{% url 'kinorium' %}" type="button" class="btn btn-primary pt-1"
        hx-target="#dialog" hx-swap="innerHTML">
    Update list of movies
</button>
```

Тут важно следующее. Мы не пишем в классах кнопки `data-bs-toggle="modal" data-bs-target="#modalform"`, как это сделано в примерах Bootstrap.
Потому что мы будем показывать/скрывать модальную форму вручную с помощью htmx.


## Заглушка для модала

```
<!-- Modal form-->
<div id="modalform" class="modal modal-blur fade" style="display: none" aria-hidden="false" tabindex="-1">
    <div id="dialog" class="modal-dialog modal-dialog-centered"></div>
</div>
```

Заглушка может быть в любом месте.
Тут надо обратить внимание на следующие вещи.
1. Мы не 
