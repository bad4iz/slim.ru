---
title: Руководство по обновлению
---

Если вы обновляетесь с версии 2 до версии 3, это существенные изменения, о которых вам нужно знать.

## Новая версия PHP
Для Slim 3 требуется PHP 5.5+

## Класс \ Slim \ Slim переименован \ Slim \ App
Slim 3 использует `\Slim\App` для [Application](/docs/objects/application.html) объект обычно называется `$app`.

<figure class="highlight"><pre><code class="language-php" data-lang="php">$app = new \Slim\App();</code></pre></figure>

## Подпись новой функции маршрута

<figure class="highlight">
<pre>
    <code class="language-php" data-lang="php">$app-&gt;get('/', function (Request $req, Response $res, $args = []) {
        return $res-&gt;withStatus(400)-&gt;write('Bad Request');
    });</code>
</pre>
</figure>

## Объекты запроса и ответа больше не доступны через объект приложения
Как упоминалось выше, Slim 3 передает объекты `Request` и `Response` объекты в качестве аргументов функции обработки 
маршрута. Поскольку они теперь доступны непосредственно в теле функции маршрута `request` и `response` больше не 
являются объектами экземпляра `/Slim/App` ([Application](/docs/objects/application.html) object).

## Получение переменных _GET и _POST
<figure class="highlight"><pre>
<code class="language-php" data-lang="php">$app-&gt;get('/', function (Request $req,  Response $res, $args = []) {
    $myvar1 = $req-&gt;getParam('myvar'); //checks both _GET and _POST [NOT PSR-7 Compliant]
    $myvar2 = $req-&gt;getParsedBody()['myvar']; //checks _POST  [IS PSR-7 compliant]
    $myvar3 = $req-&gt;getQueryParams()['myvar']; //checks _GET [IS PSR-7 compliant]
});</code></pre>
</figure>

