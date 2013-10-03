# PSG-protocol (Platform shooter game protocol)

Протокол обмена сообщениями между клиентом и сервером

Сообщения передаются в формате JSON методом POST

## Формат сообщений

### Запрос (Request)

    {
        // Самое действие
        "action": "<action>",

        // Параметры, специфичные для конкретного действия
        "params": {
            "<paramName1>": <paramValue1>,
            ...
        }
    }

#### Пример

    {
        "action": "signup",
        "params": {
            "login": "admin",
            "password": "banana"
        }
    }


### Ответ (Response)

    {
        // В случае успешного выполнения команды "<result>" == "ok".
        // В случае ошибки передается ее код
        // Если в запросе несколько ошибок, например, badLogin и badPassword, в поле result передается любая из них
        // Если команда не найдена, передается код ошибки "unknownAction"
        "result": "<result>",

        // Текстовый комментарий. Может быть произвольным
        "message": "<message>"
    }

#### Пример ответа в случае успеха

    {
        "result": "ok"
    }

#### Пример ответа в случае ошибки

    {
        "result": "userExists",
        "message": "Такой пользователь уже существует"
    }


## Действия

### signup

Параметры:

* login => строковое значение.
    - минимальная длина: 4 символа
    - макисмальная длина: 40 символов
* password => строковое значение.
    - минимальная длина: 4 символа

Коды ошибок:

* userExists (Такой пользователь уже существует)
* badLogin (Некорректный логин)
* badPassword (Некорректный пароль)

### signin

Параметры:

* login => строковое значение
    - минимальная длина: 4 символа
    - макисмальная длина: 40 символов
* password => строковое значение
    - минимальная длина: 4 символа

Ответ:

* sid => латинские буквы и цифры, не короче 1 символа

Коды ошибок:

* incorrect (Некорректный логин/пароль)

### signout

Параметры:

* sid => латинские буквы и цифры, не короче 1 символа

Коды ошибок:

* badSid (Некорректный sid)

---

### sendMessage

Параметры:

* sid => латинские буквы и цифры, не короче 1 символа
* game => число, идентификатор игры. Пустая строка -- для общего чата
* text => текст сообщения, строковое значение

Коды ошибок:

* badSid (Некорректный sid)
* badGame (Некорректный идентификатор игры)

### getMessages

Параметры:

* sid => латинские буквы и цифры, не короче 1 символа
* game => число, идентификатор игры. Пустая строка -- для общего чата
* since => Unix timestamp UTC

Ответ:

* messages => массив, отсортированный *по возрастанию* времени

Элемент массива messages:

    {
        // Время в формате Unix timestamp UTC
        "time": <time>,

        // Текст сообщения
        "text": "<text>",

        // Логин отправителя
        "login": "<login>"
    }

    - time => Unix timestamp UTC
    - text => строковое значение
    - login => строковое значение
        + минимальная длина: 4 символа
        + максимальная длина: 40 символов

Коды ошибок:

* badSid (Некорректный sid)
* badGame (Некорректный идентификатор игры)
* badSince (Некорректное время)

---

### createGame

Параметры:

* sid => латинские буквы и цифры, не короче 1 символа
* name => название игры, строковое значение, не короче одного символа
* map => название карты, строковое значение
* maxPlayers => максимально допустимое число игроков

Коды ошибок:

* badSid (Некорректный sid)
* badName (Некорректное название игры)
* badMap (Некорректное название игры)
* badMaxPlayers (Некорректное максимальное число игроков)
* gameExists (Такая игра уже существует)
* alreadyInGame (Пользователь уже в игре)

### getGames

Параметры:

* sid

Ответ:

* games => массив игр

Элемент массива games:

    {
        // Число, идентификатор игры
        id: "<id>",

        // Строка, название игры
        name: "<name>",

        // Строка, название карты
        map: "<map>",

        // Число, макисмальное количество игроков
        maxPlayers: <maxPlayers>,

        // Массив логинов игроков, отсортированный в порядке подключения
        players: ["<login1>", "<login2>", ... ],

        // Строка, состояние игры. Допустимые значения: running, finished
        status: "<status>"
    }

Коды ошибок:

* badSid (Некорректный sid)

### joinGame

Параметры:

* sid => латинские буквы и цифры, не короче 1 символа
* game => число, идентификатор игры

Коды ошибок:

* badSid (Некоректный sid)
* gameFull (Игра заполнена)
* badGame (Некорректный идентификатор игры)
* alreadyInGame (Пользователь уже в игре)

### leaveGame

Параметры:

* sid => латинские буквы и цифры, не короче 1 символа

Коды ошибок:

* notInGame (Игрок не участвует ни в одной игре)

### uploadMap

Параметры:

* name => название игры, строковое значение
* map: ["<row1>", "<row2>", ...] - массив строк, описывающих карту. Длины строк должны быть равны

// Пока что заглушка из-за недоговоренности
