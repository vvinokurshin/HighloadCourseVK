# Курсовая работа
> [Задание](https://github.com/init/highload/blob/main/homework_architecture.md)

## 1. Тема и целевая аудитория
Amazon — американская компания, крупнейшая в мире на рынках платформ электронной коммерции и публично-облачных вычислений по выручке и рыночной капитализации.

### MVP
 - Регистрация пользователей (покупателей и продавцов), управление аккаунтом
 - Поиск и просмотр товаров в каталоге
 - Добавление товаров в корзину
 - Оформление заказа
 - Отзывы и рейтинти товаров
 - Создание товаров (для продавцов)
 - Управление заказами, осуществление их отправки, доставки, возвратов
 - Управление складом 

### Целевая аудитория [[1]](https://www.similarweb.com/ru/website/amazon.com/#traffic)
 - Общее количество визитов в месяц: 2.6 млрд (с настольных компьютеров и мобильных устройств)
 - Число уникальных пользователей в месяц (MAU) в США - 230 млн [[2]](https://www.statista.com/statistics/271412/most-visited-us-web-properties-based-on-number-of-visitors/)
     > Как я не искал, по всему миру этой статистики не нашел
 
**Распределение по странам:**
 1. США - 82.95%
 2. Канада - 1.09%
 3. Индия - 1.06%
 4. Великобритания - 0.97%
 5. Япония - 0.84%
 6. Другие страны - 13.10%

**Демография аудитории:**
 - Распределение по полу: 45.99% женщины и 54.01% мужчины
 - Самая многочисленная возрастная группа — 25-34 лет (29.18%)
    - 35-44: 19.24%
    - 18-24: 17.29%
    - 45-54: 15.21%
    - 55-64: 11.79%
    - 65+: 7.28%

## 2. Расчет нагрузки

### Продуктовые метрики
 - Месячная аудитория - 213 млн [[2]](https://www.statista.com/statistics/271412/most-visited-us-web-properties-based-on-number-of-visitors/)
 - Дневная аудитория - 28 млн [[3]](https://vc.ru/trade/594954-top-zarubezhnyh-marketpleysov-dlya-prodazhi-tovarov-v-2023-amazon-etsy-i-drugie)
 - Средний размер хранилища пользователя: 
    > В хранилище пользователя входяд его заказы, личные и платежные данные, адреса и тп.
 - Среднее количество действий пользователя по типам в день:
    - Amazon имеет 9.5 млн продавцов (из них более 2.5 млн активных); каждый день к Amazon присоединяется более 2,000 новых продавцов [[4]](https://www.affiliatebay.net/ru/amazon-statistics/)
    - 66% пользователей сервиса для поиска товаров предпочитают Amazon, а не глобальный поиск [[5]](https://www.statista.com/statistics/235681/online-shoppers-digital-channels-of-choice-to-learn-about-products/)
    - ~40% пользователей совершают покупку 1-2 раза в месяц [[1]](https://www.similarweb.com/ru/website/amazon.com/#traffic)
    - Количество отправленных в день посылок: 1.6 млн [[6]](https://market.us/statistics/e-commerce-websites/amazon/)
    - 2-5% пользователей оставляют ревью после покупки товара [[7]](https://www.quora.com/What-percentage-of-buyers-write-reviews-on-Amazon)
    - Только Amazon продает более 12 миллионов товаров. Если принять во внимание сторонних продавцов, на Amazon Marketplace продается более 353 миллионов товаров. [[8]](https://0ca36445185fb449d582-f6ffa6baf5dd4144ff990b4132ba0c4d.ssl.cf1.rackcdn.com/IG_360piAmazon_9.13.16.pdf)

    **Итого:**

    | Действие        | Количество в день (на 1 пользователя) |
    | ------------- |:-------------:|
    | Регистрация  покупателей | 0.0008 |
    | Поиск      | 0.66      |
    | Добавление в корзину | 0.875  |
    | Оформление | 0.04      |
    | Добавление отзывов | 0.02  |
    | Создание товаров | 0.11     |
    | Отправка заказов | 0.05      |

### Технические метрики



## Источники
 1. https://www.similarweb.com/ru/website/amazon.com/#traffic
 2. https://www.statista.com/statistics/271412/most-visited-us-web-properties-based-on-number-of-visitors/
 3. https://vc.ru/trade/594954-top-zarubezhnyh-marketpleysov-dlya-prodazhi-tovarov-v-2023-amazon-etsy-i-drugie
 4. https://www.affiliatebay.net/ru/amazon-statistics/
 5. https://www.statista.com/statistics/235681/online-shoppers-digital-channels-of-choice-to-learn-about-products/
 6. https://market.us/statistics/e-commerce-websites/amazon/
 7. https://www.quora.com/What-percentage-of-buyers-write-reviews-on-Amazon
 8. https://0ca36445185fb449d582-f6ffa6baf5dd4144ff990b4132ba0c4d.ssl.cf1.rackcdn.com/IG_360piAmazon_9.13.16.pdf