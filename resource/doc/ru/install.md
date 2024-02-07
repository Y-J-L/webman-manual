# Требования к окружению

* PHP >= 7.2
* [Composer](https://getcomposer.org/) >= 2.0

### 1. Создание проекта

```php
composer create-project workerman/webman
```

### 2. Запуск

Перейдите в каталог webman

#### Для пользователей Windows
Дважды щелкните файл `windows.bat` или запустите `php windows.php` для запуска

> **Подсказка**
> Если возникают ошибки, скорее всего, некоторые функции заблокированы. См. [Проверка запрета функций](others/disable-function-check.md) для их разблокировки

#### Для пользователей Linux
Запуск в режиме `debug` (для разработки и отладки)

```php
php start.php start
```

Запуск в режиме `daemon` (для рабочей среды)

```php
php start.php start -d
```

> **Подсказка**
> Если возникают ошибки, скорее всего, некоторые функции заблокированы. См. [Проверка запрета функций](others/disable-function-check.md) для их разблокировки

### 3. Доступ

Откройте браузер и перейдите по адресу `http://ip адрес:8787`