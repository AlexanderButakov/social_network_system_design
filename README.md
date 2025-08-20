# social_network_system_design
Designing a mega cool social network app


## Функциональные требования:
* Создание поста
  - Загрузка фотографий
  - Добавление текста описания, заголовка
  - Добавление геометки
  - Редактирование поста
* Просмотр постов других пользователей
  * добавление комментариев
  * оценка поста
* Подписка на пользователя
* Поиск мест
  * просмотр постов об этих местах
* Просмотр ленты пользователей
  * Сортировка в обратном хронолог. порядке
* Просмотр своей ленты
  * Сортировка в обратном хронолог. порядке


## Нефункциональные требования:
* 10 000 000 DAU
* Доступность 99,9
* Доступ через мобильное приложение и браузер
* Данные храним всегда
* Аудитория стран СНГ
* Активность пользователей:
  * Посты:
    * Создание поста - в среднем 1 раз в неделю (100 символов заголовок, 5000 символов описание, 10 фотографий (400кб макс. размер фото))
    * Чтение постов - в среднем 10 постов в день
  * Комментарии:
    * Просмотр - 10 раз по 20 комментариев / день
    * Добавление - 20 комментариев (3000 символов) / день (Половина DAU будет оставлять комментарии)
  * Оценки:
    * Добавление оценки - 10 оценок / день (половина DAU оценивает посты)
  * Подписка:
    * Подписка на 1 пользователя в день
    * Просмотр списка своих подписчиков - в среднем 1 раз в день. По 20 подписчиков в выдаче.
    * Просмотр списка авторов, на кого сам подписан - в среднем 1 раз в день. По 20 авторов в выдаче.
  * Поиск:
    * Два запроса в минуту
    * 50 постов в выдаче (Заголовок + часть описания)
  * Своя лента:
    * Просмотр ленты два раза в день
    * Неограниченное количество постов (пагинация по 20 шт., заголовок + часть описания + 1 фото)
  * Ленты других пользователей:
    * Просмотр 20 лент / день
    * Неограниченное количество постов (пагинация по 20 шт., заголовок + часть описания + 1 фото)
* Лимиты:
  * Сезонность - в летние месяцы больше активность
  * Тайминги:
    * Создание поста: 3 сек.
    * Чтение поста: 1 сек.
    * Добавление комментария: 1 сек.
    * Чтение комментариев: 2 сек.
    * Добавление оценки: 1 сек.
    * Поиск: 2 сек.
    * Просмотр ленты: 2 сек.

## Модель данных:
* Пост:
  * id:  8 bytes
  * user_id:  8 bytes
  * title:  100 bytes
  * contents:  5200 bytes (5.2Kb)
  * geocoordinates [...]:  72 bytes
  * photos [...]:  150 bytes
  * created_at:  8 bytes

* Комментарий:
  * id:  8 bytes
  * post_id:  8 bytes
  * user_id:  8 bytes
  * contents: 3000 bytes (3Kb)
  * created_at:  8 bytes

* Оценка:
  * post_id:  8 bytes
  * score:  32 bytes (это сумма. Приходит 8 байт в запросе)

* Подписка:
  * following_id  8 bytes
  * follower_id 8 bytes
  * created_at: 8 bytes

* Пользователь:
  * id: 8 bytes
  * username: 20 bytes
  * created_at: 8 bytes

## Расчет нагрузки:
* Посты:
  * RPS: 
    * RPS(w текст) = 10 000 000 * 1 / 7 / 86400 = 17
    * RPS(w фото) = 10 000 000 * 10 / 7 / 86400 = 165
    * RPS(r) = 10 000 000 * 10 / 86400 = 1157
  * Трафик:
    * Traffic(w текст) = 17 * 5550 = 94.35Kb
    * Traffic(w фото) = 165 * 400000 = 66Mb
    * Traffic(r) = 1157 * (66Mb + 94.35Kb) = 76.5Gb
  * Capacity:
    * C(текст) = 94.35Kb * 86400 * 365 = 2975421600Kb = 3Tb
    * C(фото) = 66Mb * 86400 * 365 = 2081376000Mb = 2081Tb
* Комментарии:
  * RPS: 
    * RPS(r) = 10 000 000 * 10* 20 / 86400 = 2314
    * RPS(w) = 10 000 000 / 2 * 20 / 86400 = 1157
  * Трафик:
    * Traffic(w) = 1157 * 3Kb = 3471Kb (3.5Mb)
    * Traffic(r) = 2314 * 3Kb * 20 = 138840Kb (139Mb)
  * Capacity:
    * C = 3.5Mb * 86400 * 365 = 110376000Mb = 110Tb
