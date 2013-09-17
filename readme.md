# PSG-protocol (Platform shooter game protocol)

Протокол обмена сообщениями между клиентом и сервером

Сообщения передаются в формате JSON

## Формат сообщений

### Запрос (Request)

    {
        // Самое действие
        "action": "<action>",

        // Параметры, специфичные для конкретного запроса
        <data>
    }

### Ответ (Response)

    {
        // В случае успешного выполнения команды "<result>" == "ok".
        // Ответы в случаях ошибок описаны отдельно для каждого запроса
        "result": "<result>",

        // Код ошибки
        "error": "<error>"

        // Текстовый комментарий. Может быть произвольным
        "message": "<message>",

        // Данные, специфичные для конкретного действия
        <data>
    }


## Действия

### signup

Параметры:

* login => строковое значение
* password => строковое значение

Коды ошибок:

* userExists (Такой пользователь уже существует)
* badLogin (Некорректный логин)
* badPassword (Некорректный пароль)

### signin

Параметры:

* login => строковое значение
* password => строковое значение

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

* messages => массив, отсортированный по убыванию времени

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
