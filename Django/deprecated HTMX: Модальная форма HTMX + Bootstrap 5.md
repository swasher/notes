Модальная форма HTMX + Bootstrap 5
=================================

Идея взята отсюда https://blog.benoitblanchon.fr/django-htmx-modal-form/

## Делаем кнопку, которая вызывает модал

```html
<!-- Button trigger modal form-->
<button type="button" class="btn btn-primary pt-1"
        hx-get="{% url 'our_view' %}"
        hx-target="#dialog"
        hx-swap="innerHTML">
    Update list of movies
</button>
```

Тут важно следующее. Мы не пишем в классах кнопки `data-bs-toggle="modal" data-bs-target="#modalform"`, как это сделано в примерах Bootstrap.
Потому что мы будем показывать/скрывать модальную форму вручную с помощью htmx, а не с помощью bootstrap.
Так сделано потому, что каждый раз при действии пользователя View возрашает новую форму, которая может показывать пользователю поля, не прошедшии валидацию, или что-либо еще.
Если View возвращает status=204, окно закрывается и считается, что пользователь удачно завершил ввод данных.


## Заглушка для модала

```html
<!-- Modal form-->
<div id="modalform" class="modal modal-blur fade" style="display: none" aria-hidden="false" tabindex="-1">
    <div id="dialog" class="modal-dialog modal-dialog-centered">
       {# в этом месте DOM поместится то, что вернет бекенд - содержимое модала #}
    </div>
</div>
```

Заглушка может быть в любом месте в коде страницы.
Id внутреннего `div` должно совпадать с hx-target на кнопке 
Тут надо обратить внимание на следующие вещь. Верстка модального окна в бутстрапе выглядит так:

```html
<div class="modal" tabindex="-1">
  <div class="modal-dialog">

<!-- Начало шаблона -->
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title">Modal title</h5>
        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
      </div>
      <div class="modal-body">
        <p>Modal body text goes here.</p>
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
        <button type="button" class="btn btn-primary">Save changes</button>
      </div>
    </div>
<!-- Конец шаблона -->

  </div>
</div>
```

Два div-а `modal` и `modal-dialog` у нас остаются на главной странице, а встаиваеммый шаблон у нас начинается с `modal-content`

## View

При нажатии на кнопку вьюха должна вернуть кусок html, который представляет собой форму, которая встраивается в `<div id="dialog" class="modal-dialog modal-dialog-centered"></div>`:

```python
def our_view(request):
    htmx = request.htmx

    if request.method == "POST":
    # выполняется, когда пользователь отправляет заполненную форму
        form = MovieForm(request.POST)
        if form.is_valid():
            form.save()
            return HttpResponse(status=204)
    else:
        form = MovieForm()

    if htmx and htmx.target == 'dialog' and request.method == 'GET':
        # выполняется когда показываем форму
        form = Form()

    return render(request, 'partials/upload_kinorium_form.html', {'form': form,})
```

Тут используется библиотека `django-htmx`, это не важно в настоящем контексте.

## Form

Для примера используется форма для аплода двух файлов и встроенного прогресс бара. Мы рендерим форму вручную, без использования объекта Form().

```html
<div class="modal-content">
    <div class="modal-header">
        <h1 class="modal-title fs-5" id="exampleModalLabel">Upload Kinorium CSV</h1>
        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
    </div>

    <div class="modal-body">
        <form id='form' method="post" hx-encoding='multipart/form-data' hx-post='' hx-target="#answer">
            {% csrf_token %}
            <div class="col mb-3">
                <label for="input1" class="form-label">File <b>movie list</b></label>
                <input type="file" name='file_movie_list' class="form-control" id="input1">
            </div>
            <div class="col mb-3">
                <label for="input2" class="form-label">File <b>votes</b></label>
                <input type="file" name='file_votes' class="form-control m-1" id="input2">
            </div>
        </form>
    </div>

    <div class="modal-footer">
        <button type="submit" form="form" class="btn btn-primary">
            Upload
        </button>
        <progress id='progress' value='0' max='100'></progress>

        <div id="answer"></div>
        {% if uploaded_file_url %}
            <p>File uploaded at: <a href="{{ uploaded_file_url }}">{{ uploaded_file_url }}</a></p>
        {% endif %}
    </div>

</div>

<script>
    {# Это для отображения прогресс бара при заливке CSV#}
    htmx.on('#form', 'htmx:xhr:progress', function (evt) {
        htmx.find('#progress').setAttribute('value', evt.detail.loaded / evt.detail.total * 100)
    });
</script>
```

## HTMX

На странице всраиваем javascript следующего содержания (назовем его `dialog.js`)

```js
;(function () {
  const modal = new bootstrap.Modal(document.getElementById("modalform"))  // ЭТО ID ВНЕШНЕГО DIV ИЗ 'ЗАГЛУШКИ'

  htmx.on("htmx:afterSwap", (e) => {
    // Response targeting #dialog => show the modal
    if (e.detail.target.id == "dialog") {  // ЭТО ID ТАРГЕТА ИЗ hx-target
      modal.show()
    }
  })

  htmx.on("htmx:beforeSwap", (e) => {
    // Empty response targeting #dialog => hide the modal
    if (e.detail.target.id == "dialog" && !e.detail.xhr.response) {   // ЭТО ID ТАРГЕТА ИЗ hx-target
      modal.hide()
      e.detail.shouldSwap = false
    }
  })

  // Remove dialog content after hiding
  htmx.on("hidden.bs.modal", () => {
    document.getElementById("dialog").innerHTML = ""
  })
})()
```

Объект `modal` должен находить форму по ее id (`getElementById("modalform")`).
Когда пользователь нажимает на кнопку, происходит следующее:
- возникает GET запрос по адресу `our_view` (`hx-get="{% url 'our_view' %}"`)
- view возвращает в своем ответе html с формой
- этот html встраивается в DOM (`hx-target="#dialog"`)
- на этом этапе само содержимое модала еще не видно
- при изменении DOM htmx запускает событие `htmx:afterSwap`
- наш яваскрипт слушает это событие и при его возникновении выполняет modal.show()
- модальное окно появляется на экране

После закрытия окна:
- возникает событие beforeSwap
- если ответ (e.detail.xhr.response) пустой - то есть измение DOM вызвано действием пользователя, то
  - модальное окно скрывается (modal.hide())
- при скрытии окна возникает специфическое для Bootstrap событие "hidden.bs.modal"
- в нем мы удаляем кусок DOM, который представляет собой внутренности модального окна (document.getElementById("dialog").innerHTML = "")
