Как обслуживать media файлы нативно (без nginx)
====================================

### Устнановка зависимостей

    uv add whitenoise starlette aiofiles

### Настройка settings.py

```
# settings.py

import os
from pathlib import Path

BASE_DIR = Path(__file__).resolve().parent.parent

DEBUG = False  # важно: WhiteNoise работает только при DEBUG=False

STATIC_URL = '/static/'
STATIC_ROOT = BASE_DIR / 'staticfiles'

MEDIA_URL = '/media/'
MEDIA_ROOT = BASE_DIR / 'media'

INSTALLED_APPS = [
    ...
    'django.contrib.staticfiles',
    ...
]

MIDDLEWARE = [
    'whitenoise.middleware.WhiteNoiseMiddleware',
    ...
]

# включаем сжатие и immutable-хеш
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
```

Собираем статику

    python manage.py collectstatic

### Конфигурация asgi.py


```
import os
from django.core.asgi import get_asgi_application
from starlette.staticfiles import StaticFiles
from starlette.routing import Mount
from starlette.applications import Starlette

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'myproject.settings')

django_asgi_app = get_asgi_application()

app = Starlette(
    routes=[
        Mount("/media", app=StaticFiles(directory="media"), name="media"),
        Mount("/", app=django_asgi_app),
    ]
)
```

Второй пример, в котором есть вебсокет:

```
# Django-приложение
django_asgi_app = get_asgi_application()

# HTTP-приложение = media + static + Django
http_app = Starlette(
    routes=[
        Mount("/media", app=StaticFiles(directory="media"), name="media"),
        Mount("/", app=django_asgi_app),
    ]
)

# Финальный ASGI-роутер
application = ProtocolTypeRouter({
    "http": http_app,
    "websocket": AuthMiddlewareStack(
        URLRouter(websocket_urlpatterns)
    ),
})
```

В этих примерах Whitenoise внедряется через middleware, то есть он уже есть в сервере, 
получаемом в `get_asgi_application()`.

В принципе, можно вообще отказаться от Whitenoise, и обслуживать и статику, и медиа через Starlette. Но как я понял, Whitenoise
более заточен на неизменяемые файлы, а Starlett - на изменяемые, разница там в стратегиях кеширования.

### Запуск

    daphne myproject.asgi:app

### Проверка

| URL                               | Поведение                    |
| --------------------------------- | ---------------------------- |
| `http://localhost/media/test.jpg` | отдаётся через `StaticFiles` |
| `http://localhost/static/...`     | отдаётся через `WhiteNoise`  |
| `http://localhost/admin/`         | обычное Django-приложение    |
