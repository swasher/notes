Основная фишка этой техники - использование стилей bootstrap, но отказ от его js. Мы полностью контролируем модалки через htmx и свой js.
### URLS

Файл  **urls.py**

```python
path('test_modal1', views.test_modal1, name='test_modal1'),
path('test_modal2', views.test_modal2, name='test_modal2'),
```

### VIEW

Файл **views.py**. Это обычный view, возвращающий частичный html для модалки. Единственный
ньюанс - мы должны обязательно возвращать status=204 (No Content), чтобы правильно срабатывали htmx события.


```python
from django import forms

class NameForm(forms.Form):
    your_name = forms.CharField(label="Your name", max_length=100)

class SurnameForm(forms.Form):
    your_surname = forms.CharField(label="Your Surname", max_length=100)

def test_modal1(request):
    if request.method == "POST":
        form = NameForm(request.POST)
        if form.is_valid():
            print(form.cleaned_data['your_name'])
            return HttpResponse(
                status=204,
                headers={"HX-Trigger": "modalClose"}
            )
    else:
        form = NameForm()

    return render(request, "partials/test_modal_form1.html", {"form": form})


def test_modal2(request):
    if request.method == "POST":
        form = SurnameForm(request.POST)
        if form.is_valid():
            print(form.cleaned_data['your_surname'])
            return HttpResponse(
                status=204,
                headers={"HX-Trigger": "modalClose"}
            )
    else:
        form = SurnameForm()

    return render(request, "partials/test_modal_form2.html", {"form": form})
```

### TEMPLATE

Основной шаблон с кнопками вызова модалок. Как видим, кнопки полностью шаблонные, отличается
только url:

```html
{% block content %}
    <button
            class="btn btn-primary"
            hx-get="{% url 'orders:test_modal1' %}"
            hx-target="#modal-content"
            hx-trigger="click"
            hx-swap="innerHTML"
            hx-on:htmx:before-request="
                document.getElementById('modal-backdrop').style.display='block';
                document.getElementById('modal-backdrop').classList.add('show')
            "
    >
        Открыть форму1
    </button>


    <button
            class="btn btn-primary"
            hx-get="{% url 'orders:test_modal2' %}"
            hx-target="#modal-content"
            hx-trigger="click"
            hx-swap="innerHTML"
            hx-on:htmx:before-request="
                document.getElementById('modal-backdrop').style.display='block';
                document.getElementById('modal-backdrop').classList.add('show')"
    >
        Открыть форму2
    </button>

{% endblock %}
```

### PARTIAL TEMPLATE

Содерживое partial html, которое возвращает view. Приможу для одной части:

```html
<div class="modal-header">
    <h5 class="modal-title">Редактирование</h5>
    <button
            type="button"
            class="btn-close"
            hx-on:click="closeModal()">
    </button>
</div>

<div class="modal-body">
    <form
            hx-post="{% url 'orders:test_modal1' %}"
            hx-target="#modal-content"
            hx-swap="innerHTML"
    >
        {% csrf_token %}
        {{ form.as_p }}

        <div class="mt-3 text-end">
            <button type="submit" class="btn btn-primary">
                Сохранить
            </button>
        </div>
    </form>
</div>
```

### BASE TEMPLATE

После основного блока

```html
<div class="container-fluid " style="width: initial">
    {% block body %}
    {% endblock %}
</div>
```

Мы вставляем следующие фрагменты. Первое - это собственно контейнер для отображения модалок:
```html
<!-- modal container (в base.html) -->
<div
        id="modal"
        class="modal fade"
        tabindex="-1"
        style="display: none;"
        hx-on:htmx:after-swap="this.classList.add('show'); this.style.display='block'"
>
    <div class="modal-dialog">
        <div class="modal-content" id="modal-content">
            <!-- htmx будет подставлять сюда HTML -->
        </div>
    </div>
</div>
```

Второе - это div, затемняющий фон (backdrop):

```html
<!-- backdrop -->
<div
        id="modal-backdrop"
        class="modal-backdrop fade"
        style="display:none;">
</div>
```

И третье - это хук, который правильно закрывает окно.

```js
<script>
    function closeModal() {
        const modal = document.getElementById('modal');
        const backdrop = document.getElementById('modal-backdrop');

        modal.classList.remove('show');
        modal.style.display = 'none';

        backdrop.classList.remove('show');
        backdrop.style.display = 'none';

        document.body.classList.remove('modal-open');
    }

    document.body.addEventListener("modalClose", closeModal);
</script>
```
