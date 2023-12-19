Если мы подключаем файл, то нужно указать `defer`:

```html
<script src="{% static 'js/myjs.js' %}" defer></script>
```

Если скрипт в теле `HTML` то используем событие `DOMContentLoaded`:

```js
<script>
    document.addEventListener("DOMContentLoaded", function (event) {
        new TomSelect('#id-customer', {
            maxItems: 3
        });
    });
</script>
```