* Оценки:
  * RPS: 
    * RPS(w) = 10 000 000 / 2 * 10 / 86400 = 579
    * RPS(r) = 10 000 000 * 10 / 86400 = 1157
  * Трафик:
    * Traffic(w) = 579 * 16b = 9264b (9.3Kb)
    * Traffic(r) = 1157 * 40b = 46280b (46.3Kb)
  * Capacity:
    * C = 9.3Kb * 86400 * 365 = 293284800Kb = 300Gb
* Подписки:
  * RPS: 
    * RPS(w) = 10 000 000 * 1 / 86400 = 116
    * RPS(r) = 10 000 000 * 1 * 20 / 86400  = 2314
  * Трафик:
    * Traffic(w) = 116 * 24b = 2784b (2.8Kb)
    * Traffic(r) = 2314 * 24b = 55536b (55.5Kb)
  * Capacity:
    * C = 2.8Kb * 86400 * 365 = 88300800Kb = 88Gb
* Поиск:
  * RPS:
    * RPS(r) = 10 000 000 * 2 / 60 = 333333
  * Traffic:
    * Traffic(r) = 333333 * 50 * (100b + 1000) = 18Gb
* Просмотр своей ленты:
  * RPS:
    * RPS(r) = 10 000 000 * 2 / 86400 = 231
  * Traffic:
    * Traffic(r) = 231 * 20 * 400000b + 100b + 1000 = 1.9Gb
* Просмотр чужих лент:
  * RPS:
    * RPS(r) = 10 000 000 * 20 / 86400 = 2314
  * Traffic:
    * Traffic(r) = 2314 * 20 * 400000b + 100b + 1000 = 18.6Gb

### Количество соединений
* 10000000 * 0.1 = 1000000


### Количество дисков для хранения информации
* Посты (без фото) SSD (sata)
  * Disks_for_iops = iops / disk_iops = (1157 + 17) / 1000 = 2 disks 
  * Disks_for_throughput = traffic_per_second / disk_throughput = 110Mb / 500Mb/s = 1 disk
  * Disks_for_capacity = capacity / disk_capacity = 3Tb / 100Tb = 1 disk
  * Disks = max(ceil(Disks_for_capacity), ceil(Disks_for_throughput), ceil(Disks_for_iops)) = 2

* Фото SSD(sata)
  * Disks_for_iops = iops / disk_iops = 1322 / 1000 = 2 disks
  * Disks_for_throughput = traffic_per_second / disk_throughput = 76428Mb / 500Mb/s = 153 disks
  * Disks_for_capacity = capacity / disk_capacity = 2081Tb / 100Tb = 21 disks
  * Disks = max(ceil(Disks_for_capacity), ceil(Disks_for_throughput), ceil(Disks_for_iops)) = 153 

* Комментарии SSD (sata)
  * Disks_for_iops = iops / disk_iops = 3417 / 1000 = 4 disks
  * Disks_for_throughput = traffic_per_second / disk_throughput = 143Mb / 500Mb/s = 1 disk
  * Disks_for_capacity = capacity / disk_capacity = 110Tb / 100Tb = 2 disks
  * Disks = max(ceil(Disks_for_capacity), ceil(Disks_for_throughput), ceil(Disks_for_iops)) = 4 

* Оценки
  * Disks_for_iops = iops / disk_iops = 1736 / 1000 = 2 disks
  * Disks_for_throughput = traffic_per_second / disk_throughput = 0.056Mb / 500Mb/s = 1
  * Disks_for_capacity = capacity / disk_capacity = 110Tb / 100Tb = 0.3Tb / 100Tb = 1
  * Disks = max(ceil(Disks_for_capacity), ceil(Disks_for_throughput), ceil(Disks_for_iops)) = 2 

* Подписки
  * Disks_for_iops = iops / disk_iops = 2430 / 1000 = 3 disks
  * Disks_for_throughput = traffic_per_second / disk_throughput = 0.059Mb / 500Mb/s = 1
  * Disks_for_capacity = capacity / disk_capacity = 110Tb / 100Tb = 0.09Tb / 100Tb = 1
  * Disks = max(ceil(Disks_for_capacity), ceil(Disks_for_throughput), ceil(Disks_for_iops)) = ...

* Пользователи