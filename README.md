# Проектирование высоконагруженных систем. Курсовой проект

Попов М.А. АПО-31

## 1. Тема и целевая аудитория

Облачное хранилище (VK Облако).

В связи с отсутсвием открытой информации о статистике VK Облака, будем опираться на статистику сервиса Dropbox.  

MVP включает:  
- Загрузка файлов любого формата в хранилище  
- Скачивание файлов из хранилища  
- Просмотр загруженных файлов через веб-интерфейс  
- Предоставление доступа на просмотр файлов третьим лицам  

Целевая аудитория ([\[1\]](https://www.similarweb.com/ru/website/dropbox.com/#geography)):  
- США - 56 миллионов в месяц  
- Великобритания - 9 миллиона в месяц  
- Япония - 8 миллиона в месяц  
- Германия - 6.5 миллиона в месяц  
- Канада - 6 миллиона в месяц  
- Другие страны - 73.5 миллионов в месяц  

## 2. Расчет нагрузки

### Продуктовые метрики

Согласно статистике [\[1\]](https://www.similarweb.com/ru/website/dropbox.com/#traffic), Dropbox в месяц посещают около 160 миллионов пользователей.  
В среднем ежедневно около 5.3 миллиона посещений.  

Размер хранилища, доступный при бесплатном использовании, равен 2 Гб.  

Пусть около 10% посещений происходит с запросом авторизации, тогда ежедневно выполняется 530 тысяч запросов на авторизацию.  
На первой странице сервиса отображается список всех файлов, поэтому будем считать, что каждый десятый хотя бы раз попадает на эту страницу. Пусть таких запросов 60 млн в день.  
Согласно статистике [\[2\]](https://expandedramblings.com/index.php/dropbox-statistics/) за 2016 год в Dropbox ежедневно просматривают порядка 1 200 миллиона файлов. Так как с 2016 года аудитория зарегистрированных пользователей выросла на 200 миллионов [\[3\]](https://www.wallstreetzen.com/stocks/us/nasdaq/dbx/statistics), возьмём количество загрузок файлов равное 1 700 миллионам.  
Загрузка файлов происходит реже - около 500 млн раз в день (с учетом автоматической синхронизации).  
Согласно статистике [\[2\]](https://expandedramblings.com/index.php/dropbox-statistics/) за 2015 год каждый час создается 100 тысяч новых публичных папок и файлов. С 2015 года аудитория Dropbox увеличилась на 300 миллионов зарегистрированных пользователей [\[3\]](https://www.wallstreetzen.com/stocks/us/nasdaq/dbx/statistics), поэтому будем считать, что каждый час создается 180 тысяч шареных файлов. Тогда в день таких файлов создается порядка 4.4 миллиона.  

**Среднее количество действий пользователей**
Тип запроса             | Среднее количество (млн/день)
----------------------- | -----------------------------
Авторизация             | 0.53
Просмотр списка файлов  | 60
Просмотр файлов         | 1 700
Загрузка файлов         | 500
Шаринг файла            | 4.4

**Среднее количество действий одного пользователя**
Тип запроса             | Среднее количество (в день)
----------------------- | -----------------------------
Авторизация             | 0.1
Просмотр списка файлов  | 11
Просмотр файлов         | 320
Загрузка файлов         | 94
Шаринг файла            | 1
### Технические метрики

Согласно статистике [\[3\]](https://www.wallstreetzen.com/stocks/us/nasdaq/dbx/statistics), на текущий момент в Dropbox зарегистрировано 700 млн пользователей, из которых 15.5 млн (~2%) - платные пользователи.  

Размер бесплатного хранилища пользователя равен 2 Гб. Примем, что пользователи в среднем занимают 1 Гб в облаке, тогда размер хранилища для бесплатных пользователей равен 684.5 * 10^6 * 1 = 668 457 Тб.

Dropbox предлагает неограниченнный объем хранилища на самой дорогой подписке. Так как нет открытой информации о среднем объеме хранения платных пользователей, возьмем его равным 128 Гб. Тогда размер хранилища для платных пользователей равен 15.5 * 10^6 * 128 = 1 937 500 Тб.

Таким образом, суммарный объем хранилища составляет около 2 600 000 Тб.

Обычно пользователи хранят в облаке фотографии или текстовые документы. Будем считать, что их средний размер 4 Мб.  
Считаем, что в пике средняя нагрузка увеличина в 2 раза.

**Входящий трафик**
Тип запроса             | Объём 1 запроса (Кб) | Суточный объём (Гб/сутки) | Пиковое потребление в течении суток (Гбит/с)
----------------------- | -------------------- | ------------------------- | --------------------------------------------
Авторизация             | 1                    | 0.5                       | 0.00009
Загрузка файлов         | 4096                 | 1 953 125                 | 362
Шаринг файла            | 1                    | 4.2                       | 0.0008

**Входящий трафик в расчете на одного пользователя**
Тип запроса             | Объём 1 запроса (Кб) | Суточный объём (Кб/сутки) | Пиковое потребление в течении суток (Кбит/с)
----------------------- | -------------------- | ------------------------- | --------------------------------------------
Авторизация             | 1                    | 0.1                       | 0.00002
Загрузка файлов         | 4096                 | 385 024                   | 71
Шаринг файла            | 1                    | 1                         | 0.0002

**Исходящий трафик**
Тип запроса             | Объём 1 запроса (Кб) | Суточный объём (Гб/сутки) | Пиковое потребление в течении суток (Гбит/с)
----------------------- | -------------------- | ------------------------- | --------------------------------------------
Просмотр списка файлов  | 512                  | 29 296                    | 5
Просмотр файлов         | 4096                 | 6 640 625                 | 1 230

**Исходящий трафик в расчете на одного пользователя**
Тип запроса             | Объём 1 запроса (Кб) | Суточный объём (Кб/сутки) | Пиковое потребление в течении суток (Кбит/с)
----------------------- | -------------------- | ------------------------- | --------------------------------------------
Просмотр списка файлов  | 512                  | 5 632                     | 1
Просмотр файлов         | 4096                 | 1 310 720                 | 243


**Количество RPS по типам запросов**
Тип запроса             | RPS
----------------------- | -----
Авторизация             | 12
Просмотр списка файлов  | 1 400
Просмотр файлов         | 40 000
Загрузка файлов         | 11 600
Шаринг файла            | 51


## 3. Логическая схема БД
![схема БД](https://github.com/4Marvin2/highload_cloud/blob/main/DB_diagram.svg)
