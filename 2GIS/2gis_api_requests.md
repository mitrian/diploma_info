# Запросы к API 2ГИС для поиска мест, где поесть

Документация по использованию Places API 2ГИС для поиска ресторанов, кафе и тд

## Базовые параметры

- **Endpoint**: `https://catalog.api.2gis.com/3.0/items`
- **Метод**: `GET`
- **Формат ответа**: JSON

### Обязательные параметры

- `key` - ключ API (лимит на 1000 запросов в месяц, буду менять аккаунт по надобности)
- `q` - текстовый запрос для поиска (Cюда вписывается практически что угодно (рестораны/рестораны такой-то кухни))

### Параметры геоограничения

- `point` - координаты точки (например: `30.307834,59.957499`)
- `radius` - радиус поиска в метрах (например: `500`)

### Дополнительные параметры

- `type` - видимо, тип объекта (у ресторанов зачастую указывается `branch`)
- `sort` - сортировка результатов
- `page_size` - количество результатов на странице (ограничение в 10)
- `page` - номер страницы
- `fields` - дополнительные поля для получения в ответе (подробная инфа на сайте, планирую использовать: `items.rubrics,items.reviews` те вытянить рейтинг заведения и отзывы)

---

## 1. Поиск мест, где поесть в заданном радиусе от координаты

### Описание

Поиск всех заведений питания в заданном радиусе от указанной точки.

### Пример запроса

```bash
curl -G "https://catalog.api.2gis.com/3.0/items" \
  --data-urlencode "q=ресторан" \
  --data-urlencode "type=branch" \
  --data-urlencode "point=30.307834,59.957499" \
  --data-urlencode "radius=500" \
  --data-urlencode "sort=distance" \
  --data-urlencode "page_size=2" \
  --data-urlencode "key=API_KEY"
```

### Альтернативные варианты запроса

Можно сделать инчае:

```bash
# Поиск кафе
curl -G "https://catalog.api.2gis.com/3.0/items" \
  --data-urlencode "q=кафе" \
  --data-urlencode "point=30.307834,59.957499" \
  --data-urlencode "radius=500" \
  --data-urlencode "key=API_KEY"

# Поиск всех мест, где можно поесть
curl -G "https://catalog.api.2gis.com/3.0/items" \
  --data-urlencode "q=где поесть" \
  --data-urlencode "point=30.307834,59.957499" \
  --data-urlencode "radius=500" \
  --data-urlencode "key=API_KEY"
```

### Пример ответа

Здесь из полезной информации уже можно получить: название, адресс

```json
{
    "meta": {
        "api_version": "3.0.19643",
        "code": 200,
        "issue_date": "20260127"
    },
    "result": {
        "items": [
            {
                "address_name": "Сытнинская улица, 14",
                "id": "70000001019030190",
                "name": "RoseMary, рыбный ресторан",
                "type": "branch"
            },
            {
                "address_name": "улица Маркина, 1",
                "id": "70000001017300545",
                "name": "Ясли, бар",
                "type": "branch"
            }
        ],
        "total": 29
    }
}
```

### Получение дополнительной информации

Для получения дополнительных данных о заведениях используется параметр `fields`:

```bash
curl -G "https://catalog.api.2gis.com/3.0/items" \
  --data-urlencode "q=ресторан" \
  --data-urlencode "point=30.307834,59.957499" \
  --data-urlencode "radius=500" \
  --data-urlencode "fields=items.rubrics,items.reviews,items.point" \
  --data-urlencode "page_size=2" \
  --data-urlencode "key=API_KEY"
```

### Пример ответа

Тут уже видно какие-то категории, рейтинг и кол-во отзывов, что может повлиять на доверие пользователя к заведению

