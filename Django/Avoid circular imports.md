Например, нам нужно создать файл с какими-то функциям-утилитами, которые используются в джанговском views.py. 
В этом файле мы хотим аннотировать параметры функций, которые являются объектами Django:

    from .models import Detail
    
    def adapter(details: list[Detail]) -> str:
        ...

в таком случае мы получим ошибку Circular import.

Чтобы избежать этого, применяем следующую технику

    from __future__ import annotations
    from typing import TYPE_CHECKING
    
    if TYPE_CHECKING:
        from .models import Detail

    def adapter(details: list[Detail]) -> str:
        ...

#### Пояснение.

Использование forward references (в Django 4.0+)

Начиная с Python 3.7 и Django 4.0, Python поддерживает from __future__ import annotations, что позволяет указывать аннотации типов без немедленного импорта. Это часть механизма отложенных аннотаций (PEP 563). Начиная с Python 3.10, отложенные аннотации стали поведением по умолчанию. Использование from __future__ import annotations помогает подготовить код к будущим версиям Python.

    from __future__ import annotations
    
    def my_view(request: HttpRequest) -> MyModel:
        # логика функции
        pass

Этот подход позволяет Django и Python корректно обрабатывать аннотации типов даже без импорта на этапе выполнения, что предотвращает циклические зависимости. Это достигается за счет того, что аннотации типов будут обрабатываться как строки (то есть они не будут вычисляться во время выполнения).

Однако, в таком случае мы теряем возможность поддержки типов со стороны IDE, так как у нас нет прямого импорта класса и IDE не 
видит типа параметра (HttpRequest).

Для этого мы применяем `TYPE_CHECKING`.

    from typing import TYPE_CHECKING
    
    if TYPE_CHECKING:
        from myapp.models import MyModel  # только для аннотаций типов
    
    def my_view(request: HttpRequest) -> "MyModel":
        # логика функции
        pass

Модуль `typing` предоставляет константу `TYPE_CHECKING`, которая равна True только во время проверки типов. 
Это позволяет импортировать необходимые модули только на этапе проверки типов.
