# Приложение для создания и редактирования информации о встречах сотрудников

## Задание
Код содержит ошибки разной степени критичности. Некоторых из них стилистические, а некоторые даже не позволят вам запустить приложение. Вам необходимо найти и исправить их.

## Запуск
```
npm i
npm run dev
```

Для сброса данных в базе:
```
npm run reset-db
```

## Дневник разработчика

Для запуска приложения я использовал встроенный в редактор VS Code режим отладки.

При запуске сразу видим ошибку. Пробежавшись по стеку вызовов видим, что скрипт отвалился во время создания инстанса Sequalize. 

Идем в документацию Sequalize и видим, что **на вход в конструктор должно подаваться три аргумента, затем уже опции, а в текущей реализации их передается лишь два.** Принимаем судьбоносное решение - добавляем аргумент password (еще один null). Приложение запускается. 


Первый route "/" работает, пробуем перейти на "/graphql", и видим ошибку 404. Странно, ведь route для graphql объявлен. Пристально изучаем [./graphql/routes/](./graphql/routes), не видя ничего криминального идем перечитывать документацию express, не находим отличий с текущей реализацией. Смотрим на каждую строчку еще пристальнее. Ага, **опечатка**, вместо graphql написано graphgl. Смеемся с собственной невнимательности, идем попить чайку.

Во время чайной паузы в нас просыпается эстет и мы решаем, что проект, на который тратится больше получаса, не может существовать без config-файла. Добавляем config.js, в нем указываем порт для запуска и папку для статических файлов.

Давно хотел познакомится с GraphQL. Идем читать доки. Когда начинаем что-то понимать, переходим к тестированию запросов.


Ошибка №1 появляется при попытке получить Events. Идем в [./graphql/resolvers/query.js](./graphql/resolvers/query.js), фиксим **ошибку с передачей аргумента**, заодно форматируем код. Не вручную, конечно, просто жмем Ctrl+Shift+I в VS Code.

Теперь запрос events работает, но не полностью: **users и room - пустые значения**, хотя в БД они есть. Идем в [./graphql/resolvers/index.js](./graphql/resolvers/index.js), добавляем return'ы в Event-users и Event-room.

Ошибка №2 - не работает функция добавления User  в Event. Заходим в [graphql/resolvers/mutation.js] (graphql/resolvers/mutation.js), видим что там и **не описана эта функция**. Добавляем ее. Профит.

Ошибка №3 - функция changeEventRoom работает, но **не возвращает ничего в graphql sandbox**. Добавляем в функцию return event.

Ошибка №4 - **Не работает должным образом обновление** User, Event и Room, т.к. для обновления используются те же input types, что и для создания. Соответственно, чтобы обновить одно из полей, в input нужно указать все обязательные поля. Разделяем UserInput на UserCreateInput и UserUpdateInput, для других коллекций - аналогично.

Ошибка №5 - **ненужный тип UserRoom**. Выпиливаем его.

Ошибка №6 - **при создании User мы не можем добавить avatarUrl**, так как его нет в схеме. Добавляем.

На этом задание можно считать оконченным, т.к. все запросы проходят успешно.
В идеале в папку со скриптами нужно добавить serviceWorker.js, т.к. браузер пытается найти его в папке public, но не находит.Постараюсь добавить после выполнения следующих заданий.

**P.S.** 

Ошибка №7 - **при генерации данных из create-mock-data.js у последнего event неправильно указано время**, меняем местами значения dateStart и dateEnd.
Ошибка №8 - **в мутации setEvent вместо roonId комнаты устанавливается id**, заменяем id yf roomId.

В ходе третьего задания были добавлены методы addUsersToEvent и removeUsersFromEvent для добавления/удаления нескольких пользователей одновременно.

На этом задание можно считать оконченным, т.к. все запросы проходят успешно.
В идеале в папку со скриптами нужно добавить serviceWorker.js, т.к. браузер пытается найти его в папке public, но не находит.Постараюсь добавить после выполнения следующих заданий.

