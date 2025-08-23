### Dynamic attributes 

Если в атрибутах есть одноименные имя и значения, типа

    <img src={src} alt="{name} dancing" />

то можно сократить до 

    <img {src} alt="{name} dancing" />

### Snapshot

Иногда нужно иметь "нереактивную" копию реактивного объекта. Например, затем, что мы не можем печатать реактивыные объекты в консоль, пытаясь
отследить их состояние. Тогда нужно использовть snapshot:

```svelte
<script>
let numbers = $state([1, 2, 3, 4]);

function addNumber() {
    numbers.push(numbers.length + 1);
    console.log($state.snapshot(numbers));
}
</script>
```

state - это Руна. Можно использовать другую руну - $inspect(numbers);

Можно просто написать $inspect в скрипте, и реактивная переменная будет выводиться при каждом изменении
