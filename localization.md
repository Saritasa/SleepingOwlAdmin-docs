# Localization

SleepingOwl Admin uses locale from your `config/app.php`.

```php
/*
|--------------------------------------------------------------------------
| Application Locale Configuration
|--------------------------------------------------------------------------
|
| The application locale determines the default locale that will be used
| by the translation service provider. You are free to set this value
| to any of the locales which will be supported by the application.
|
*/

'locale' => 'ru',
```

## Supported locales (out of box)

 - en
 - ru
 - es
 - pl
 - uk
 - pt-BR
 - zn-CN

## Your own locales

You can create your own locals. Create file `resources/lang/vendor/sleeping_owl/{locale}/lang.php`, 
insert everything from `vendor/laravelrus/sleepingowl/resource/lang/{locale}/lang.php` and edit appropriately. 

**You can create pull request to merge your localization into main package.**
