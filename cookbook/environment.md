---
title: Getting и Mocking окружение
---

Объект Environment инкапсулирует `$_SERVER` суперглобальный массив и отделяет приложение Slim от глобальной среды PHP. 
Развязка приложения Slim из глобальной среды PHP позволяет нам создавать HTTP-запросы, которые могут (или не могут) 
представлять глобальную среду. Это особенно полезно для модульного тестирования и инициирования вспомогательных запросов. 
Вы можете получить текущий объект Environment в любом месте вашего Slim-приложения следующим образом:

```php
$container = $app->getContainer();
$environment = $container['environment'];
```

## Свойства среды
Каждое приложение Slim имеет объект среды с различными свойствами, которые определяют поведение приложения. 
Многие из этих свойств отражают найденные в `$_SERVER` суперглобальном массиве. Требуются некоторые свойства. 
Другие свойства являются необязательными.

### Обязательные свойства

REQUEST_METHOD
:   метод запроса HTTP. Это должно быть одно из «GET», «POST», «PUT», «DELETE», «HEAD», «PATCH» или «OPTIONS».

SCRIPT_NAME
:   Абсолютное имя пути к PHP-скрипту front-controller относительно вашего корня документа, без учета любой перезаписи URL, выполняемой вашим веб-сервером.

REQUEST_URI
:   абсолютное имя пути URI-запроса HTTP, включая любые изменения перезаписи URL-адресов, выполняемые вашим веб-сервером.

QUERY_STRING
:    часть пути URI HTTP-запроса после, но не включая, «?». Это может быть пустая строка, если текущий HTTP-запрос не указывает строку запроса.

SERVER_NAME
:   имя хоста сервера, под которым выполняется текущий скрипт. Если скрипт запущен на виртуальном хосте, это будет значение, определенное для этого виртуального хоста.

SERVER_PORT
:   порт на сервере, используемый веб-сервером для связи. Для настроек по умолчанию это будет «80»; используя SSL, например, изменит это на любой ваш защищенный HTTP-порт.

HTTPS
:   установить непустое значение, если скрипт был запрошен через протокол HTTPS.

### Дополнительные свойства

CONTENT_TYPE
:   тип содержимого запроса HTTP (например, `application/json;charset=utf8`)

CONTENT_LENGTH
:   длина содержимого запроса HTTP. Это должно быть целое число, если оно присутствует.

HTTP_*
:   заголовки HTTP-запросов, отправленные клиентом. Эти значения идентичны их аналогам в `$_SERVER` суперглобальной матрице. Если они присутствуют, эти значения должны содержать префикс «HTTP_».  

PHP_AUTH_USER
:   `Authentication` расшифрованное имя пользователя заголовка HTTP .

PHP_AUTH_PW
:   `Authentication` расшифрованный пароль HTTP- заголовка.

PHP_AUTH_DIGEST
:   необработанный HTTP- `Authentication` заголовок, отправленный клиентом HTTP.

AUTH_TYPE
:   `Authentication` тип аутентификации HTTP- заголовка (например, «Basic» или «Digest»).

slim.files
:   массив объектов, реализующих интерфейс `\Psr\Http\Message\UploadedFileInterface` 
    (например, нативный для Slim Framework `\Slim\Http\UploadedFile`)
                              

## Макетная среда

Каждое Slim-приложение создает экземпляр объекта Environment, используя информацию из текущей глобальной среды. 
Тем не менее, вы также можете создавать объекты mock environment с настраиваемой информацией. Объекты Mock Environment 
полезны только при написании модульных тестов.

```php
$env = \Slim\Http\Environment::mock([
    'REQUEST_METHOD' => 'POST',
    'REQUEST_URI' => '/foo/bar',
    'QUERY_STRING' => 'abc=123&foo=bar',
    'SERVER_NAME' => 'example.com',
    'CONTENT_TYPE' => 'multipart/form-data',
    'slim.files' => [
        'field1' => new UploadedFile('/path/to/file1', 'filename1.txt', 'text/plain', filesize('/path/to/file1')),
        'field2' => new UploadedFile('/path/to/file2', 'filename2.txt', 'text/plain', filesize('/path/to/file2')),
    ],
]);
```