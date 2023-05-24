В общих чертах - код довольно чистый, ошибки проявляются в использовании новых инструментов
либо не знаете каких-то общепринятых вещей, но это все не критично,
оценил бы работу на 6 баллов, если закроете issues, что я описал ниже

## `junior_developers.settings`
1. Если используешь переменные окружения для django проекта
можно также выносить значение переменной DEBUG,
поставить по умолчанию, например, True, а на production будет False

## `vacancies.views`
1. вместо try-except с raise Http404 можно использовать `django.shortcuts.get_object_or_404`,
таким образом не пишем то, что уже есть, чтобы не дублировать код.
2. если используете try-except лучше указывать конкретные исключения,
в данном случае `DoesNotExists`, но благодаря `get_object_or_404` тут не придется писать try-except
3. для сбора вакансий с фильтром по компании или специальности можно использовать полученную специальность
пример
```python
from vacancies.models import Company

company = Company.objects.get(id=1)
company.vacancies.all()
company.vacancies.filter(salary=...)
```
аттрибут `vacancies` внутри `company` выступает как `RelatedManager` модели Vacancy с фильтром по компании `company`
4. `view` в названии view-функциях лучше писать в единственном числе, это касается только данных view-функций,
т.к. там только одно возможное представление на одну view-функцию.
таким образом jobs_views надо переименовать в jobs_view. Бывает когда у вас несколько представлений в функции или классе,
тогда это принято называть view set (`job_viewset` / `JobViewSet`), но тогда название сущности в единственном числе.
это не является ошибкой, просто так принято в обществе разработчиков.

## `vacancies.models`
1. id поле уже есть по умолчанию, в задании не сказано его изменять:
```python
from django.db import models

# по умолчанию
models.AutoField(auto_created=True, primary_key=True, verbose_name='ID')
# ваша версия
models.IntegerField(primary_key=True)
```
Подробнее почитать про [AutoField](https://docs.djangoproject.com/en/4.1/ref/models/fields/#django.db.models.AutoField)
Суть в том, что это IntegerField который автоматически увеличивается.

2. проверьте название полей у моделей, сравните с тем, что просят в задании.(предоставленные мок-данные jobs хранят поле
posted, что некорректно, либо в задании надо менять название, либо в мок-данных)

## `vacancies.management.commands.data_to_sql`
1. Тут можно обойтись обычной распаковкой python, а именно через **kwargs
```python
for company in companies:
    new_item = Company(**company)
    ...
```

2. советую воспользоваться bulk_create, для сокращения количество запросов в базу данных.
bulk_create создаст сразу все объекты за 1 запрос
```python
from vacancies.data import companies
from vacancies.models import Company

# Пример - компании
new_companies = [Company(**company) for company in companies]
Company.objects.bulk_create(new_companies)

# или еще более короткий вариант кода
Company.objects.bulk_create(
    [Company(**company) for company in companies]
)
```
