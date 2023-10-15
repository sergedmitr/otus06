#Otus 06
Backend for frontends. Apigateway
#Результат выполнения задания
Студент: Дмитриев С.А. Группа OTUS MicroserviceArchitecture-2023-04

##Описание
В качестве Apigateway использован Istio.
Данные об аутентификации храню в памяти в сервисе аутентификации.
Карточки клиентов в проиложении из ДЗ 3. Приложение пришлось доработать, чтобы оно работало не по Id, а по username клиента. Поэтому карточка клиента создается отдельно от регистрации. 
Схема работы системы в изображении Authorization schema.png
1. Istio маршрутизирует запросы на auth-service и my-wsdb-service. Эндпоинты auth-service /work/login и /work/logout исключены из проверки авторизации.
2. При поступлении запроса на my-wsdb-service, Istio обращается с кукой в auth-service /auth/ и если сессия в куке хорошая, то возвращает заголовок x-username со значением username из сессии.
3. Если куки нет или сессии нет, то Istio вернёт клиенту 401 ошибку.
4. Дальше sidecar istio-proxy на основании запрошенного пути и значение заголовка x-username примет решение о предоставлении данных клиенту. Сейчас условие, что путь должен содержать x-username.
5. Иначе Istio вернёт 403 ошибку.

Процесс выполнения:
1. Установил Istio в неймспейс istio-system.
2. Установил PostgreSql в неймспейс zipper
helm install postgresql-test oci://registry-1.docker.io/bitnamicharts/postgresql --set auth.database=mydb,auth.postgresPassword=secretpassword -n zipper
3. Написал свой сервис авторизации и аутентификации на Java (auth-work)
4. Уcтановил auth-work в неймспейс granite
5. Установил приложение БД my-wsdb-service с помощью Helm в неймспейс zipper.
6. Сделал настройки Istio для маршрутизации, аутентификации и авторизации.
7. Сделал коллекцию postman для проверки предложенного сценария (Otus-Auth-Check.json)
