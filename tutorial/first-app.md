---
title: Первое пошаговое руководство
---

Если вы ищете гид по всем компонентам для создания очень простого приложения Slim  
(тут нет Twig, но есть Monolog и подключение к базе данных PDO), то вы в нужном месте. Пройдитесь через учебник, 
чтобы создать пример приложения, или адаптируйте каждый шаг для своих нужд.

Прежде чем вы начнете: существует также [skeleton project](https://github.com/slimphp/Slim-Skeleton) 
который даст вам быстрый старт для примера приложения, поэтому используйте его, если вы предпочитаете просто 
что-то сделать, а не изучать, как работают все компоненты.

> В этом учебном пособии рассматривается пример приложения. [Код приложения](https://github.com/slimphp/Tutorial-First-Application), если вы хотите , чтобы обратиться к нему.

## Настройка

Начните с создания папки для вашего проекта (у меня называется `project`, потому что именовать вещи сложно). 
Мне нравится резервировать верхний уровень для вещей, которые есть не-код, а затем есть папка для исходного кода и 
папка внутри того, что является моим webroot, поэтому моя первоначальная структура выглядит следующим образом:

```
.
├── project
│   └── src
│       └── public
```

### Установка Slim Framework

[Composer](https://getcomposer.org) лучший способ установить Slim Framework. 
Если у вас его нет, вы можете воспользоваться [инструкцией по установке](https://getcomposer.org/download/), 
в моем проекте я только что загрузил `composer.phar` в свой `src/` каталог, и я буду использовать его локально.
  Итак, моя первая команда выглядит так (Я в  `src/` каталоге):

```
    php composer.phar require slim/slim
```

Это сделает две вещи:

* Добавьте зависимость Slim Framework в `composer.json` (в моем случае он создает файл для меня, 
поскольку у меня его еще нет, безопасно запускать его, если у вас уже есть `composer.json` файл )
* Выполните `composer install` так, чтобы эти зависимости действительно были доступны для использования в вашем приложении

Если вы сейчас заглянете в каталог проекта, вы увидите, что у вас есть `vendor/` папка со всем кодом библиотеки. 
Также есть два новых файла: `composer.json` и `composer.lock`. Это самое лучшее время, чтобы настроить 
системы управления версиями, а также: при работе с composer,  всегда исключать `vendor/` каталог, 
но два `composer.json` И `composer.lock` должны быть включены в систему контроля версиями. Поскольку я использую 
`composer.phar` в этом каталогк я собираюсь включить его также в свое репо; чтобы вы могли использовать `composer` 
команды на всех системай в которых надо.

Чтобы правильно настроить игнорирование в git , создайте файл `src/.gitignore` и добавте следующую стоку в файл.

    vendor/*


Теперь git не предложит вам добавить файлы `vendor/` в репозиторий - потому что мы позволяем 
composer управлять этими зависимостями, а не включать их в наш репозиторий управления версиями.

### Создание Приложения

На [project homepage](http://www.slimframework.com) есть отличный и минимальный пример, 
поэтому мы будем использовать этот пример как отправную точку `index.php` для Slim Framework. 
Поместите следующий код в `src/public/index.php`:
``` php
<?php
use \Psr\Http\Message\ServerRequestInterface as Request;
use \Psr\Http\Message\ResponseInterface as Response;

require '../vendor/autoload.php';

$app = new \Slim\App;
$app->get('/hello/{name}', function (Request $request, Response $response) {
    $name = $request->getAttribute('name');
    $response->getBody()->write("Hello, $name");

    return $response;
});
$app->run();
``` 
Мы просто вставили загрузку кода ... давайте посмотрим, что он делает.

В `use` объявление в верхней части скрипта только подключения `Request` и `Response` в наш скрипт, 
и мы не должны обращаться к ним по их многословным именам. Slim framework поддерживает PSR-7, который является стандартом 
PHP для обмена сообщениями HTTP, поэтому вы заметите, что при создании своего приложения вы часто и часто видите 
`Request` и `Response` объекты. Это современный и отличный подход к написанию веб-приложений.

Затем мы подключаем `vendor/autoload.php` файл - файл созданый Composer. Он позволит ссылаться на Slim  
и другие связанные зависимости которые мы установили ранее. Обратите внимание: если вы используете ту же структуру 
файлов, что и я, то `vendor/` каталог находится в родительском уровне от вашего, `index.php` и вам, возможно, придется 
настроить путь, как я сделал выше.

Наконец, мы создаем `$app` бъект, который является началом Slim goodness. 
`$app->get()` Вызов наш первый "route" - когда мы делаем запрос GET на `/hello/someone` 
то это код , который ответит на него. **Не забывайте** вам нужна эта заключительная `$app->run()` 
строка, чтобы рассказать Slim, что мы закончили настройку, и пришло время заняться основным событием.

Теперь у нас есть приложение, нам нужно его запустить. Я расскажу о двух вариантах: встроенном веб-сервере 
PHP и настройке виртуального хоста Apache.

### Запуск приложения с веб-сервером PHP

Это мой предпочтительный вариант «быстрого старта», потому что он не полагается ни на что другое! Из `src/public` 
каталого запустите команду:

    php -S localhost:8080

Это сделает ваше приложение доступным по адресу [http://localhost:8080](http://localhost:8080) 
(если вы уже используете порт 8080 на своем компьютере, вы получите предупреждение. Просто выберите другой номер порта).

**Обратите внимание** вы получите сообщение об ошибке "Page Not Found" по этому URL-адресу - 
но это сообщение об ошибке **от Slim**, так что это ожидается.  Попробуйте 
[http://localhost:8080/hello/joebloggs](http://localhost:8080/hello/joebloggs) вместо этого :)

### Запустите приложение с помощью Apache или nginx

Чтобы получить эту настройку в стандартном стеке LAMP, нам понадобится пара дополнительных компонентов: 
некоторая конфигурация виртуального хоста и одно правило перезаписи.

Конфигурация vhost должна быть довольно простой; нам здесь ничего не нужно. Скопируйте существующую конфигурацию 
vhost по умолчанию и укажите, `ServerName` как вы хотите ссылаться на свой проект. Например, вы можете установить:

    ServerName slimproject.dev

    or for nginx:

    server_name slimproject.dev;

Затем вы также захотите установить `DocumentRoot` чтобы указать `public/` каталог вашего проекта, что-то 
вроде этого (отредактируйте существующую строку):

    DocumentRoot    /home/lorna/projects/slim/project/src/public/

    or for nginx:

    root    /home/lorna/projects/slim/project/src/public/


**Не забудьте** перезапустить сервер, ведь вы изменили конфигурацию!

У меня также есть `.htaccess` файл в моем `src/public` каталоге; это зависит от того, включен ли модуль 
перезаписи Apache и просто делает все веб-запросы доступными к `index.php`, чтобы Slim мог обрабатывать всю 
маршрутизацию для нас. Вот мой `.htaccess` файл:

```
RewriteEngine on
RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule . index.php [L]
```

nginx не использует `.htaccess` файлы, поэтому вам нужно добавить следующее в конфигурацию вашего сервера 
в `location` блоке:

```
if (!-e $request_filename){
    rewrite ^(.*)$ /index.php break;
}
```

**ПРИМЕЧАНИЕ:** Если вы хотите, чтобы ваша точка входа была чем-то иным, чем index.php, вам также понадобится 
изменить вашу конфигурацию.
`api.php` также обычно используется в качестве точки входа, поэтому ваша настройка должна соответствовать 
соответствующим образом. В этом примере предполагается, что вы используете `index.php.`

С помощью этой настройки просто не забудьте использовать http://slimproject.dev вместо http: // localhost: 8080 в 
других примерах этого урока. Такое же предупреждение о работоспособности, как указано выше: вы увидите страницу с 
ошибкой на странице http://slimproject.dev, но на самом деле это страница с ошибкой *Slim's* . Если вы перейдете к 
http://slimproject.dev/hello/joebloggs, тогда должно произойти что-то лучше.

## Конфигурация и автозагрузчики

Теперь мы создали платформу, мы можем начать получать все, что нам нужно, в самом приложении.

### Добавить параметры конфигурации в приложение

В первом примере используются все значения по умолчанию Slim, но мы можем легко добавить конфигурацию в наше 
приложение, когда мы его создадим. Есть несколько вариантов, но здесь я только что создал массив опций конфигурации, 
а затем сказал Slim, чтобы использовать его настройки здесь, когда я его создаю.

Сначала сама конфигурация:

``` php
$config['displayErrorDetails'] = true;
$config['addContentLengthHeader'] = false;

$config['db']['host']   = "localhost";
$config['db']['user']   = "user";
$config['db']['pass']   = "password";
$config['db']['dbname'] = "exampleapp";
``` 


Первая строка - самая важная! Включите это в режиме разработки, чтобы получить информацию об ошибках 
(без этого, Slim будет, по крайней мере, регистрировать ошибки, поэтому, если вы используете встроенный веб-сервер 
PHP, тогда вы увидите их на консольном выходе, который будет полезен). Вторая строка позволяет веб-серверу 
устанавливать заголовок Content-Length, который делает Slim более предсказуемым.

Другие настройки здесь не являются конкретными ключами/значениями, это всего лишь некоторые данные, 
которые я хочу получить позже.

Теперь, чтобы передать это в Slim, нам нужно *изменить*, где мы создаем`Slim/App` 
объект, чтобы он теперь выглядел следующим образом:


``` php
$app = new \Slim\App(["settings" => $config]);
``` 
Теперь мы сможем получить доступ к любым настройкам, которые мы помещаем в этот `$config` массив из нашего приложения.

## Настройка автозагрузки для собственных классов

Composer может обрабатывать автозагрузку ваших собственных классов так же хорошо, как и имеющиеся. 
Для углубленного руководства взгляните на 
[использование Composer для управления правилами автозагрузки](https://getcomposer.org/doc/04-schema.md#autoload).

Моя настройка довольно проста, так как у меня есть только несколько дополнительных классов, они просто находятся 
в глобальном пространстве имен, а файлы находятся в  
`src/classes/` каталоге. Поэтому, чтобы загрузить их, я добавлю этот `autoload` раздел в свой  `composer.json` файл:


``` php
{
    "require": {
        "slim/slim": "^3.1",
        "slim/php-view": "^2.0",
        "monolog/monolog": "^1.17",
        "robmorgan/phinx": "^0.5.1"
    },
    "autoload": {
        "psr-4": {
            "": "classes/"
        }
    }
}
``` 

## Добавление зависимостей

Большинство приложений будут иметь некоторые зависимости, а Slim прекрасно их имитирует, используя DIC 
(Container Injection Container), построенный на [Pimple](http://pimple.sensiolabs.org/). 
В этом примере будут использоваться как [Monolog](https://github.com/Seldaek/monolog) так и 
[PDO](http://php.net/manual/en/book.pdo.php) соединение с MySQL.

Идея контейнера для инъекций зависимостей заключается в том, что вы настраиваете контейнер для загрузки 
зависимостей, которые необходимы вашему приложению, когда они им нужны. После того, как DIC создал / собрал 
зависимости, он сохраняет их и может предоставить их позже, если это необходимо.

Чтобы получить контейнер, мы можем добавить следующее после строки, в которой мы создаем, `$app` и до того, 
как мы начнем регистрировать маршруты в нашем приложении:


``` php
$container = $app->getContainer();
``` 
Теперь у нас есть `Slim\Container` объект, мы можем добавить к нему наши сервисы.

### использование Monolog в вашем приложении

Если вы еще не знакомы с Monolog, это отличный framework логирования для PHP-приложений, поэтому собираюсь использовать 
его здесь. Во-первых, подключите библиотеку Monolog через composer:

    php composer.phar require monolog/monolog

Зависимость называется `logger`, а код для добавления выглядит следующим образом:


``` php
$container['logger'] = function($c) {
    $logger = new \Monolog\Logger('my_logger');
    $file_handler = new \Monolog\Handler\StreamHandler("../logs/app.log");
    $logger->pushHandler($file_handler);
    return $logger;
};
``` 

Мы добавляем элемент в контейнер, который сам является анонимной функцией 
(`$c` параметр передается в самом контейнере, поэтому вы можете использовать другие зависимости, если это вам нужно).  
Это будет вызвано, когда мы попытаемся получить доступ к этой зависимости в первый раз; здесь код устанавливает 
настройку зависимости. В следующий раз, когда мы попытаемся получить доступ к той же зависимости, тот же самый 
объект, который был создан в первый раз, будет использоваться в следующий раз.

Мой Monolog конфиг здесь довольно прозрачен, только настройки приложения для регистрации всех ошибок в файл 
`logs/app.log` (помните, это путь с точки зрения того, где работает скрипт, в нашем случае это `index.php`).

С помощью logger на месте я могу использовать его изнутри кода роута с помощью строки, подобной этой:

``` php
 $this->logger->addInfo("Something interesting happened");
``` 

Хорошее логирование приложения - действительно важная основа для любого приложения, поэтому я всегда рекомендую 
поместить что-то подобное на место. Это позволяет вам добавить столько или меньше отладки, сколько захотите, и с 
помощью соответствующих уровней журнала с каждым сообщением вы можете иметь столько же или немного деталей, сколько 
подходит для того, что вы делаете в любой момент.

### Добавить соединение с базой данных

Существует множество библиотек баз данных, доступных для PHP, но в этом примере используется PDO - это доступно в PHP 
в качестве стандарта, поэтому оно, вероятно, полезно в каждом проекте или вы можете использовать свои собственные 
библиотеки, адаптировав приведенные ниже примеры.

Точно так же, как мы сделали для добавления Monolog to the DIC, мы добавим анонимную функцию, которая 
устанавливает зависимость, в этом случае называется `db`:

``` php
$container['db'] = function ($c) {
    $db = $c['settings']['db'];
    $pdo = new PDO("mysql:host=" . $db['host'] . ";dbname=" . $db['dbname'],
        $db['user'], $db['pass']);
    $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
    $pdo->setAttribute(PDO::ATTR_DEFAULT_FETCH_MODE, PDO::FETCH_ASSOC);
    return $pdo;
};
``` 


Помните конфиг, который мы добавили в наше приложение раньше? 
Ну, вот где мы его используем - контейнер знает, как получить доступ к нашим настройкам, и поэтому мы можем легко 
получить нашу конфигурацию отсюда. С помощью config мы создаем `PDO` объект 
(помните, что это приведет к отказу, `PDOException` если он сработает, и вы можете обработать это здесь) 
чтобы мы могли подключиться к базе данных. Я включил два `setAttribute()` вызова, которые действительно не нужны, 
но я нахожу, что эти два параметра делают PDO более удобным для использования в качестве библиотеки, поэтому я 
оставил настройки в этом примере, чтобы вы могли их использовать! Наконец, мы возвращаем наш объект соединения.

Опять, мы можем получить доступ к нашим зависимостям с помощью только, `$this->` и затем имя требуемой зависимости, 
которая в этом случае есть `$this->db`, по этому в моем случае приложении есть код, который выглядит примерно так:

``` php
 $mapper = new TicketMapper($this->db);
``` 

Это приведет к `db` dependency ависимости от DIC, создав его, если это необходимо, и в этом примере просто позволяет 
мне передать `PDO` объект прямо в мой класс сопоставления.

## Создание роутеров

"Роутеры" - это шаблоны URL, которые мы будем описывать и прикреплять к ним функциональность. 
Slim не использует автоматическое сопоставление или формулы URL-адресов, поэтому вы можете сделать любой шаблон 
маршрута, который вам нравится, на любую понравившуюся вам функцию, она очень гибкая. Маршруты могут быть связаны 
с определенным HTTP-глаголом (например, GET или POST) или более чем одним глаголом.

В качестве первого примера, вот код для создания запроса GET, `/tickets` в котором перечислен список в мой багтрекер 
пример приложения. Он просто выплевывает переменные, так как мы еще не добавили никаких представлений в наше приложение:

``` php
$app->get('/tickets', function (Request $request, Response $response) {
    $this->logger->addInfo("Ticket list");
    $mapper = new TicketMapper($this->db);
    $tickets = $mapper->getTickets();

    $response->getBody()->write(var_export($tickets, true));
    return $response;
});
``` 


Использование `$app->get()` означает, что этот маршрут доступен только для запросов GET; 
есть эквивалентный `$app->post()` вызов, который также принимает шаблон роутера и обратный вызов для запросов POST. 
Существуют также [методы для других глаголов](objects/router.html) - а также `map()` 
функция для ситуаций, когда более одного глагола должен использовать один и тот же код для определенного роута.

Slim роуты работают в том порядке, в котором они объявлены, поэтому, если у вас есть роут, который может пересекать 
другой роут, вам нужно сначала поставить наиболее конкретный роут. Slim будет генерировать исключение, если есть 
проблема, например, в этом приложении у меня есть оба `/ticket/new` и `/ticket/{id}` они должны быть объявлены в этом 
порядке, иначе маршрутизация будет считать, что «новый» - это идентификатор!

В этом примере приложения все роуторы находятся в `index.php` но напрактике это может сделать довольно длинный и 
громоздкий файл! Это хороших повод прорефакторить ваше приложени, чтобы поместить набор маршрутов с обратными вызовами, 
которые фактически объявлены в другом месте.

Все обратные вызовы роута принимают три параметра (третий необязателен):

<dl>
  <dt>Request</dt>
  <dd>запрос в нем содержится вся информация о входящем запросе, заголовках, переменных и т. д.</dd>

  <dt>Response</dt>
  <dd>ответ мы можем добавить к нему выходные данные и заголовки, и после его завершения он будет превращен 
       в HTTP-ответ, который получает клиент</dd>
  
  <dt>Arguments</dt>
  <dd>аргументы именованные заполнители из URL-адреса (больше всего за один момент), это необязательно и обычно 
  опускается, если нет </dd>
</dl>

Этот акцент на Request and Response иллюстрирует, что Slim 3 основан на стандарте PSR-7 для HTTP-сообщений. 
Использование объекта запроса и ответа также делает приложение более тестируемым, так как нам не нужно делать 
**реальные** запросы и ответы, мы можем просто настроить объекты по желанию.

### Роуторы с помощью именных заполнителей

Sometimes, our URLs have variables in them that we want to use in our application.  In my bug tracking example, 
I want to have URLs like `/ticket/42` to refer to the ticket - and Slim has an easy way of parsing out the "42" 
bit and making it available for easy use in the code.  Here's the route that does exactly that:

``` php
$app->get('/ticket/{id}', function (Request $request, Response $response, $args) {
    $ticket_id = (int)$args['id'];
    $mapper = new TicketMapper($this->db);
    $ticket = $mapper->getTicketById($ticket_id);

    $response->getBody()->write(var_export($ticket, true));
    return $response;
});
```

Look at where the route itself is defined: we write it as `/ticket/{id}`.  When we do this, the route will take the portion of the URL from where the `{id}` is declared, and it becomes available as `$args['id']` inside the callback.

### Using GET Parameters

Since GET and POST send data in such different ways, then the way that we get that data from the Request object differs hugely in Slim.

It is possible to get all the query parameters from a request by doing `$request->getQueryParams()` which will return an associative array.  So for the URL `/tickets?sort=date&order=desc` we'd get an associative array like:
``` php
    ["sort" => "date", "order" => "desc"]
``` 
These can then be used (after validating of course) inside your callback.


### Working with POST Data

When working with incoming data, we can find this in the body.  We've already seen how we can parse data from the URL and how to obtain the GET variables by doing `$request->getQueryParams()` but what about POST data?  The POST request data can be found in the body of the request, and Slim has some good built in helpers to make it easier to get the information in a useful format.

For data that comes from a web form, Slim will turn that into an array.  My tickets example application has a form for creating new tickets that just sends two fields: "title" and "description".  Here is the first part of the route that receives that data, note that for a POST route use `$app->post()` rather than `$app->get()`:

<figure class="highlight"><pre><code class="language-php" data-lang="php">$app-&gt;post('/ticket/new', function (Request $request, Response $response) {
    $data = $request-&gt;getParsedBody();
    $ticket_data = [];
    $ticket_data['title'] = filter_var($data['title'], FILTER_SANITIZE_STRING);
    $ticket_data['description'] = filter_var($data['description'], FILTER_SANITIZE_STRING);
    // ...</code></pre></figure>

The call to `$request->getParsedBody()` asks Slim to look at the request and the `Content-Type` headers of that request, then do something smart and useful with the body.  In this example it's just a form post and so the resulting `$data` array looks very similar to what we'd expect from `$_POST` - and we can go ahead and use the [filter](http://php.net/manual/en/book.filter.php) extension to check the value is acceptable before we use it.  A huge advantage of using the built in Slim methods is that we can test things by injecting different request objects - if we were to use `$_POST` directly, we aren't able to do that.

What's really neat here is that if you're building an API or writing AJAX endpoints, for example, it's super easy to work with data formats that arrive by POST but which aren't a web form.  As long as the `Content-Type` header is set correctly, Slim will parse a JSON payload into an array and you can access it exactly the same way: by using `$request->getParsedBody()`.

## Views and Templates

Slim doesn't have an opinion on the views that you should use, although there are some options that are ready to plug in.  Your best choices are either Twig or plain old PHP.  Both options have pros and cons: if you're already familiar with Twig then it offers lots of excellent features and functionality such as layouts - but if you're not already using Twig, it can be a large learning curve overhead to add to a microframework project.  If you're looking for something dirt simple then the PHP views might be for you!  I picked PHP for this example project, but if you're familiar with Twig then feel free to use that; the basics are mostly the same.

Since we'll be using the PHP views, we'll need to add this dependency to our project via Composer.  The command looks like this (similar to the ones you've already seen):

    php composer.phar require slim/php-view

In order to be able to render the view, we'll first need to create a view and make it available to our application; we do that by adding it to the DIC.  The code we need goes with the other DIC additions near the top of `src/public/index.php` and it looks like this:

<figure class="highlight"><pre><code class="language-php" data-lang="php">$container['view'] = new \Slim\Views\PhpRenderer("../templates/");</code></pre></figure>

Now we have a `view` element in the DIC, and by default it will look for its templates in the `src/templates/` directory.  We can use it to render templates in our actions - here's the ticket list route again, this time including the call to pass data into the template and render it:

<figure class="highlight"><pre><code class="language-php" data-lang="php">$app-&gt;get('/tickets', function (Request $request, Response $response) {
    $this-&gt;logger-&gt;addInfo("Ticket list");
    $mapper = new TicketMapper($this-&gt;db);
    $tickets = $mapper-&gt;getTickets();

    $response = $this-&gt;view-&gt;render($response, "tickets.phtml", ["tickets" =&gt; $tickets]);
    return $response;
});</code></pre></figure>

The only new part here is the penultimate line where we set the `$response` variable.  Now that the `view` is in the DIC, we can refer to it as `$this->view`.  Calling `render()` needs us to supply three arguments: the `$response` to use, the template file (inside the default templates directory), and any data we want to pass in.  Response objects are *immutable* which means that the call to `render()` won't update the response object; instead it will return us a new object which is why it needs to be captured like this.  This is always true when you operate on the response object.

When passing the data to templates, you can add as many elements to the array as you want to make available in the template.  The keys of the array are the variables that the data will exist in once we get to the template itself.

As an example, here's a snippet from the template that displays the ticket list (i.e. the code from `src/templates/tickets.phtml` - which uses [Pure.css](http://purecss.io/) to help cover my lack of frontend skills):

<figure class="highlight"><pre><code class="language-php" data-lang="php"><span class="nt">&lt;h1&gt;</span>All Tickets<span class="nt">&lt;/h1&gt;</span>

<span class="nt">&lt;p&gt;&lt;a</span> <span class="na">href=</span><span class="s">"/ticket/new"</span><span class="nt">&gt;</span>Add new ticket<span class="nt">&lt;/a&gt;&lt;/p&gt;</span>

<span class="nt">&lt;table</span> <span class="na">class=</span><span class="s">"pure-table"</span><span class="nt">&gt;</span>
    <span class="nt">&lt;tr&gt;</span>
        <span class="nt">&lt;th&gt;</span>Title<span class="nt">&lt;/th&gt;</span>
        <span class="nt">&lt;th&gt;</span>Component<span class="nt">&lt;/th&gt;</span>
        <span class="nt">&lt;th&gt;</span>Description<span class="nt">&lt;/th&gt;</span>
        <span class="nt">&lt;th&gt;</span>Actions<span class="nt">&lt;/th&gt;</span>
    <span class="nt">&lt;/tr&gt;</span>

<span class="cp">&lt;?php</span> <span class="k">foreach</span> <span class="p">(</span><span class="nv">$tickets</span> <span class="k">as</span> <span class="nv">$ticket</span><span class="p">)</span><span class="o">:</span> <span class="cp">?&gt;</span>

    <span class="nt">&lt;tr&gt;</span>
        <span class="nt">&lt;td&gt;</span><span class="cp">&lt;?=</span><span class="nv">$ticket</span><span class="o">-&gt;</span><span class="na">getTitle</span><span class="p">()</span> <span class="cp">?&gt;</span><span class="nt">&lt;/td&gt;</span>
        <span class="nt">&lt;td&gt;</span><span class="cp">&lt;?=</span><span class="nv">$ticket</span><span class="o">-&gt;</span><span class="na">getComponent</span><span class="p">()</span> <span class="cp">?&gt;</span><span class="nt">&lt;/td&gt;</span>
        <span class="nt">&lt;td&gt;</span><span class="cp">&lt;?=</span><span class="nv">$ticket</span><span class="o">-&gt;</span><span class="na">getShortDescription</span><span class="p">()</span> <span class="cp">?&gt;</span> ...<span class="nt">&lt;/td&gt;</span>
        <span class="nt">&lt;td&gt;</span>
            <span class="nt">&lt;a</span> <span class="na">href=</span><span class="s">"</span><span class="cp">&lt;?=</span><span class="nv">$router</span><span class="o">-&gt;</span><span class="na">pathFor</span><span class="p">(</span><span class="s1">'ticket-detail'</span><span class="p">,</span> <span class="p">[</span><span class="s1">'id'</span> <span class="o">=&gt;</span> <span class="nv">$ticket</span><span class="o">-&gt;</span><span class="na">getId</span><span class="p">()])</span><span class="cp">?&gt;</span><span class="s">"</span><span class="nt">&gt;</span>view<span class="nt">&lt;/a&gt;</span>
        <span class="nt">&lt;/td&gt;</span>
    <span class="nt">&lt;/tr&gt;</span>

<span class="cp">&lt;?php</span> <span class="k">endforeach</span><span class="p">;</span> <span class="cp">?&gt;</span>
<span class="nt">&lt;/table&gt;</span></code></pre></figure>

In this case, `$tickets` is actually a `TicketEntity` class with getters and setters, but if you passed in an array, you'd be able to access it using array rather than object notation here.

Did you notice something fun going on with `$router->pathFor()` right at the end of the example?  Let's talk about named routes next :)

### Easy URL Building with Named Routes

When we create a route, we can give it a name by calling `->setName()` on the route object.  In this case, I am adding the name to the route that lets me view an individual ticket so that I can quickly create the right URL for a ticket by just giving the name of the route, so my code now looks something like this (just the changed bits shown here):

<figure class="highlight"><pre><code class="language-php" data-lang="php">$app-&gt;get('/ticket/{id}', function (Request $request, Response $response, $args) {
    // ...
})-&gt;setName("ticket-detail");</code></pre></figure>

To use this in my template, I need to make the router available in the template that's going to want to create this URL, so I've amended the `tickets/` route to pass a router through to the template by changing the render line to look like this:

<figure class="highlight"><pre><code class="language-php" data-lang="php">    $response = $this-&gt;view-&gt;render($response, "tickets.phtml", ["tickets" =&gt; $tickets, "router" =&gt; $this-&gt;router]);</code></pre></figure>

With the `/tickets/{id}` route having a friendly name, and the router now available in our template, this is what makes the `pathFor()` call in our template work.  By supplying the `id`, this gets used as a named placeholder in the URL pattern, and the correct URL for linking to that route with those values is created.  This feature is brilliant for readable template URLs and is even better if you ever need to change a URL format for any reason - no need to grep templates to see where it's used.  This approach is definitely recomended, especially for links you'll use a lot.

## Where Next?

This article gave a walkthrough of how to get set up with a simple application of your own, which I hope will let you get quickly started, see some working examples, and build something awesome.

From here, I'd recommend you take a look at the other parts of the project documentation for anything you need that wasn't already covered or that you want to see an alternative example of.  A great next step would be to take a look at the [Middleware](http://www.slimframework.com/docs/concepts/middleware.html) section - this technique is how we layer up our application and add functionality such as authentication which can be applied to multiple routes.
