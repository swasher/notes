### [Simple Icons](https://simpleicons.org/)

Логитипы различных сервисов, компаний, технологий, продуктов.

### [Icones](https://icones.js.org/)

Самая большая и полная коллекция икон.

Использование в Svelte.

- Берем готовый компонент с icones, сохраняем в `sample.svelte`
- Импортируем: `import Sample from "$lib/components/icons/sample.svelte"`
- Вставляем в верстку: `<Sample />`
- Изменяем размер, 40 пикселей: `<AlarmClock width="40" height="40"/> `
- Изменяем размер - в два раза больше текста: `<Sample width="2em" height="2em" />`
- Цвет: по умолчанию иконка будет с цветом родителя:
  ```
  <div style="color: blue;">
    <AlarmClock /> <!-- иконка будет синяя -->
  </div>
  ```
  цвет управляется атрибутом `fill="currentColor"`. Или можно задать цвет явно: `<AlarmClock fill="green"/>`
- Фон: фона у иконки нет (она "прозрачная"), но если сильно приспичило, то можно обернуть:
  ```
  <div style="background: yellow; display: inline-block; padding: 0.5em;">
  <Sample width="2em" height="2em" color="red" /> <!-- красная иконка на желтом фоне -->
  </div>
  ```

##### Передача пропсов в Svelte
У тебя уже есть {...$$props} — это значит, что все атрибуты, которые ты передаёшь компоненту, автоматически попадут в `<svg>`.
Примеры:

    <AlarmClock width="50" height="50" fill="purple" class="my-icon"/>

Можно использовать CSS для всех иконок:
```
<style>
  .my-icon {
    transition: transform 0.3s;
  }
  .my-icon:hover {
    transform: scale(1.2);
  }
</style>
```
