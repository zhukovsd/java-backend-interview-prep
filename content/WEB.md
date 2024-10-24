## WEB

#### 1. REST
*REST* - это архитектурный стиль взаимодействия клиент-серверного WEB-приложения. Клиент-сервер общаются через протокол HTTP, а формат данных - JSON

#### 2. RESTful и RESTless
*RESTful* строго следует принципам REST, используя стандартные методы HTTP и четко структурированные URL, а *RESTless* нарушает эти принципы, делая взаимодействие менее стандартным и гибким

#### 3. REST vs SOAP
*SOAP* и *REST* – это два разных подхода к разработке API. Подход SOAP отличается высокой степенью структурированности и использует формат данных XML. REST более гибкий и позволяет приложениям обмениваться данными в нескольких форматах.

> На данный момент SOAP устаревший подход, использующийся на легаси проектах

#### 4. В каких ситуациях допустимо использование RESTless
Использование RESTless допустимо:
- В небольшом приложение, где строгие принципы REST не критичны
- Если существующая система уже использует другой подход и переделка будет слишком сложной

#### 5. REST vs RPC. Как выбрать что лучше подходит?
Для работы с данными это обычно всегда *REST*

Для работы с действиями, операциями(logout,login,signin) *RPC* будет смотреться лучше

*RPC* - все описывает глаголами, а *REST* - существительными

#### 6. Назовите 6 принципов REST API
- **Клиент-серверная архитектура**: Клиент и сервер работают независимо друг от друга. Клиент отправляет запросы на сервер, сервер их принимает и возвращает ответы. Это позволяет развиваться клиенту и серверу независимо друг от друга
- **Stateless (Отсутствие состояния)**: Сервер не сохраняет информацию о состоянии клиента между запросами. Информация о текущей сессии должна целиком храниться у клиента
- **Кеширование**: В REST API сервер может запомнить результаты частых запросов, чтобы быстрее их вернуть и не тратить ресурсы на повторные вычисления.
- **Единообразие интерфейса**: В API используются стандартные команды (GET, POST, PUT, DELETE), чтобы взаимодействие было простым и предсказуемым
- **Слои**: Система может быть разделена на несколько слоев, каждый из которых выполняет свою задачу
- **Код по требованию**: Иногда сервер может отправлять клиенту код (например, скрипты), который клиент может использовать для выполнения задач

#### 7. Идемпотентный метод
Это метод, который сколько раз ты не выполнишь принесёт один результат, не изменяющий состояние сервера
Идемпотентными считаются:

- GET
- PUT
- DELETE

Не идемпотентными считаются:

- POST
- PATCH

#### 8. Основные HTTP методы
- GET. Получение ресурса.
- POST. Создание нового ресурса.
- PUT. Полное обновление ресурса или создание, если его нет.
- PATCH. Частичное обновление ресурса.
- DELETE. Удаление ресурса.
- HEAD. Получение заголовка ресурса
- OPTIONS. Узнать доступные методы для ресурса

#### 9. Статусы ответов
- 100-199. Информационные
- 200-299. Успешные
- 300-399. Перенаправления
- 400-499. Клиентские ошибки
- 500-599. Серверные ошибки

#### 10. Почему для конфиденциальных данных рекомендуется использовать POST, а не GET запросы
В случае GET запроса передаваемые параметры являются частью url. В случае POST запроса передаваемые параметры являются частью тела запроса

#### 11. Разница HTTP и HTTPs
При использовании HTTPs - данные, передаваемые серверу - зашифрованы