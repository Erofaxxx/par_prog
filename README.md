# Экспорт данных из Яндекс Метрики

Программа для выгрузки данных из Яндекс Метрики через Logs API.

## Установка

```bash
pip install -r requirements.txt
```

## Быстрый старт

1. Создайте конфигурационный файл:

```bash
cp config.example.json config.json
```

2. Откройте `config.json` и укажите:
   - **counter_id** - ID вашего счетчика Яндекс Метрики
   - **date_from** / **date_to** - Период выгрузки данных (YYYY-MM-DD)

Пример `config.json`:
```json
{
  "token": "y0__xDf3anVBxj1oj0g2YDzmxbqmKoUgfZgE6CsOwrateq5Fbogyg",
  "counter_id": "12345678",
  "date_from": "2025-02-06",
  "date_to": "2025-02-13"
}
```

3. Запустите экспорт:

```bash
python run_export.py
```

## Альтернативный способ

Можно использовать основной скрипт напрямую, отредактировав настройки в файле:

```bash
python yandex_metrika_export.py
```

## Экспорт больших периодов

Если нужно выгрузить данные за длительный период (месяц и больше), используйте скрипт для выгрузки по частям:

```bash
python run_export_chunks.py
```

Скрипт:
- Разобьет период на части по 7 дней
- Выгрузит каждую часть отдельно
- Автоматически объединит все части в один файл
- Обработает ошибки и покажет статистику

Программа:
1. Создаст запрос на формирование логов
2. Будет ожидать обработки запроса (обычно 5-15 минут)
3. Скачает данные и сохранит их в TSV файл
4. Очистит запрос на сервере

## Выходные данные

Данные сохраняются в файл формата TSV (Tab-Separated Values):
- Название файла: `metrika_visits_YYYY-MM-DD_YYYY-MM-DD.tsv`
- Можно открыть в Excel, Google Sheets или обработать в Python/Pandas

## Анализ данных

После получения данных можно воспользоваться скриптом для их анализа:

```bash
python analyze_data.py
```

Скрипт выведет:
- Общую статистику по данным
- Статистику по покупкам (выручка, средний чек)
- Топ-10 продуктов
- Статистику по звонкам
- Статистику по Яндекс Директ
- Экспортирует покупки в отдельный CSV файл

## Выгружаемые поля

### Покупки
- purchaseID, purchaseDateTime, purchaseRevenue
- purchaseTax, purchaseShipping, purchaseCoupon
- purchaseCurrency, purchaseProductQuantity

### Продукты
- productsPurchaseID, productsID, productsName
- productsCategory, productsPrice

### Яндекс Директ
- DirectClickOrder, DirectClickBanner
- DirectClickOrderName, ClickBannerGroupName
- DirectClickBannerName, DirectPhraseOrCond
- DirectPlatformType, DirectBannerGroup

### Показы продуктов
- impressionsURL, impressionsDateTime
- impressionsProductID, impressionsProductName
- impressionsProductBrand, impressionsProductCategory (1-5 уровни)
- impressionsProductVariant, impressionsProductPrice
- impressionsProductCurrency, impressionsProductCoupon

### Офлайн звонки
- offlineCallTalkDuration, offlineCallMissed
- offlineCallTag, offlineCallURL
- offlineCallFirstTimeCaller

### Дата и время
- dateTime, clientTimeZone, dateTimeUTC

## Ограничения API

- Максимальный период выгрузки: 1 год
- Максимум 10 одновременных запросов
- Данные доступны за последние 2 года
- Лимит: 100 запросов в день

## Устранение неполадок

### Ошибка 400 (Bad Request)
- Проверьте правильность COUNTER_ID
- Убедитесь, что все поля доступны для вашего счетчика

### Ошибка 401 (Unauthorized)
- Проверьте токен доступа
- Убедитесь, что токен имеет права на чтение данных счетчика

### Ошибка 403 (Forbidden)
- Проверьте права доступа к счетчику
- Токен должен быть привязан к аккаунту с доступом к счетчику

## Дополнительная информация

Документация API: https://yandex.ru/dev/metrika/doc/api2/logs/intro.html
