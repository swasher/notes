# Как в методе модели ‘save' сравнить старые и новые значения

Owner: Alexey Swasher
Last edited time: November 28, 2023 6:26 PM

### Источник на [Stackoverflow](https://stackoverflow.com/a/64116052/1334825)

```python
class Detail(models.Model):
    printsheet = models.ForeignKey(PrintSheet, blank=True, null=True, 
				on_delete=models.SET_NULL, related_name='details')

    @classmethod
    def from_db(cls, db, field_names, values):
        """
        Helper function. 
				In 'save' method we have _loaded_values attribute, 
				which contain 'old' instance values.
        """
        instance = super().from_db(db, field_names, values)

        # save original values, when model is loaded from database,
        # in a separate attribute on the model
        instance._loaded_values = dict(zip(field_names, values))

        return instance

    def save(self, *args, **kwargs):

        super(Detail, self).save(*args, **kwargs)

        if self._loaded_values['printsheet_id'] != self.printsheet_id:
            """
            Запускаем обновление цветов Листа, только если у Детали изменился связанный Лист.
            Если у Детали поменялся связанный лист с L1 на L2, мы должны обновить оба листа.
            Кроме того, деталь могла быть не связана с листом, или перестать быть связанной с листом, поэтому проверяем на None.
            """
            print('Update color from Detail->Save')
            if self.printsheet:
                self.printsheet.update_colors()
            if self._loaded_values['printsheet_id']:
                old_sheet = PrintSheet.objects.get(id=self._loaded_values['printsheet_id'])
                old_sheet.update_colors()

```