```json
{
    "meta": {
        "api_version": "3.0.19643",
        "code": 200,
        "issue_date": "20260127"
    },
    "result": {
        "items": [
            {
                "address_comment": "1 этаж",
                "address_name": "Большой П.С. проспект, 35а",
                "id": "70000001064680309",
                "name": "Gorilla, ресторан",
                "point": {
                    "lat": 59.959706,
                    "lon": 30.302436
                },
                "reviews": {
                    "general_rating": 4.9,
                    "general_review_count": 1046,
                    "general_review_count_with_stars": 11674,
                    "is_reviewable": true,
                    "is_reviewable_on_flamp": true,
                    "items": [
                        {
                            "is_reviewable": true,
                            "tag": "2gis_reviews"
                        },
                        {
                            "is_reviewable": true,
                            "tag": "flamp"
                        }
                    ],
                    "org_rating": 4.9,
                    "org_review_count": 1046,
                    "org_review_count_with_stars": 11674
                },
                "rubrics": [
                    {
                        "alias": "restorany",
                        "id": "164",
                        "kind": "primary",
                        "name": "Рестораны",
                        "parent_id": "2",
                        "short_id": 164
                    },
                    {
                        "alias": "sushi_bary",
                        "id": "15791",
                        "kind": "additional",
                        "name": "Суши-бары",
                        "parent_id": "2",
                        "short_id": 15791
                    }
                ],
                "type": "branch"
            },
            {
                "address_comment": "1 этаж",
                "address_name": "улица Ленина, 18/49",
                "id": "70000001076113885",
                "name": "Nomad, ресторан",
                "point": {
                    "lat": 59.961508,
                    "lon": 30.305995
                },
                "reviews": {
                    "general_rating": 4.9,
                    "general_review_count": 74,
                    "general_review_count_with_stars": 140,
                    "is_reviewable": true,
                    "is_reviewable_on_flamp": true,
                    "items": [
                        {
                            "is_reviewable": true,
                            "tag": "2gis_reviews"
                        },
                        {
                            "is_reviewable": true,
                            "tag": "flamp"
                        }
                    ],
                    "org_rating": 4.9,
                    "org_review_count": 74,
                    "org_review_count_with_stars": 140
                },
                "rubrics": [
                    {
                        "alias": "restorany",
                        "id": "164",
                        "kind": "primary",
                        "name": "Рестораны",
                        "parent_id": "2",
                        "short_id": 164
                    },
                    {
                        "alias": "bary",
                        "id": "159",
                        "kind": "additional",
                        "name": "Бары",
                        "parent_id": "2",
                        "short_id": 159
                    },
                    {
                        "alias": "dostavka_edy",
                        "id": "1203",
                        "kind": "additional",
                        "name": "Доставка еды",
                        "parent_id": "2",
                        "short_id": 1203
                    }
                ],
                "type": "branch"
            }
        ],
        "total": 29
    }
}
```
---

## 2. Поиск мест, где поесть с заданной категорией кухни в заданном радиусе

### Описание

Поиск заведений с определенным типом кухни (японская, итальянская, грузинская и т.д.) в заданном радиусе от указанной точки.

В данном случае в поиске указываем название кухни. В зависимости от наличия/отсутствия заведения в ответе на подобный запрос можно присваивать/убирать у заведения этот тип непосредственно внутри БД 

### Пример запроса

```bash
curl -G "https://catalog.api.2gis.com/3.0/items" \
  --data-urlencode "q=японская кухня" \
  --data-urlencode "point=30.307834,59.957499" \
  --data-urlencode "radius=500" \
  --data-urlencode "fields=items.name,items.rubrics.name" \
  --data-urlencode "key=API_KEY"
```

### Примеры запросов для разных типов кухни

```bash
# Итальянская кухня
curl -G "https://catalog.api.2gis.com/3.0/items" \
  --data-urlencode "q=итальянская кухня" \
  --data-urlencode "point=30.307834,59.957499" \
  --data-urlencode "radius=500" \
  --data-urlencode "key=API_KEY"

# Грузинская кухня
curl -G "https://catalog.api.2gis.com/3.0/items" \
  --data-urlencode "q=грузинская кухня" \
  --data-urlencode "point=30.307834,59.957499" \
  --data-urlencode "radius=500" \
  --data-urlencode "key=API_KEY"

# Французская кухня
curl -G "https://catalog.api.2gis.com/3.0/items" \
  --data-urlencode "q=французская кухня" \
  --data-urlencode "point=30.307834,59.957499" \
  --data-urlencode "radius=500" \
  --data-urlencode "key=API_KEY"

# Китайская кухня
curl -G "https://catalog.api.2gis.com/3.0/items" \
  --data-urlencode "q=китайская кухня" \
  --data-urlencode "point=30.307834,59.957499" \
  --data-urlencode "radius=500" \
  --data-urlencode "key=API_KEY"
```

### Пример ответа

```json
{
    "meta": {
        "api_version": "3.0.19643",
        "code": 200,
        "issue_date": "20260127"
    },
    "result": {
        "items": [
            {
                "address_comment": "1 этаж",
                "address_name": "Большой П.С. проспект, 35а",
                "id": "70000001064680309",
                "name": "Gorilla, ресторан",
                "type": "branch"
            },
            {
                "address_name": "улица Лизы Чайкиной, 19 лит Б",
                "id": "70000001023690556",
                "name": "Такояки-Мисэ, стритфуд-кафе японской кухни",
                "type": "branch"
            }
        ],
        "total": 7
    }
}
```

