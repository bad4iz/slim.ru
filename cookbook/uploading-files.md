---
title: Загрузка файлов с использованием форм POST
---

Файлы, которые загружаются с использованием форм в POST-запросах, могут быть получены с помощью
[`getUploadedFiles`](/docs/objects/request.html#uploaded-files) метода `Request` объекта.

При загрузке файлов с использованием запроса POST убедитесь, что ваша форма загрузки файла имеет атрибут, 
`enctype="multipart/form-data"` иначе `getUploadedFiles()` будет возвращен пустой массив.

Если несколько файлов загружаются для одного имени ввода, добавьте скобки после имени ввода в HTML, иначе только 
один загруженный файл будет возвращен для имени ввода `getUploadedFiles()`.

Ниже приведен пример HTML-формы, содержащей как одиночную, так и множественную загрузку файлов.

```php
<!-- make sure the attribute enctype is set to multipart/form-data -->
<form method="post" enctype="multipart/form-data">
    <!-- upload of a single file -->
    <p>
        <label>Add file (single): </label><br/>
        <input type="file" name="example1"/>
    </p>

    <!-- multiple input fields for the same input name, use brackets -->
    <p>
        <label>Add files (up to 2): </label><br/>
        <input type="file" name="example2[]"/><br/>
        <input type="file" name="example2[]"/>
    </p>

    <!-- one file input field that allows multiple files to be uploaded, use brackets -->
    <p>
        <label>Add files (multiple): </label><br/>
        <input type="file" name="example3[]" multiple="multiple"/>
    </p>

    <p>
        <input type="submit"/>
    </p>
```
</form>
<figure>
<figcaption>Figure 1: Пример формы HTML для загрузки файлов</figcaption>
</figure>

Загруженные файлы можно перемещать в каталог с помощью `moveTo` метода. Ниже приведен пример приложения, который
обрабатывает загруженные файлы HTML-формы выше.

```php
<?php

require_once __DIR__ . '/vendor/autoload.php';

use Slim\Http\Request;
use Slim\Http\Response;
use Slim\Http\UploadedFile;

$app = new \Slim\App();

$container = $app->getContainer();
$container['upload_directory'] = __DIR__ . '/uploads';

$app->post('/', function(Request $request, Response $response) {
    $directory = $this->get('upload_directory');

    $uploadedFiles = $request->getUploadedFiles();

    // handle single input with single file upload
    $uploadedFile = $uploadedFiles['example1'];
    if ($uploadedFile->getError() === UPLOAD_ERR_OK) {
        $filename = moveUploadedFile($directory, $uploadedFile);
        $response->write('uploaded ' . $filename . '<br/>');
    }


    // handle multiple inputs with the same key
    foreach ($uploadedFiles['example2'] as $uploadedFile) {
        if ($uploadedFile->getError() === UPLOAD_ERR_OK) {
            $filename = moveUploadedFile($directory, $uploadedFile);
            $response->write('uploaded ' . $filename . '<br/>');
        }
    }

    // handle single input with multiple file uploads
    foreach ($uploadedFiles['example3'] as $uploadedFile) {
        if ($uploadedFile->getError() === UPLOAD_ERR_OK) {
            $filename = moveUploadedFile($directory, $uploadedFile);
            $response->write('uploaded ' . $filename . '<br/>');
        }
    }

    // handle single input with multiple file uploads
    foreach ($uploadedFiles['example3'] as $uploadedFile) {
        if ($uploadedFile->getError() === UPLOAD_ERR_OK) {
            $filename = moveUploadedFile($directory, $uploadedFile);
            $response->write('uploaded ' . $filename . '<br/>');
        }
    }
});

/**
 * Moves the uploaded file to the upload directory and assigns it a unique name
 * to avoid overwriting an existing uploaded file.
 *
 * @param string $directory directory to which the file is moved
 * @param UploadedFile $uploaded file uploaded file to move
 * @return string filename of moved file
 */
function moveUploadedFile($directory, UploadedFile $uploadedFile)
{
    $extension = pathinfo($uploadedFile->getClientFilename(), PATHINFO_EXTENSION);
    $basename = bin2hex(random_bytes(8)); // see http://php.net/manual/en/function.random-bytes.php
    $filename = sprintf('%s.%0.8s', $basename, $extension);

    $uploadedFile->moveTo($directory . DIRECTORY_SEPARATOR . $filename);

    return $filename;
}

$app->run();
```
<figure>
<figcaption>Figure 2: Пример Slim applcation для обработки загруженных файловs</figcaption>
</figure>

Смотрите также
--------
* [https://akrabat.com/psr-7-file-uploads-in-slim-3/](https://akrabat.com/psr-7-file-uploads-in-slim-3/)
