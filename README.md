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
 - Средний размер хранилища пользователя: ~1Мб
    > В хранилище пользователя входяд его заказы, личные и платежные данные, адреса и тп.
 - Среднее количество действий пользователя по типам в день:
    - Amazon имеет 9.5 млн продавцов (из них более 2.5 млн активных); каждый день к Amazon присоединяется более 2,000 новых продавцов [[4]](https://www.affiliatebay.net/ru/amazon-statistics/). Предположим, что продавцов на площадке - 10%, тогда в день новых пользователей - 200000
    - 66% пользователей сервиса для поиска товаров предпочитают Amazon, а не глобальный поиск [[5]](https://www.statista.com/statistics/235681/online-shoppers-digital-channels-of-choice-to-learn-about-products/). предположим, что поиск используется в 1 из 10 случаев.
    - Предположим, что покупатель в среднем открывает 1-2 товара, который отобразился в каталоге/поиске
    - Предположим, что в корзине в среднем 2 товара, а покупатель покупает каждый второй товар из корзины 
    - Количество отправленных в день посылок: 1.6 млн [[6]](https://market.us/statistics/e-commerce-websites/amazon/)
    - 2-5% пользователей оставляют ревью после покупки товара [[7]](https://www.quora.com/What-percentage-of-buyers-write-reviews-on-Amazon)
    - Только Amazon продает более 12 миллионов товаров. Если принять во внимание сторонних продавцов, на Amazon Marketplace продается более 353 миллионов товаров. [[8]](https://0ca36445185fb449d582-f6ffa6baf5dd4144ff990b4132ba0c4d.ssl.cf1.rackcdn.com/IG_360piAmazon_9.13.16.pdf)

    **Итого:**

    | Действие        | Формула | Количество в день (на 1 пользователя) |
    | ------------- |:-------------:|:-------------:|
    | Регистрация | 200000 (регистраций в день) / 28 млн (DAU) | 0.0007 |
    | Поиск      | 0.66 (часть пользователей, который используют поиск) * 0.1 (часть, которая приходится на поиск) * 28 млн (DAU) / 28 млн (DAU) | 0.066      |
    | Просмотр каталога     |  0.9 (часть, которая приходится на просмотр каталога) * 28 млн (DAU) / 28 млн (DAU) | 0.9      |
    | Просмотр товара      |  (0.9 + 0.66 * 0.1) (пользователи, которые пришли из каталога и поиска) * 28 млн (DAU) * 1.5 (1-2 товар открывает) / 28 млн (DAU) | 1.45      |
    | Добавление в корзину | 1.45 (просмотр товара) * 2 (2 товара в корзине в среднем) | 2.9  |
    | Оформление заказов | 2.9 (добавление в корзину) * 0.5 (каждый второй товар покупается) | 1.45 |
    | Добавление отзыва | 1.45 (оформление заказов) * 0.035 (2-5% пользователей оставляют ревью) | 0.05  |
    | Создание товаров | 1.6 млн (количество посылок в день) * 2 (2 товара в корзине) / 28 млн (DAU) | 0.1     |
    | Отправка заказов | 1.6 млн (количество посылок в день) / 28 млн (DAU) | 0.05      |

### Технические метрики

#### RPS

Чтобы подсчитать средний RPS нужно данные, полученные в продуктовых метриках умножить на DAU (так как считали на 1 пользователя) и поделить на количество секунд в дне (так как считали количество действий в день).

Предположим, что в пике нагрузка вырастает в 2 раза.

**Итого**

| Действие        | Средний RPS | Пиковый RPS |
| ------------- |:-------------:|:-------------:|
| Регистрация      | 5   | 10 |
| Поиск | 513  | 1026 |
| Просмотр каталога | 7000  | 14000 |
| Просмотр товара | 11278  | 22556 |
| Добавление в корзину  |  22556  | 45112 |
| Оформление заказов |  11278  | 22556 |
| Добавление отзыва |  389  | 778 |
| Создание товаров |  778  | 1556 |
| Отправка заказов | 389  | 778 |


#### Трафик

Посчитаем количество товаров на каждой из основых страниц:
> Выделим только основные, в которых гоняется много данных по сети (например, регистрацию, добавление отзывов и добавление в корзину учитывать не будем)

 1. Поиск: на странице находится 12 товаров + в ленте рекомендаций еще 15 товаров (27 предпоказов)
 2. Просмотр каталога: на странице находится 24 товара + в ленте рекомендаций еще 15 товаров (39 предпоказов)
 3. Просмотр товара: 1 товар + 20 товаров, которые предлагаются купить в комплекте + в ленте рекомендаций еще 15 товаров (1 полноценный товар и 35 предпоказов)
 4. Создание товара: 1 товар

На предпоказ товара требуется:
 - Краткое описание + рейтинг ~1Кб
 - Сжатая картинка ~300Кб

Информация о товаре весит ~15Мб:
   - 8 картинок, каждая по 500Кб= 4Мб
   - описание товара, отзывы ~ 1Мб
   - видео о товаре на минуту ~ 10 Мб

Получаем:

| Действие         | RPS | Трафик | 
| ------------- |:-------------:| :-------: |
| Поиск        | 513 | 513 * 27 * 301Кб ~ 3.98Гб|
| Просмотр каталога | 7000 | 7000 * 39 * 301Кб ~ 78.3Гб |
| Просмотр товара    | 11278 | 11278 * (35 * 301Кб + 1 * 15Мб) ~ 278.5Гб|
| Создание товара   | 778 | 778 * 1 * 15Мб ~ 11.4Гб|

**Итого** 
 - Трафик в секунду: 3.98Гб + 78.3Гб + 278.5Гб + 11.4Гб = **372.18 Гб/с** = **2977.44 Гбит/с**
 - Трафик в сутки: 372.18 Гб/с * 60 * 60 * 24 = **30.67 Пб/сут**

**Пиковый**

Если трафик вырастет в 2 раза, то:
 - Трафик в секунду: **5954.88 Гбит/с**
 - Трафик в сутки: **61.34 Пб/сут**

Подсчитаем, сколько дополнительной памяти понадобится хранилищу через год.

Основное действие по заполнению хранилища - добавление товара (15Мб на товар), кроме этого, учтем, что средний размер хранилища пользователя составляет 1Мб.

Тогда:
 - В день новых пользователей - 200000 = 200000Мб или 195Гб; в год  это 69Тб
 - RPS создания товара 778, один товар - 15Мб; это 11.4Гб/с или 342.86Пб в год

Получаем **342.93Пб в год**.

## Глобальная балансировка нагрузки

### Расположение датацентров

Исходя из предоставленного выше распределения, основной фокус следует сделать на размещении датацентров в США, где находится наибольшее число пользователей (82.95%).

Страны, где наиболее распространен Amazon представлены ниже:

![geography](img/geography.png)

Размешение датацентров в разных густонаселенных городах США, таких как Нью-Йорк, Лос-Анджелес, Чикаго и Сан-Франциско, поможет обеспечить более низкую задержку при обработке запросов пользователей, а также снизит риски, связанные с отказом отдельного центра.

Исходя из распределения стран, дополнительные датацентры стоит расположить в Европе (Амстердам) и в Азии (Гонконг). Это поможет снизить задержку и улучшить производительность для пользователей из этих регионов.

### Глобальная балансировка

Для глобальной балансировки можно использовать GeoDNS, который позволит направлять пользователей на ближайший доступный сервер на основе их географического местоположения. При использовании GeoDNS, при выполнении запросов DNS - имя пользователя будет разрешаться в IP-адрес сервера, ближайшего к пользователю с учетом его местоположения. Затем с балансировка производится помощью BGP Anycast.

## Локальная балансировка нагрузки

Входящие запросы: на первом уровне, балансировка нагрузки осуществляется посредством аппаратного балансировщика типа F5 Networks. Он обладает широким функционалом, позволяет распределять нагрузку между серверами, обрабатывать SSL-шифрование, предотвращать DDoS-атаки и т.д.

Следующий этап - балансировка нагрузки на уровне сети или транспорта (IP/ TCP / UDP). Здесь может быть использовано программное решение - Nginx, к примеру. Он позволяют балансировать нагрузку по алгоритмам, таким как Round Robin или Least Connections.

Балансировка приложений (Layer 7): на данном уровне запросы распределяются на основе контента. Решением для этого может служить, например, Envoy. Он обладает мощными возможностями по балансировке нагрузки, поддерживает gRPC и, что важно, создавался с учетом потребностей микросервисных архитектур.

Отказоустойчивость: для обеспечения постоянной доступности сервиса даже при отказе отдельных узлов необходимо применение протокола поддержания доступности, такого как Keepalived.

Для повышения безопасности можно использовать PFS, а Session Cache для улучшения производительности, а для для обеспечения SSL-сертификации сервис Let’s Encrypt.

## Логическая схема БД

![er](./img/er.png)

### Рассчёт занимаемого дискового пространства

Посчитаем, сколько за год потребуется дополнительной памяти.

**Типы данных в БД:**
 - varchar(x) - X Б 
 - boolean - 1 Б
 - bytea - 32 Б (+-)
 - int - 4 Б
 - bigint - 8 Б
 - timestamp - 8 Б
 - double - 8 Б

**Допущения при рассчете:**

- 20000 регистрация в день - это 7.3 млн в год
- Считаем, что в среднем у пользователя только один способ оплаты, а также, один адрес получения
- В среднем у пользователя 2 товара в корзине, каждый второй товар покупается (= удаляется из корзины). Будем считать, что одновременно у каждого пользователя 1 товар в корзине
- Пусть и каждого продавца в среднем по одному магазину. Всего продавцов регистрируется 2000 ежедневно или 0.73 млн в год
- 0.1 - количество добавлений товара в день на 1 пользователя, тогда в год на всех продавцов это 0.1 * 365 * 0.73 млн = 266 млн
- В каждом товаре максимально 8 фоток и 1 видео = 9 медиа на товар = 2394 млн строк
- 0.05 - добавление отзывов на товар в день на 1 пользователя, тогда в год на всех пользователей это 0.05 * 7.3 млн = 0.365 млн
- У амазона 23 главных категории, в каждой из которых примерно по 10 подкатегорий
- 1.45 - количество оформлений заказов в день на 1 пользователя, тогда в год на всех пользователей это 3863 млн
- На каждый заказ - одна оплата в среднем 
- На одного пользователя в среднем одна сессия в среднем

| Таблица         | Строк (млн) | Данных на строку | Всего | 
| ------------- |:-------------:| :-------: | :-------: |
| users        | 7.3 | 8 + 30 + 50 + 32 + 50 + 50 + 1 = 221 Б | 1.5 Гб |
| recipients |  7.3 | 8 + 50 * 6 + 4 * 2 + 20 + 100 + 8 = 444 Б | 3.02 Гб |
| payments_details    | 7.3 | 8 + 16 + 8 + 32 + 8 = 72 Б | 0.49 Гб |
| shopping_cards  | 7.3 | 8 + 4 + 8 + 8 = 36 Б| 0.24 Гб |
| stores  | 0.73 | 8 + 50 + 200 + 8 = 266 Б| 0.18 Гб |
| products  | 266 | 8 + 50 + 200 + 4 + 8 * 9 + 8 + 8 + 8 = 358 Б| 88 Гб |
| media  | 2394 | 8 + 100 + 50 = 158 Б| 352 Гб |
| comments  | 0.365 | 8 + 200 + 4 + 8 * 3 = 236 Б| 0.08 Гб |
| categories (без parent_id)  | 0.000023 | 8 + 50 = 58 Б| 1.3 Мб |
| categories (с parent_id)  | 0.000253 | 8 + 50 + 8 = 66 Б| 16 Мб |
| orders  | 3863 | 8 + 4 + 8 + 10 + 8 * 4 = 62 Б| 223 Гб |
| payments  | 3863 | 10 + 8 * 4 = 42 Б| 151 Гб |
| sessions | 7.3 | 8 + 100 + 8 + 8  = 124 Б| 0.84 Гб|

**Итого:** ~820 Гб в год (без учета медиа - они были выше)


## Источники
 1. https://www.similarweb.com/ru/website/amazon.com/#traffic
 2. https://www.statista.com/statistics/271412/most-visited-us-web-properties-based-on-number-of-visitors/
 3. https://vc.ru/trade/594954-top-zarubezhnyh-marketpleysov-dlya-prodazhi-tovarov-v-2023-amazon-etsy-i-drugie
 4. https://www.affiliatebay.net/ru/amazon-statistics/
 5. https://www.statista.com/statistics/235681/online-shoppers-digital-channels-of-choice-to-learn-about-products/
 6. https://market.us/statistics/e-commerce-websites/amazon/
 7. https://www.quora.com/What-percentage-of-buyers-write-reviews-on-Amazon
 8. https://0ca36445185fb449d582-f6ffa6baf5dd4144ff990b4132ba0c4d.ssl.cf1.rackcdn.com/IG_360piAmazon_9.13.16.pdf