## Хуки
Хуки больше не являются частью Slim по сравнению с v3. Вы должны рассмотреть реализовав какие - либо функциональные 
возможности, связанные с [default hooks in Slim v2](http://docs.slimframework.com/hooks/defaults/) как
 [middleware](/docs/concepts/middleware.html) вместо. Если вам нужна возможность применять пользовательские Хуки 
 в произвольных точках вашего кода (например, в пределах route),вам следует рассмотреть сторонний пакет, например
  [Symfony's EventDispatcher](http://symfony.com/doc/current/components/event_dispatcher/introduction.html) или
   [Zend Framework's EventManager](https://zend-eventmanager.readthedocs.org/en/latest/).

## Удаление HTTP-кеша
В Slim v3 мы удалили HTTP-кэширование в свой собственный модуль [Slim\Http\Cache](https://github.com/slimphp/Slim-HttpCache).

## Удаление остановки/остановки `Stop/Halt`
Slim Core удалил Stop / Halt. В ваших приложениях вы должны перейти к использованию методов withStatus () и withBody ().

## Удаление автозагрузчика
`Slim::registerAutoloader()` были удалены, мы полностью перешли к композитору.

## Изменения в контейнере
`$app->container->singleton(...)` теперь `$container = $app->getContainer(); $container['...'] = function () {};`
 читайте в Pimple docs для получения дополнительной информации

## Удаление configureMode()
`$app->configureMode(...)` был удален в v3.

## Удаление PrettyExceptions
PrettyExceptions вызывают множество проблем для многих людей, поэтому они были удалены.

## Route::setDefaultConditions(...) удален
Мы включили маршрутизаторы, которые позволяют вам сохранять регулярные выражения по умолчанию внутри шаблона маршрута.

## Изменения в перенаправлении
В Slim v2.x можно использовать вспомогательную функцию `$app->redirect();` для запуска запроса перенаправления.
В Slim v3.x можно сделать то же самое с использованием класса Response.

Пример:

<figure class="highlight"><pre><code class="language-php" data-lang="php">$app-&gt;get('/', function ($req, $res, $args) {
  return $res-&gt;withStatus(302)-&gt;withHeader('Location', 'your-new-uri');
});</code></pre></figure>

## Подпись `Middleware` промежуточного ПО 
Подпись middleware изменилась с класса на функцию.

Новая подпись:

<figure class="highlight"><pre><code class="language-php" data-lang="php">use Psr\Http\Message\RequestInterface as Request;
use Psr\Http\Message\ResponseInterface as Response;

$app-&gt;add(function (Request $req,  Response $res, callable $next) {
    // Do stuff before passing along
    $newResponse = $next($req, $res);
    // Do stuff after route is rendered
    return $newResponse; // continue
});</code></pre></figure>

Вы все равно можете использовать класс:

<figure class="highlight"><pre><code class="language-php" data-lang="php">namespace My;

use Psr\Http\Message\RequestInterface as Request;
use Psr\Http\Message\ResponseInterface as Response;

class Middleware
{
    function __invoke(Request $req,  Response $res, callable $next) {
        // Do stuff before passing along
        $newResponse = $next($req, $res);
        // Do stuff after route is rendered
        return $newResponse; // continue
    }
}


// Register
$app-&gt;add(new My\Middleware());
// or
$app-&gt;add(My\Middleware::class);</code></pre></figure>


## Выполнение Middleware
Приложение middleware выполняется как Last In First Executed (LIFE).

## Flash-сообщения 
Flash messages are no longer a part of the Slim v3 core but instead have been moved to seperate [Slim Flash](/docs/features/flash.html) package.

## Cookies
In v3.0 cookies has been removed from core. See [FIG Cookies](https://github.com/dflydev/dflydev-fig-cookies) for a PSR-7 compatible cookie component.

## Removal of Crypto
In v3.0 we have removed the dependency for crypto in core.

## New Router
Slim now utilizes [FastRoute](https://github.com/nikic/FastRoute), a new, more powerful router!

This means that the specification of route patterns has changed with named parameters now in braces and square brackets used for optional segments:

{% highlight php %}
// named parameter:
$app->get('/hello/{name}', /*...*/);

// optional segment:
$app->get('/news[/{year}]', /*...*/);
{% endhighlight %}

## Route Middleware
The syntax for adding route middleware has changed slightly.
In v3.0:

{% highlight php %}
$app->get(…)->add($mw2)->add($mw1);
{% endhighlight %}

## Getting the current route
The route is an attribute of the Request object in v3.0:

{% highlight php %}
$request->getAttribute('route');
{% endhighlight %}

When getting the current route in middleware, the value for
`determineRouteBeforeAppMiddleware` must be set to `true` in the Application
configuration, otherwise the getAttribute call returns `null`.

## urlFor() is now pathFor() in the router

`urlFor()` has been renamed `pathFor()` and can be found in the `router` object:

{% highlight php %}
$app->get('/', function ($request, $response, $args) {
    $url = $this->router->pathFor('home');
    $response->write("<a href='$url'>Home</a>");
    return $response;
})->setName('home');
{% endhighlight %}

Also, `pathFor()` is base path aware.

## Container and DI ... Constructing
Slim uses Pimple as a Dependency Injection Container.

{% highlight php %}

// index.php
$app = new Slim\App(
    new \Slim\Container(
        include '../config/container.config.php'
    )
);

// Slim will grab the Home class from the container defined below and execute its index method.
// If the class is not defined in the container Slim will still contruct it and pass the container as the first arugment to the constructor!
$app->get('/', Home::class . ':index');


// In container.config.php
// We are using the SlimTwig here
return [
    'settings' => [
        'viewTemplatesDirectory' => '../templates',
    ],
    'twig' => [
        'title' => '',
        'description' => '',
        'author' => ''
    ],
    'view' => function ($c) {
        $view = new Twig(
            $c['settings']['viewTemplatesDirectory'],
            [
                'cache' => false // '../cache'
            ]
        );

        // Instantiate and add Slim specific extension
        $view->addExtension(
            new TwigExtension(
                $c['router'],
                $c['request']->getUri()
            )
        );

        foreach ($c['twig'] as $name => $value) {
            $view->getEnvironment()->addGlobal($name, $value);
        }

        return $view;
    },
    Home::class => function ($c) {
        return new Home($c['view']);
    }
];

{% endhighlight %}

## PSR-7 Objects

### Request, Response, Uri & UploadFile are immutable.
This means that when you change one of these objects, the old instance is not updated.

{% highlight php %}
// This is WRONG. The change will not pass through.
$app->add(function (Request $request, Response $response, $next) {
    $request->withAttribute('abc', 'def');
    return $next($request, $response);
});

// This is correct.
$app->add(function (Request $request, Response $response, $next) {
    $request = $request->withAttribute('abc', 'def');
    return $next($request, $response);
});
{% endhighlight %}

### Message bodies are streams

{% highlight php %}
// ...
$image = __DIR__ . '/huge_photo.jpg';
$body = new Stream($image);
$response = (new Response())
     ->withStatus(200, 'OK')
     ->withHeader('Content-Type', 'image/jpeg')
     ->withHeader('Content-Length', filesize($image))
     ->withBody($body);
// ...
{% endhighlight %}

For text:
{% highlight php %}
// ...
$response = (new Response())->getBody()->write('Hello world!')

// Or Slim specific: Not PSR-7 compliant.
$response = (new Response())->write('Hello world!');
// ...
{% endhighlight %}
