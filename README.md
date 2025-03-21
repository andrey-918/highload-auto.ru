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
      - [3. Глобальная балансировка нагрузки](#3-глобальная-балансировка-нагрузки)
        - [Функциональное разбиение по доменам](#функциональное-разбиение-по-доменам) 
        - [Обоснования расположения ДЦ](#обоснования-расположения-дц)
        - [Расчет распределения запросов по типам запросов и ДЦ](#расчет-распределения-запросов-по-типам-запросов-и-дц)
        - [Схема Anycast балансировки](#схема-anycast-балансировки)
        - [Механизм регулировки трафика между ДЦ](#механизм-регулировки-трафика-между-дц)
      - [4. Локальная балансировка нагрузки](#4-локальная-балансировка-нагрузки)
        - [Балансировка входящих запросов](#балансировка-входящих-запросов)
        - [Балансировка межсервисных запросов](#балансировка-межсервисных-запросов)
        - [Отказоустойчивость входящих запросов](#отказоустойчивость-входящих-запросов)
        - [Отказоустойчивость межсервисных запросов](#отказоустойчивость-межсервисных-запросов)
  - [Список источников](#список-источников)

## Основная часть

### 1. Тема и целевая аудитория

Сервис "Авто.ру"[^1] является популярной платформой для покупки и продажи транспортных средств, а также предоставляет различные услуги, связанные с автомобилями.

#### Целевая аудитория

Целевая аудитория сервиса включает:
- **Размер аудитории**: более 30 миллионов активных пользователей в месяц [^2]
- **Местоположение**: Россия и страны СНГ

##### Анализ трафика

| Метрика                               | Значение                  |
|---------------------------------------|--------------------------|
| **Месячная аудитория**                | 30 000 000 пользователей   |
| **Дневная аудитория**                 | 2 500 000 пользователей      |
| **Среднее количество посещенных страниц пользователем**                 | 9.57 |      |
| **Среднее время посещения**           | 00:04:53                            |

Поскольку в месяц аудитория 30 миллионов, а среднем пользователь посещает сайт 2.5 дня в месяца [^3], следовательно в среднем 2.5 миллионов уникальных пользователей заходит на сайт в день (30 миллионов за месяц * 2.5 дня / 30 дня = 2.5 миллиона)

##### Веб-трафик
![Traffic by Country](img/traffic/countries.png) [^4]


#### Функционал

Для разработки MVP сервиса "Авто.ру" были выделены следующие ключевые функции:
1. **Создание объявления о продаже автомобиля**: пользователи могут создавать объявления с описанием и фотографиями.
2. **Поиск автомобилей по фильтрам**: пользователи могут искать автомобили по различным критериям (марка, модель, цена и т.д.).
3. **Оценка стоимости автомобиля**: сервис предоставляет возможность оценить стоимость автомобиля на основе рыночных данных.
4. **Обратная связь с продавцом**: пользователи могут связываться с продавцами через встроенный мессенджер.
5. **История цен на автомобили**: пользователи могут видеть динамику цен на интересующие их модели.
6. **Регистрация**: пользователи создают аккаунты, добавляют фотографии профиля и информацию о себе.
7. **Рекомендации**: персональная подборка предположительно интересных объявлений для пользователя
8. **Избранное**: добавление объявления в отдельную вкладку для быстрого доступа и просмотра


### 2. Расчет нагрузки

#### Продуктовые метрики

##### Месячная и дневная аудитория

  - Месячная аудитория: 30 млн. человек
  - Дневная аудитория: 2.5 млн. человек

##### Пользовательская активность

- **Просмотр объявлений**:
  - В среднем пользователь смотрит 10 объявлений за раз и заходит на сайт 2.5 раза в месяц.
  - Общее количество просмотров в месяц: 2.5 * 10 = 25 объявлений [^2]
  - В день это составит: 25 / 30 = 0.83 объявлений
  - Общее количество просмотров объявлений в день для всей аудитории: 0.83 * 2.5 = 2 08 млн. просмотров 

##### Объем данных

- **Количество объявлений**: 700 тыс. [^1]
- **Количество фотографий**:
  - Среднее количество фотографий на одно объявление: (12 + 35) / 2 = 23.5
  - Общее количество фотографий для всех объявлений: 183000 * 23.5 = 16.3 млн. фотографий
- **Количество пользователей**:
  - Допустим, что 30 млн пользователей зарегистрированы на сайте и имеют заполненную информацию, которая весит 1 Мб, тогда: 30 000 000 * 1 Мб = 30 000 ГБ  

- **Средний размер фотографий**:

  - Предполагаемый размер одной фотографии: 200 Кб.
  - Общий объем фотографий на сервисе: 16.3 млн * 200 Кб = 3 268 000 Мб = 3 200 Гб

- **Итоговая нагрузка**:

  - Дневная нагрузка: примерно 2.08 млн. объявлений в день.
  - Объем данных: общий объем фотографий на сервисе составляет около 3 200 Гб.
  - Общий объем данных объялений: 700 Гб + 3200 ГБ + 30 Гб = 3 930 Гб ≈ 4 ТБ

| Данные         | Количество | ГБ |
|-----------------------|-------------|-------------|
| Описание объвляения    | 700 000 |     700    |
| Пользовательская информация       | 30 000 000 |   30     |
| Фотографии      |  16 300 000 |   3 200     |
| Итог      |  - |   3 930    |


#### Технические метрики

##### Сетевой трафик

##### Оценка суммарного суточного трафика в Гб
  Чтобы оценить суммарный суточный трафик в гигабайтах, можно использовать средние значения RPS и предположить средний размер данных для каждого типа запроса.
  
  - Просмотр объявления: допустим размер одного запроса составляет 50 КБ.
  - Поиск объявления: допустим размер одного запроса составляет 30 КБ.
  - Загрузка страницы: допустим размер одного запроса составляет 100 КБ.
  - Загрузка фотографии: допустим размер одного запроса составляет 1 МБ.
  - Создание объявления: допустим размер одного запроса составляет 5000 КБ.
  - Добавить объявление в избранное: допустим размер одного запроса составляет 10 КБ.
  - Регистриция: допустим размер одного запроса составляет 400 КБ.
  - Авторизация: допустим размер одного запроса составляет 400 КБ.
  - Отправка сообщения в чате: допустим размер одного запроса составляет 1 КБ.
  - Получение сообщения в чате: допустим размер одного запроса составляет 1 КБ.
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
    
  - Создание объявления:
    Трафик = 2 500 × 5 000 КБ = 12 500 000 КБ = (12 500 000 / 1 024²) ≈ 11.91 Гбайт
    
  - Регистрация:
    Трафик = 25 000 × 400 КБ = 10 000 000 КБ = (10 000 000 / 1 024²) ≈ 9.54 Гбайт
    
  - Авторизация:
    Трафик = 250 000 × 400 КБ = 100 000 000 КБ = (100 000 000 / 1 024²) ≈ 95.37 Гбайт
    
  - Отправка сообщения в чате:
    Трафик = 75 000 000 × 1 КБ = 75 000 000 КБ = (75 000 000 / 1 024²) ≈ 71.53 Гбайт
    
  - Получение сообщения в чате:
    Трафик = 75 000 000 × 1 КБ = 75 000 000 КБ = (75 000 000 / 1 024²) ≈ 71.53 Гбайт
    
  - Другие запросы:
    Трафик =  1 375 000 × 20 КБ = 27 500 000 КБ = (27 500 000)/1 024² ≈ 26 Гбайт

    

###### Общий суммарный суточный трафик

| Тип запроса                      | Количество запросов | Размер запроса | Трафик (ГБ)  | Средний трафик (Гбит/с) |
|----------------------------------|---------------------|----------------|--------------|-------------------------|
| Просмотр объявления              | 13 750 000          | 50 КБ          | ≈ 655        | ≈ 0.0607                |
| Поиск объявления                 | 8 250 000           | 30 КБ          | ≈ 235        | ≈ 0.0218                |
| Загрузка страницы                | 2 750 000           | 100 КБ         | ≈ 262        | ≈ 0.0242                |
| Загрузка фотографии              | 1 375 000           | 1 МБ           | ≈ 1.31       | ≈ 0.000121              |
| Создание объявления              | 2500                | 5000 КБ        | ≈ 12         | ≈ 0.0011                |
| Добавить объявление в избранное  | 120 000             | 10 КБ          | ≈ 1.14       | ≈ 0.000106              |
| Регистрация                      | 25 000              | 400 КБ         | ≈ 10         | ≈ 0.00093               |
| Авторизация                      | 25 000              | 400 КБ         | ≈ 10         | ≈ 0.00093               |
| Отправка сообщения в чате        | 75 000 000          | 1 КБ           | ≈ 71         | ≈ 0.00662               |
| Получение сообщения в чате       | 75 000 000          | 1 КБ           | ≈ 71         | ≈ 0.00662               |
| Другие запросы                   | 1 375 000           | 20 КБ          | ≈ 26         | ≈ 0.00241               |
| Итого                            | 177 600 000         | 6000 КБ        | ≈ 1 450      | ≈ 0.135                 |



- Общий трафик: ≈ **1 450 ГБ**
- Средний трафик: ≈ **0.135 Гбит/с**




##### RPS по методам запросов
Берем средние значения количества запросов для пользователей на сайтах, направленных на продажу/покупку товаров (в данном случае ТС)
- 1% DAU регистрируются
- 1% DAU авторизуются
- 0.1% DAU создают объявления (относительно подобных сайтов мало, потому что намного чаще ищут автомобили, чем продают)
- Остальные предположения из расчета, что поведение пользователей на похожей платформе схоже, просто численно меньше в какое-то количество раз [^5]

| Метод запроса                        | Количество на пользователя в сутки | Общее количество в сутки | Среднее RPS | Пиковое RPS |
|--------------------------------------|------------------------------------|--------------------------|-------------|-------------|
| Регистрация                          | 0.01                               | 25 000                   | ~0.29       | ~1          |
| Авторизация                          | 0.01                               | 25 000                   | ~0.29       | ~1          |
| Создание объявления                  | 0.001                              | 2 500                    | ~0.03       | ~0.08       |
| Поиск                                | 3.3                                | 8 250 000                | ~95.4       | ~200        |
| Просмотр объявления                  | 5.5                                | 13 750 000               | ~159        | ~300        |
| Добавить объявление в избранное      | 0.4                                | 120 000                  | ~13.9       | ~30         |
| Отправка сообщения в чате            | 5                                  | 75 000 000               | ~867        | ~2 000      |
| Получение сообщения в чате           | 5                                  | 75 000 000               | ~867        | ~2 000      |




### 3. Глобальная балансировка нагрузки

#### Функциональное разбиение по доменам
- **[Домен 1](https://auto.ru)**: Основной интерфейс (поиск и просмотр объявлений)
- **[Домен 2](https://auth.auto.ru/login/)**: Пользовательский интерфейс (регистрация, авторизация)
- **[Домен 3](https://auto.ru/rossiya/cars/used/)**: Просмотр объявлений
- **[Домен 4](https://auto.ru/my/)**: Административный интерфейс (управление объявлениями)

#### Обоснования расположения ДЦ
- **Критерии выбора**:
  - **Задержка**: Выбор ДЦ ближе к целевым пользователям для уменьшения времени отклика.
  - **Надежность**: Размещение в разных географических регионах для обеспечения отказоустойчивости.
  - **Зависимость от трафика**: Учет пикового времени использования и распределение нагрузки по регионам.
  
- **Влияние на продуктовые метрики**:
  - Уменьшение времени загрузки страниц.
  - Повышение качества обслуживания пользователей (QoS).
  - Устойчивость к сбоям и обеспечение доступности сервиса.
 
Выбор расположения ДЦ будем делать из логических соображений о количестве автомобилей и необходимости покупки личного транспортного средства.
  
Можно предположить, что в Москве будет наибольшее количество пользователей, покупок/продаж и объявлений, так как это наибольший город страны. Санкт-Петербург второй по населению. А вот Новосибирск, хоть и третий в списке городов по населению, имеет всего 35к объявлений на Auto.ru, когда в Владивостоке 90к, потому что он близок к границам, и есть возможность ввозить машины из других стран.
 
| **Город** | **Активные объявления** |
| - | - |
| Москва | [230К](https://auto.ru/moskva/cars/all/) |
| Санкт-Петербург | [110К](https://auto.ru/sankt-peterburg/cars/all/) |
| Владивосток | [90К](https://auto.ru/vladivostok/) |
| Сочи | [50К](https://auto.ru/sochi/cars/all/) |
| Новосибирск |  [35К](https://auto.ru/novosibirsk/cars/all/) |


Имеем следующее распределние нагрузки по стране по количеству объявлений и активных пользователей:

```mermaid
pie
  "Москва": 33
  "Санкт-Петербург": 16
  "Владивосток": 13
  "Сочи": 7
  "Новосибирск": 5
  "Остальная Россия": 26
```


Оставляем только 3 - это и будут дата-центры, два в Европе и один на Азию. Нагрузка будет примерно распределяться следующим образом (исходя из количества активных объявлений в городе, в котором расположен дата-центр, а также близлежащих городов):

```mermaid
pie
  "Москва": 35
  "Санкт-Петербург": 25
  "Новосибирск": 40
```

Разумно будет оставить только три ДЦ - Москву, Санкт-Петербург и Новосибирск, тк остальные центры нагружаются намного меньше, а Новосибирск нужен для соединения с Азией

![MapOfDC](img/dc/mapofdc.png)

#### Расчет распределения запросов по типам запросов и ДЦ

| Тип запроса                      | RPS     | Москва     | Санкт-Петербург | Новосибирск | 
|----------------------------------|---------|------------|-----------------|-------------|
| Регистрация                      | 0.29    | 0.1        | 0.07            | 0.12        |
| Авторизация                      | 0.29    | 0.1        | 0.07            | 0.12        |
| Создание объявления              | 0.03    | 0.01       | 0.007           | 0.013       |
| Поиск                            | 95.4    | 33         | 24              | 38.4        |
| Просмотр объявления              | 159     | 56         | 40              | 63          |
| Добавить объявление в избранное  | 13.9    | 4.9        | 3.5             | 5.5         |
| Отправка сообщения в чате        | 867     | 303        | 217             | 347         |
| Получение сообщения в чате       | 867     | 303        | 217             | 347         |
| Итог                             | ~2 000  | ~700       | ~500            | ~800        |


#### Схема Anycast балансировки

BGP Anycast — это метод маршрутизации, который позволяет нескольким серверам (или дата-центрам) делить один и тот же IP-адрес. Вот как это работает:

- Настройка Anycast: В каждом из дата-центров настраивается маршрутизаторы так, чтобы они объявляли один и тот же IP-адрес через протокол BGP (Border Gateway Protocol). Таким образом, несколько дата-центров могут "представлять" один и тот же IP-адрес.

- Маршрутизация по метрикам: Когда пользователь делает запрос к этому IP-адресу, его интернет-провайдер (ISP) использует BGP для определения наилучшего маршрута к этому IP. BGP основывается на различных метриках, таких как количество хопов до целевой сети, чтобы выбрать наиболее оптимальный путь.

- Географическое распределение: Поскольку BGP Anycast направляет трафик к ближайшему дата-центру с точки зрения маршрутизации, пользователи автоматически подключаются к тому дата-центру, который находится ближе всего в терминах сетевой инфраструктуры. Это помогает уменьшить задержки и повысить скорость отклика.

- Обработка отказов: Если один из дата-центров становится недоступным, BGP автоматически перестает объявлять этот IP-адрес через него. Запросы пользователей будут перенаправлены к другим доступным дата-центрам, что обеспечивает отказоустойчивость.



#### Механизм регулировки трафика между ДЦ
- Использование CDN (Content Delivery Network) позволяет разместить часто запрашиваемый контент (например, изображения, скрипты и статические файлы) ближе к конечным пользователям, тем самым снижая нагрузку на основные ДЦ.


### 4. Локальная балансировка нагрузки

#### Балансировка входящих запросов
Для балансировки входящих запросов мы выберем NGINX на уровне L7. Это решение обосновано следующими факторами:

1. Эффективность обработки запросов: NGINX отлично справляется с большим количеством запросов, обеспечивая кэширование статического контента, такого как фотографии автомобилей и информации об автомобиле. Это позволяет ускорить время отклика и снизить нагрузку на бэкэнд-серверы.

2. Гибкость настройки: NGINX можно легко адаптировать под требования инфраструктуры.

3. SSL-терминация: NGINX будет расшифровывать HTTPS-трафик, что разгрузит бэкэнд-серверы, позволяя им обрабатывать уже расшифрованные данные.

4. Интеграция с Kubernetes: Kubernetes будет автоматически масштабировать приложение, добавляя или убирая ресурсы в зависимости от текущей нагрузки, а NGINX будет сразу же перенаправлять трафик на новые экземпляры.


#### Балансировка межсервисных запросов

Для обработки межсервисных запросов мы будем использовать балансировку с алгоритмом ***Least Connections***. Этот алгоритм направляет новые запросы на сервер с наименьшим количеством активных подключений, что позволяет избежать перегрузки. В случае сбоя одного из серверов, запросы автоматически перенаправляются на другие работающие сервера, что поддерживает отказоустойчивость системы. В качестве прокси-сервера для межсервисного взаимодействия мы выберем ***Envoy***.

#### Отказоустойчивость входящих запросов

- Два экземпляра NGINX устраняют единую точку отказа. Балансировка трафика между ними будет осуществляться с помощью метода L3 Direct Routing, который имеет ряд преимуществ: обратный трафик не проходит через балансировщик, он прост в настройке и требует минимальных ресурсов, так как заключается в изменении MAC-адреса.

- NGINX поддерживает механизм failover, выполняя регулярные проверки состояния бэкэнд-серверов (health checks). Если один из серверов станет недоступным, NGINX автоматически исключит его из пула активных серверов и перенаправит трафик на работающие серверы.

- Kubernetes обеспечит авто-масштабирование: при увеличении нагрузки новые экземпляры веб-серверов будут добавлены автоматически, и NGINX начнет направлять на них трафик.

#### Отказоустойчивость межсервисных запросов

- Два экземпляра Envoy устраняют единую точку отказа.

- Envoy также будет выполнять проверки состояния бэкэнд-сервисов. Если один из сервисов станет недоступным, Envoy перенаправит запросы на доступные экземпляры.





## Список источников

[^1]: [Официальный сайт Auto.ru](https://www.auto.ru) - информация о функционале и услугах.
[^2]: [SpyMetrics](https://spymetrics.ru/ru/website/auto.ru) - анализ трафика auto.ru
[^3]: [Yandex](https://yandex.ru/support/autoru-legal/ru) - о сервисе для партнеров
[^4]: [Website Analysis similarweb](https://www.similarweb.com/ru/website/auto.ru) - анализ веб-трафика auto.ru
[^5]: [Inclient](https://inclient.ru/avito-stats/#avitoru) - анализ веб-трафика avito от Inclient.ru

