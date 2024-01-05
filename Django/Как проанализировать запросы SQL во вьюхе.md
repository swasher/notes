Вы можете использовать reset_queries()для очистки connection.queries.

Например, если поставить reset_queries() после Person.objects.select_for_update(), как показано ниже:

```python
# "store/views.py"

from django.db import transaction
from .models import Person
from django.db import reset_queries
from django.db import connection
from django.http import HttpResponse

@transaction.atomic
def test(request):
    Person.objects.create(name="John") # INSERT
   
    qs = Person.objects.select_for_update().get(name="John") # SELECT FOR UPDATE

    reset_queries() # RESET
    qs.name = "Tom"
    qs.save() # UPDATE
    qs.delete() # DELETE
                 
    for query in connection.queries: # Here
        print(query)
```

Вы получаете только запросы UPDATEиDELETE без запросов INSERTиSELECT FOR UPDATE , как показано ниже:

```
{'sql': 'UPDATE "store_person" SET "name" = \'Tom\' WHERE "store_person"."id" = 190', 'time': '0.000'}
{'sql': 'DELETE FROM "store_person" WHERE "store_person"."id" IN (190)', 'time': '0.000'}
[24/Dec/2022 07:00:01] "GET /store/test/ HTTP/1.1" 200 9
```
