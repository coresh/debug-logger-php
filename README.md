# Debug Logger PHP

Простой логгер, cохраняющий отладочную информацию в файл.

# Содержание

<!-- MarkdownTOC levels="1,2,3,4,5,6" autoanchor="true" autolink="true" -->

- [Требования](#%D0%A2%D1%80%D0%B5%D0%B1%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D1%8F)
- [Установка](#%D0%A3%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0)
- [Класс `\App\DebugLogger`](#%D0%9A%D0%BB%D0%B0%D1%81%D1%81-appdebuglogger)
    - [Методы класса](#%D0%9C%D0%B5%D1%82%D0%BE%D0%B4%D1%8B-%D0%BA%D0%BB%D0%B0%D1%81%D1%81%D0%B0)
    - [Дополнительные параметры](#%D0%94%D0%BE%D0%BF%D0%BE%D0%BB%D0%BD%D0%B8%D1%82%D0%B5%D0%BB%D1%8C%D0%BD%D1%8B%D0%B5-%D0%BF%D0%B0%D1%80%D0%B0%D0%BC%D0%B5%D1%82%D1%80%D1%8B)
    - [Примеры](#%D0%9F%D1%80%D0%B8%D0%BC%D0%B5%D1%80%D1%8B)
- [Автор](#%D0%90%D0%B2%D1%82%D0%BE%D1%80)
- [Лицензия](#%D0%9B%D0%B8%D1%86%D0%B5%D0%BD%D0%B7%D0%B8%D1%8F)

<!-- /MarkdownTOC -->

<a id="%D0%A2%D1%80%D0%B5%D0%B1%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D1%8F"></a>
## Требования

- PHP >= 7.0.
- Произвольный автозагрузчик классов, реализующий стандарт [PSR-4](https://www.php-fig.org/psr/psr-4/).

<a id="%D0%A3%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0"></a>
## Установка

Установка через composer:
```
$ composer require andrey-tech/debug-logger-php
```

или добавить

```
"andrey-tech/debug-logger-php"
```

в секцию require файла composer.json.

<a id="%D0%9A%D0%BB%D0%B0%D1%81%D1%81-appdebuglogger"></a>
## Класс `\App\DebugLogger`

Класс `\App\DebugLogger` обеспечивает логирование отладочной информации в файл.  
При возникновении ошибок выбрасывается исключение с объектом класса `\App\AppException`. 

<a id="%D0%9C%D0%B5%D1%82%D0%BE%D0%B4%D1%8B-%D0%BA%D0%BB%D0%B0%D1%81%D1%81%D0%B0"></a>
### Методы класса

- `static instance(string $logFileName = 'debug.log') :\App\DebugLogger`  
    Возвращает единственнный объект класса для заданного лог-файла `$logFileName`.
    + `$logFileName` - имя лог-файла.
- `save(mixed $info, $object = null) :void` Сохраняет подлежащую логированию информацию в файл.
    + $info - строка, массив или объект для логирования;
    + $object - ссылка на объект класса в котором выполняется логирование.

<a id="%D0%94%D0%BE%D0%BF%D0%BE%D0%BB%D0%BD%D0%B8%D1%82%D0%B5%D0%BB%D1%8C%D0%BD%D1%8B%D0%B5-%D0%BF%D0%B0%D1%80%D0%B0%D0%BC%D0%B5%D1%82%D1%80%D1%8B"></a>
### Дополнительные параметры

Дополнительные параметры устанавливаются через публичные свойства объекта класса `\App\DebugLogger`:

Свойство                | По умолчанию  | Описание
----------------------- | ------------- | --------
`$isActive`             | false         | Включает или выключает логирование
`$logFileDir`           | `temp/`       | Устанавливает каталог, в котором сохраняются лог-файлы

<a id="%D0%9F%D1%80%D0%B8%D0%BC%D0%B5%D1%80%D1%8B"></a>
### Примеры 

```php
use \App\DebugLogger;

try {
    $logFileName = 'debug_extensions.log'
    $logger = DebugLogger::instance($logFileName);

    // Устанавливаем каталог для сохранения лог-файлов
    $logger->logFileDir = 'logs/';

    // Включаем логирование
    $logger->isActive = true;

    // Сохраняем информацию о всех скомпилированных и загруженных модулях
    $logger->save(get_loaded_extensions());
    
    // Сохраняем версию движка Zend
    $logger->save(zend_version());

} catch (\App\AppException $e) {
    printf('Ошибка (%d): %s' . PHP_EOL, $e->getCode(), $e->getMessage());
}
```

Пример результатов логирования:

```
*** ytoqjz5 [2020-06-14 12:57:18,420258 +00:00 Δ- s, 0.69/2.00 MiB] ********************
[
    "Core",
    "bcmath",
    "calendar",
    "ctype"
]

*** ytoqjz5 [2020-06-14 12:57:18,421359 +00:00 Δ0.001101 s, 0.69/2.00 MiB] ********************
3.2.0
```

Формат заголовков логирования:

- `*** ytoqjz5 [2020-06-14 12:57:18,421359 +00:00 Δ0.001101 s, 0.69/2.00 MiB] ********************`
    + `ytoqjz5` - уникальный идентификатор объекта класса '\App\DebugLogger';
    + `2020-06-14 12:57:18,421359 +00:00` - время сохранения информации;
    + `Δ0.001101 s` - время, прошедшее с момента предыдущего сохранения информации;
    + `0.69/2.00 MiB` - максимальный объем памяти, который был выделен PHP-скрипту / реальный объем памяти, выделенный системой.

<a id="%D0%90%D0%B2%D1%82%D0%BE%D1%80"></a>
## Автор

© 2019-2020 andrey-tech

<a id="%D0%9B%D0%B8%D1%86%D0%B5%D0%BD%D0%B7%D0%B8%D1%8F"></a>
## Лицензия

Данная библиотека распространяется на условиях лицензии [MIT](./LICENSE).