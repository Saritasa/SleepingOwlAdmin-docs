# Authorization

SleepingOwl Admin doesn't use authorization by default, i.e. admin panel is not protected from unauthorized access. 
You can restrict access, using `middleware`.

Easiest way is to use standard implementation of Laravel itself.

To use it, you should execute console command `php artisan make:auth` 
(See https://laravel.com/docs/5.3/authentication#authentication-quickstart for details) 
and add `auth` middleware in `sleeping_owl.php` config.

```php
/*
 | ...
 | see https://laravel.com/docs/5.3/authentication#authentication-quickstart
 |
 */
    
'middleware' => ['web', 'auth'],
...
```

Thus, you have ability to implement very flexible access rules, using middleware.

---

## Admin panel access restrictions

You can restrict admin panel access globally, using middleware.

Let's say, you have user roles, and you want only administrators to be able access admin panel.
You can create a new middleware class, ex `App\Http\Middleware\AdminAuthenticate`

```php
<?php

namespace App\Http\Middleware;

use App\User;
use Closure;
use Illuminate\Support\Facades\Auth;
class AdminAuthenticate
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request $request
     * @param  \Closure $next
     * @param  string|null $guard
     *
     * @return mixed
     */
    public function handle($request, Closure $next, $guard = null)
    {
        $auth = Auth::guard($guard);
        if (Auth::guard($guard)->guest()) {
            if ($request->ajax() || $request->wantsJson()) {
                return response('Unauthorized.', 401);
            } else {
                return redirect()->guest('login');
            }
        }
        if (! $auth->user()->isAdmin()) {
            return response('Access denied.', 401);
        }
        
        return $next($request);
    }
}
```

Register this class in `App\Http\Kernel`

```php
 ...
 protected $routeMiddleware = [
     ...
     'admin' => \App\Http\Middleware\AdminAuthenticate::class,
     ...
 ];
 ...
```

Now you can add this middleware in config `config\sleeoping_owl.php`

```php
...
'middleware' => ['web', 'admin'],
...
```

You can learn more details about authorization in [Laravel documentation](https://laravel.com/docs/5.3/authentication)
