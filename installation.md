# Installation

You can install package via command line command
```
 composer require "laravelrus/sleepingowl":"4.*@dev"
```
or add string
```
"laravelrus/sleepingowl": "4.*@dev"
```
into `composer.json` and execute `composer update`

Now you should add Service Provider, in `providers` section of file `config/app.php`:

```
SleepingOwl\Admin\Providers\SleepingOwlServiceProvider::class,
```

**Example**
```php
    ...
    /*
     * SleepingOwl Service Provider
     */
      SleepingOwl\Admin\Providers\SleepingOwlServiceProvider::class,

      /*
     * Application Service Providers...
     */
    App\Providers\AppServiceProvider::class,
    ...
```

Then run command `php artisan sleepingowl:install`.

Installation is complete, you can proceed to [configuration](configuration).