### Альтернативный способ поиска по категории кухни

Можно использовать более конкретные запросы:

```bash
# Рестораны с итальянской кухней
curl -G "https://catalog.api.2gis.com/3.0/items" \
  --data-urlencode "q=рестораны с итальянской кухней" \
  --data-urlencode "point=30.307834,59.957499" \
  --data-urlencode "radius=500" \
  --data-urlencode "key=API_KEY"
```

---

## 3. Получение всех rubric_id, связанных с видами кухни

### Описание

Еще один вариант для поиска заведений с определенным типом кухни можно использовать `rubric_id` (идентификатор категорий).

### Способ 1: Поиск категорий по ключевым словам

Используется Categories API от 2GIS

#### Шаг 1: Получение region_id

Сначала нужно узнать идентификатор региона.`region_id`:
- Москва: `1`
- Санкт-Петербург: `2`
- Полный список можно получить через Regions API

#### Шаг 2: Поиск категорий кухни

```bash
curl -G "https://catalog.api.2gis.com/2.0/catalog/rubric/search" \
  --data-urlencode "q=японская кухня" \
  --data-urlencode "region_id=2" \
  --data-urlencode "key=API_KEY"
```

#### Пример ответа

```json
{
    "meta": {
        "code": 200,
        "api_version": "2.0",
        "issue_date": "20260127"
    },
    "result": {
        "items": [
            {
                "id": "15791",
                "name": "Суши-бары",
                "alias": "sushi_bary",
                "type": "rubric"
            },
            {
                "id": "162",
                "name": "Японская кухня",
                "alias": "yaponskaya_kukhnya",
                "type": "rubric"
            }
        ],
        "total": 2
    }
}
```

### Способ 2: Получение списка всех категорий и фильтрация

Можно получить полный список категорий в регионе и найти нужные:


```bash
curl -G "https://catalog.api.2gis.com/2.0/catalog/rubric/list" \
  --data-urlencode "region_id=2" \
  --data-urlencode "parent_id=164" \
  --data-urlencode "fields=items.rubrics" \
  --data-urlencode "page_size=10000" \
  --data-urlencode "key=API_KEY"
```

### Использование rubric_id в Places API

Потом, зная rubric_id всех видов кухни, можно будет сделать по ним филтрацию при поиске мест, соответсвенно, учесть это при наполнении БД (присвоении категорий)

- `164` - рестораны
- `159` - бары 
- и тд (подробные категории можно узнать при помощи указания `parent_id`, в примере выше - дочерние категории по отношению к ресторанам)

```bash
curl -G "https://catalog.api.2gis.com/3.0/items" \
  --data-urlencode "rubric_id=164" \
  --data-urlencode "point=30.307834,59.957499" \
  --data-urlencode "radius=500" \
  --data-urlencode "key=API_KEY"
```

Также можно указать несколько `rubric_id` через запятую:

```bash
curl -G "https://catalog.api.2gis.com/3.0/items" \
  --data-urlencode "rubric_id=162,159" \
  --data-urlencode "point=30.307834,59.957499" \
  --data-urlencode "radius=500" \
  --data-urlencode "key=API_KEY"
```

Еще можно комбинировать `rubric_id` с текстовым запросом для более точного поиска

```bash
curl -G "https://catalog.api.2gis.com/3.0/items" \
  --data-urlencode "q=ресторан" \
  --data-urlencode "rubric_id=162" \
  --data-urlencode "point=30.307834,59.957499" \
  --data-urlencode "radius=500" \
  --data-urlencode "key=API_KEY"
```



## Ссылки

- [Документация Places API 2ГИС](https://docs.2gis.com/api/search/places/overview)
- [Справочник API Places](https://docs.2gis.com/api/search/places/reference)
- [Документация Categories API](https://docs.2gis.com/api/search/categories/overview)
- [Поиск категорий по ключевым словам](https://docs.2gis.com/api/search/categories/reference/2.0/catalog/rubric/search)
- [Список всех категорий](https://docs.2gis.com/api/search/categories/reference/2.0/catalog/rubric/list)
- [Примеры использования Categories API](https://docs.2gis.com/api/search/categories/examples)
- [Личный кабинет 2ГИС](https://cabinet.2gis.com/)
