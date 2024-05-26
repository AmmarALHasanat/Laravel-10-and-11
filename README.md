# Changes and Comparison between Laravel 10 and 11
Laravel has undergone significant changes in version 11, particularly in terms of directory structure, which has been noticeably streamlined compared to previous versions. Let's delve into the details.

## 1. Slim Skeleton
Folder structure changed to look like this
```
app/
├── Http/
│   └── Controllers/
│       └── Controller.php
├── Models/
│   └── User.php
└── Providers/
    └── AppServiceProvider.php
bootstrap/
├── app.php
└── providers.php
config
...
```
> Removed folders: **app/Console, app/Exceptions, app/Http/Middleware.**
> Also, The files **routes/channel.php, routes/console.php, and routes/api.php** have been removed.

> Routes, Middlewares, and Exceptions are now registered in the **bootstrap/app.php** file.

### bootstrap/app.php:
```
<?php

use Illuminate\Foundation\Application;
use Illuminate\Foundation\Configuration\Exceptions;
use Illuminate\Foundation\Configuration\Middleware;

return Application::configure(basePath: dirname(__DIR__))
    ->withRouting(
        web: __DIR__.'/../routes/web.php',
        commands: __DIR__.'/../routes/console.php',
        health: '/up',
    )
    ->withMiddleware(function (Middleware $middleware) {
        // add use $middleware->use([])->web([])->apu([])->......
    })
    ->withExceptions(function (Exceptions $exceptions) {
        //
    })->create();
```

## 2. Removed Some Config Files

- config/broadcasting.php
- config/cors.php
- config/hashing.php
- config/sanctum.php
- config/view.php

But you can publish them manually, with this command:
```
php artisan config:publish
```
```
php artisan config:publish --all
```

## 3. API and Broadcasting: Installed Optionally
Laravel 11 does not installed Sanctum by default. You can install the API scaffolding with
```
php artisan install:api
```
after install and regestr you must only add the Laravel\Sanctum\HasApiTokens trait to the User Model.

```
php artisan install:broadcast
```

## 4. New Defaults: Pest and SQLite
The default database for Laravel was changed to SQLite.

## 5. New make Commands
  
```
php artisan make:enum
```
```
php artisan make:class
```
```
php artisan make:interface
```

```
php artisan make:trait
```


## 6. Dumpable Trait
Laravel 11 introduces a new Dumpable trait, designed to substitute the existing dd() and dump() methods across various classes within the framework. 
This trait can be utilized by users and package authors to facilitate code debugging.

Here is the usage example:
```
use Illuminate\Support\Traits\Conditionable;
use Illuminate\Support\Traits\Dumpable;
 
class Post
{
    use Conditionable, Dumpable;
 
    // ...
}
```
```
$post = new Post;
$post->setThis()->dd()->setThat(); 
```

## 7. Limit Eager Load
Laravel 11 will allow limiting eagerly loaded records natively, without external packages.

```
class Post extends Model
{
    // ...
}
```
```
class User extends Model
{
    public function posts()
    {
        return $this->hasMany(Post::class);
    }
}
```
```
$users = User::with(['posts' => function ($query) {
    $query->latest()->limit(10); 
}])->get();
```

## 8. <a href='https://laravel.com/docs/11.x/eloquent-mutators#attribute-casting'>Casts Method</a>
the casts will be provided in the protected method casts() instead of the protected property $casts.

```
 
class User extends extends Authenticatable
{
    /**
     * Get the attributes that should be cast.
     *
     * @return array<string, string>
     */
    protected function casts(): array
    {
        return [
            'email_verified_at' => 'datetime',
            'password' => 'hashed',
            'type' => [AsCollection::class, OptionCollection::class],
        ];
    }
}
```

## 9. once() Method
This function ensures that a callable is called only once, returning the same result on subsequent calls.

```
class User extends Model
{
    public function stats(): array
    {
        return once(function () {
            // Expensive operations to generate user stats, multiple db queries, etc...
 
            return $stats;
        });
    }
}
```
```
// Assuming you have two User instances: $userA and $userB
 
$userA->stats();
$userA->stats(); // cached result from previous line...
 
$userB->stats(); // Not using $userA cached results, because $userB !== $userA
$userB->stats(); // cached result from previous line...
```

## Last but not least: Minimum PHP 8.2

| Version | PHP (*) | Release           | Bug Fixes Until | Security Fixes Until |
|---------|---------|-------------------|-----------------|----------------------|
| 10      | 8.1 - 8.3 | February 14th, 2023 | August 6th, 2024 | February 4th, 2025  |
| 11      | 8.2 - 8.3 | March 12th, 2024    | September 3rd, 2025 | March 12th, 2026    |

