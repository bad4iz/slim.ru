---
title: Приложение
---

Приложение, (или `Slim\App`) является точкой входа в ваше Slim-приложение и используется для 
регистрации маршрутов, которые ссылаются на ваши обратные вызовы или контроллеры.

```php
// instantiate the App object
$app = new \Slim\App();

// Add route callbacks
$app->get('/', function ($request, $response, $args) {
    return $response->withStatus(200)->write('Hello World!');
});

// Run application
$app->run();
```

## Конфигурация приложения

Приложение принимает только один аргумент. Это может быть либо экземпляр [контейнера](/docs/concepts/di.html) 
либо массив для настройки контейнера по умолчанию, который создается автоматически.

Также есть ряд настроек, которые используются Slim. Они сохраняются в `settings`
ключе конфигурации. Вы также можете добавить свои настройки для конкретного приложения.

Например, мы можем установить для параметра Slim значение `displayErrorDetails` true, а также настроить 
Monolog следующим образом:

```php
$config = [
    'settings' => [
        'displayErrorDetails' => true,

        'logger' => [
            'name' => 'slim-app',
            'level' => Monolog\Logger::DEBUG,
            'path' => __DIR__ . '/../logs/app.log',
        ],
    ],
];
$app = new \Slim\App($config);
```


### Получение настроек

Поскольку настройки хранятся в контейнере DI, вы можете получить к ним доступ через `settings` ключ в 
контейнерах. Например:

```php
$loggerSettings = $container->get('settings')['logger'];
```

Вы также можете получить к ним доступ в маршрутных вызовах через `$this`:

```php
$app->get('/', function ($request, $response, $args) {
    $loggerSettings = $this->get('settings')['logger'];
    // ...
});
```

### Обновление настроек

Если вам нужно добавить или обновить настройки, сохраненные в контейнере DI *после* инициализации 
контейнера, вы можете использовать этот `replace` метод в контейнере настроек. Например:

```php
$settings = $container->get('settings');
$settings->replace([
        'displayErrorDetails' => true,
        'determineRouteBeforeAppMiddleware' => true,
        'debug' => true
    ]);
]);
```

## Slim настройки по умолчанию

Slim имеет следующие настройки по умолчанию, которые вы можете переопределить:

<dl>
<dt>httpVersion</dt>
    <dd>Версия протокола, используемая объектом <a href="/docs/objects/response.html">Response</a>
        object.
        <br>(По умолчанию: `1.1`)</dd>
<dt>>responseChunkSize</dt>
    <dd>Размер каждого фрагмента, считываемого из тела ответа при отправке в браузер.
        <br>(По умолчанию: 4096)</dd>
<dt>>outputBuffering</dt>
    <dd>Если false, то буферизация вывода не включена. Если `append` или `prepend`, то любые echo или print 
    утверждения захватываются и добавляются или добавляются к Ответу, возвращенному из маршрута, который можно вызывать. 
        <br>(По умолчанию: >`append`)</dd>
<dt>>determineRouteBeforeAppMiddleware</dt>
    <dd>Когда true, маршрут вычисляется до выполнения любого промежуточного программного обеспечения. 
    Это означает, что вы можете проверить параметры маршрута в промежуточном программном обеспечении, если вам нужно. 
    <br>(По умолчанию: `false`)</dd>
<dt>>displayErrorDetails</dt>
    <dd>Когда true, дополнительная информация об исключениях отображается 
    [обработчиком ошибок по умолчанию](/docs/handlers/error).
    <br>(По умолчанию: false)</dd>
<dt>>addContentLengthHeader</dt>
    <dd>Когда true, Slim добавит Content-Length заголовок к ответу. Если вы используете средство 
    аналитики времени исполнения, например New Relic, то это должно быть отключено. 
    <br>(По умолчанию: true)</dd>
<dt>>routerCacheFile</dt>
    <dd>Имя файла для кэширования маршрутов FastRoute. Должно быть установлено допустимое имя файла в 
    каталоге, пригодном для записи. Если файл не существует, он создается с правильной информацией о 
    кеше при первом запуске. <br>
    Установите для `false` отключения системы кэширования `FastRoute`. 
    <br>(По умолчанию: false)</dd>
</dl>
