# Auto.ru

## Содержание

- [Auto.ru](#auto.ru)
  - [Содержание](#содержание)
    - [Основная часть](#основная-часть)
    - [1. Тема и целевая аудитория](#1-тема-и-целевая-аудитория)
      - [Целевая аудитория](#целевая-аудитория)
        - [Анализ трафика](#анализ-трафика)
        - [Веб-трафик](#веб-трафик)
      - [Функционал](#функционал)
    - [2. Расчет нагрузки](#2-расчет-нагрузки)
      - [Продуктовые метрики](#продуктовые-метрики)
        - [Месячная и дневная аудитория](#месячная-и-дневная-аудитория)
        - [Пользовательская активность](#пользовательская-активность)
        - [Объем данных](#объем-данных)
      - [Технические метрики](#технические-метрики)
        - [Сетевой трафик](#сетевой-трафик)
          - [Пиковое потребление трафика](#пиковое-потребление-трафика)
          - [RPS во время пикового потребления](#rps-во-время-пикового-потребления)
          - [Просмотр объявления](#просмотр-объявления)
          - [Поиск объявления](#поиск-объявления)
          - [Загрузка страницы](#загрузка-страницы)
          - [Загрузка фотографии](#загрузка-фотографии)
          - [Другие запросы](#другие-запросы)
        - [Оценка суммарного суточного трафика в Гб](#оценка-суммарного-суточного-трафика-в-гб)
          - [Общий суммарный суточный трафик](#общий-сумарный-суточный-трафик)
        - [RPS по методам запросов](#rps-по-методам-запросов)
  - [Список источников](#список-источников)

## Основная часть

### 1. Тема и целевая аудитория

Сервис "Авто.ру"[^1] является популярной платформой для покупки и продажи транспортных средств, а также предоставляет различные услуги, связанные с автомобилями.

#### Целевая аудитория

Целевая аудитория сервиса включает:
- **Размер аудитории**: более 30 миллионов активных пользователей в месяц
- **Местоположение**: Россия и страны СНГ

##### Анализ трафика

| Метрика                               | Значение                  |
|---------------------------------------|--------------------------|
| **Месячная аудитория**                | 30 000 000 пользователей   |
| **Дневная аудитория**                 | 2 500 000 пользователей      |
| **Среднее количество посещенных страниц пользователем**                 | 9.57 |      |
| **Среднее время посещения**           | 00:04:53                            |

Поскольку в месяц аудитория 30 миллионов, а в день заходят 2 миллиона, следовательно в среднем пользователь посещает сайт 2 5 дня в месяц

##### Веб-трафик
![Traffic by Country](img/traffic/countries.png) [^2]


#### Функционал

Для разработки MVP сервиса "Авто.ру" были выделены следующие ключевые функции:
1. **Создание объявления о продаже автомобиля**: пользователи могут создавать объявления с описанием и фотографиями.
2. **Поиск автомобилей по фильтрам**: пользователи могут искать автомобили по различным критериям (марка, модель, цена и т.д.).
3. **Оценка стоимости автомобиля**: сервис предоставляет возможность оценить стоимость автомобиля на основе рыночных данных.
4. **Обратная связь с продавцом**: пользователи могут связываться с продавцами через встроенный мессенджер.
5. **История цен на автомобили**: пользователи могут видеть динамику цен на интересующие их модели.
6. **Услуги по оформлению кредита**: пользователи могут получить информацию о возможных вариантах кредитования для покупки автомобиля.


### 2. Расчет нагрузки

#### Продуктовые метрики

##### Месячная и дневная аудитория

  - Месячная аудитория: 30 млн. человек
  - Дневная аудитория: 2.5 млн. человек

##### Пользовательская активность

- **Просмотр объявлений**:
  - В среднем пользователь смотрит 10 объявлений за раз и заходит на сайт 2.5 раза в месяц.
  - Общее количество просмотров в месяц: 2.5 * 10 = 25 объявлений
  - В день это составит: 25 / 30 = 0.83 объявлений
  - Общее количество просмотров объявлений в день для всей аудитории: 0.83 * 2.5 = 2 08 млн. просмотров 

##### Объем данных

- **Количество объявлений**: 183 тыс.
- **Количество фотографий**:
  - Среднее количество фотографий на одно объявление: (12 + 35) / 2 = 23.5
  - Общее количество фотографий для всех объявлений: 183000 * 23.5 = 4.3 млн. фотографий
- **Количество пользователей**:
  - Допустим, что 30 млн пользователей зарегистрированы на сайте и имеют заполненную информацию, которая весит 1 Мб, тогда: 30 000 000 * 1 Мб = 30 000 ГБ  

- **Средний размер фотографий**:

  - Предполагаемый размер одной фотографии: 200 Кб.
  - Общий объем фотографий на сервисе: 4.3 млн * 200 Кб = 860000 Мб = 860 Гб

- **Итоговая нагрузка**:

  - Дневная нагрузка: примерно 2.08 млн. объявлений в день.
  - Объем данных: общий объем фотографий на сервисе составляет около 860 Гб.
  - Общий объем данных объялений: 183 Гб + 860 ГБ + 30 Гб = 1 073 Гб ≈ 1 100 ГБ

| Данные         | Количество | ГБ |
|-----------------------|-------------|-------------|
| Описание объвляения    | 183 000 |     183    |
| Пользовательская информация       | 30 000 000 |   30     |
| Фотографии      |  4 300 000 |   860     |
| Итог      |  - |   1 100    |


#### Технические метрики

##### Сетевой трафик

###### Пиковое потребление трафика

  Предположим, что пиковое время использования сервиса приходится на вечерние часы. Если в пиковый период активность пользователей составляет половину от активности за весь день:
  
  - Пиковая дневная аудитория: 2.5 M / 2 = 1.25 M
  - Среднее количество запросов на пользователя за сессию: 10 просмотры + 1 загрузка страницы = 11
  
###### RPS во время пикового потребления

  Если пиковое время составляет 4 часа (14 400 секунд): RPS (пиковый) = Пиковая дневная аудитория * Запросы на пользователя / Количество секунд в пиковом времени = 1 250 000 * 11 / 14 400 ≈ 1 000 

###### Просмотр объявления
  - Предположим, что 50% запросов — это просмотры объявлений.
  - Общее количество запросов = 2.5M * 11 = 27.5M
  - Запросы на просмотр объявления = 27.5M * 0.5 = 13.75M
  - Средний RPS = 13 750 000 / 86 400 (количество секунд в сутках) ≈ 159
  - Пиковый RPS = 1 000

###### Поиск объявления

  - Предположим, что 30% запросов — это поиск объявлений.
  - Запросы на поиск объявления = 27.5M * 0.3 = 8.25M
  - Средний RPS = 8 250 000 / 86 400 ≈ 95
  - Пиковое ≈ 550

###### Загрузка страницы

  - Предположим, что 10% запросов — это загрузка страниц.
  - Запросы на загрузку страницы = 27.5M * 0.1 = 2.75M.
  - Средний RPS = 2 750 000 / 86 400 ≈ 32.
  - Пиковый RPS = 200 (как указано).


###### Загрузка фотографии

  - Предположим, что 5% запросов — это загрузка фотографий.
  - Запросы на загрузку фотографий = 27.5M * 0.05 = 1.375M
  - Средний RPS = 1 375 000 / 86 400 ≈ 16
  - Пиковый RPS = 100

###### Другие запросы

  - Остальные запросы составляют оставшиеся проценты (5%).
  - Запросы на другие запросы = 27.5M * 0.05 = 1.375M
  - Средний RPS = 1 375 000 / 86 400 ≈ 16
  - Пиковый RPS = 100

##### Оценка суммарного суточного трафика в Гб
  Чтобы оценить суммарный суточный трафик в гигабайтах, можно использовать средние значения RPS и предположить средний размер данных для каждого типа запроса.
  
  - Просмотр объявления: допустим размер одного запроса составляет 50 КБ.
  - Поиск объявления: допустим размер одного запроса составляет 30 КБ.
  - Загрузка страницы: допустим размер одного запроса составляет 100 КБ.
  - Загрузка фотографии: допустим размер одного запроса составляет 1 МБ.
  - Другие запросы: допустим размер одного запроса составляет 20 КБ.
  
  Теперь можно рассчитать суммарный суточный трафик:
  
  - Просмотр объявления:
    Трафик =  13 750 000 × 50 КБ = 687 500 000 КБ = (687 500 000)/1 024² ≈ 655 Гбайт 
  - Поиск объявления:
    Трафик =  8 250 000 × 30 КБ = 247 500 000 КБ = (247 500 000)/1 024² ≈ 235 Гбайт 
  - Загрузка страницы:
    Трафик =  2 750 000 × 100 КБ = 275 000 000 КБ = (275 000 000)/1 024² ≈ 262 Гбайт 
  - Загрузка фотографии:
    Трафик =  1 375 000 × 1 МБ = 1 375 000 КБ = (1 375 000)/1 024² ≈ 1.31 Гбайт 
  - Другие запросы:
    Трафик =  1 375 000 × 20 КБ = 27 500 000 КБ = (27 500 000)/1 024² ≈ 26 Гбайт 

###### Общий суммарный суточный трафик

| Тип запроса          | Количество запросов | Размер запроса | Трафик (ГБ) | Средний трафик (Гбит/с) |
|----------------------|---------------------|----------------|--------------|--------------------------|
| Просмотр объявления   | 13,750,000          | 50 КБ          | ≈ 655        | ≈ 0.0607                 |
| Поиск объявления      | 8,250,000           | 30 КБ          | ≈ 235        | ≈ 0.0218                 |
| Загрузка страницы     | 2,750,000           | 100 КБ         | ≈ 262        | ≈ 0.0242                 |
| Загрузка фотографии   | 1,375,000           | 1 МБ           | ≈ 1.31       | ≈ 0.000121               |
| Другие запросы       | 1,375,000           | 20 КБ          | ≈ 26         | ≈ 0.00241                |


- Общий трафик: ≈ **1,179 ГБ**
- Средний трафик: ≈ **0.109 Гбит/с**




##### RPS по методам запросов

| Метод запроса         | Средний RPS | Пиковый RPS |
|-----------------------|-------------|--------------|
| Просмотр объявления    |     ~159    |    1 000     |
| Поиск объявления       |     ~95     |    550       |
| Загрузка страницы      |     ~32     |    200       |
| Загрузка фотографии    |     ~16     |    100       |
| Другие запросы        |     ~16     |    100       |






## Список источников

[^1]: [Официальный сайт Auto.ru](https://www.auto.ru) - информация о функционале и услугах.
[^2]: [Website Analysis similarweb](https://www.similarweb.com/ru/website/auto.ru) - анализ веб-трафика auto.ru
