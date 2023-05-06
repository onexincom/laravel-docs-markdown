# Middleware
- [Introduction](#introduction)
- [Defining Middleware](#defining-middleware)
- [Registering Middleware](#registering-middleware)
    - [Global Middleware](#global-middleware)

    - [Assigning Middleware To Routes](#assigning-middleware-to-routes)

    - [Middleware Groups](#middleware-groups)

    - [Sorting Middleware](#sorting-middleware)


- [Middleware Parameters](#middleware-parameters)
- [Terminable Middleware](#terminable-middleware)

## [Introduction](#introduction)
Middleware provide a convenient mechanism for inspecting and filtering HTTP requests entering your application. For example, Laravel includes a middleware that verifies the user of your application is authenticated. If the user is not authenticated, the middleware will redirect the user to your application's login screen. However, if the user is authenticated, the middleware will allow the request to proceed further into the application.

Additional middleware can be written to perform a variety of tasks besides authentication. For example, a logging middleware might log all incoming requests to your application. There are several middleware included in the Laravel framework, including middleware for authentication and CSRF protection. All of these middleware are located in the `app/Http/Middleware` directory.

## [Defining Middleware](#defining-middleware)
To create a new middleware, use the `make:middleware` Artisan command:

```shell	
php artisanmake:middlewareEnsureTokenIsValid
```
This command will place a new `EnsureTokenIsValid` class within your `app/Http/Middleware` directory. In this middleware, we will only allow access to the route if the supplied `token` input matches a specified value. Otherwise, we will redirect the users back to the `home` URI:

```php	
<?php
 
namespace App\Http\Middleware;
 
useClosure;
use Illuminate\Http\Request;
use Symfony\Component\HttpFoundation\Response;
 
classEnsureTokenIsValid
{
/**
 * Handle an incoming request.
 *
 * @param\Closure(\Illuminate\Http\Request): (\Symfony\Component\HttpFoundation\Response) $next
*/
publicfunctionhandle(Request$request, Closure$next):Response
 {
if ($request->input('token') !=='my-secret-token') {
returnredirect('home');
 }
 
return$next($request);
 }
}
```
As you can see, if the given `token` does not match our secret token, the middleware will return an HTTP redirect to the client; otherwise, the request will be passed further into the application. To pass the request deeper into the application (allowing the middleware to "pass"), you should call the `$next` callback with the `$request`.

It's best to envision middleware as a series of "layers" HTTP requests must pass through before they hit your application. Each layer can examine the request and even reject it entirely.

> **Note**  
>  All middleware are resolved via the [service container](/docs/master/container), so you may type-hint any dependencies you need within a middleware's constructor.

[]()[]()

#### Middleware & Responses
Of course, a middleware can perform tasks before or after passing the request deeper into the application. For example, the following middleware would perform some task **before** the request is handled by the application:

```php	
<?php
 
namespace App\Http\Middleware;
 
useClosure;
use Illuminate\Http\Request;
use Symfony\Component\HttpFoundation\Response;
 
classBeforeMiddleware
{
publicfunctionhandle(Request$request, Closure$next):Response
 {
// Perform action
 
return$next($request);
 }
}
```
However, this middleware would perform its task **after** the request is handled by the application:

```php	
<?php
 
namespace App\Http\Middleware;
 
useClosure;
use Illuminate\Http\Request;
use Symfony\Component\HttpFoundation\Response;
 
classAfterMiddleware
{
publicfunctionhandle(Request$request, Closure$next):Response
 {
$response=$next($request);
 
// Perform action
 
return$response;
 }
}
```
## [Registering Middleware](#registering-middleware)
### [Global Middleware](#global-middleware)
If you want a middleware to run during every HTTP request to your application, list the middleware class in the `$middleware` property of your `app/Http/Kernel.php` class.

### [Assigning Middleware To Routes](#assigning-middleware-to-routes)
If you would like to assign middleware to specific routes, you may invoke the `middleware` method when defining the route:

```php	
use App\Http\Middleware\Authenticate;
 
Route::get('/profile', function() {
// ...
})->middleware(Authenticate::class);
```
You may assign multiple middleware to the route by passing an array of middleware names to the `middleware` method:

```php	
Route::get('/', function() {
// ...
})->middleware([First::class, Second::class]);
```
For convenience, you may assign aliases to middleware in your application's `app/Http/Kernel.php` file. By default, the `$middlewareAliases` property of this class contains entries for the middleware included with Laravel. You may add your own middleware to this list and assign it an alias of your choosing:

```php	
// Within App\Http\Kernel class...
 
protected$middlewareAliases= [
'auth'=> \App\Http\Middleware\Authenticate::class,
'auth.basic'=> \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
'bindings'=> \Illuminate\Routing\Middleware\SubstituteBindings::class,
'cache.headers'=> \Illuminate\Http\Middleware\SetCacheHeaders::class,
'can'=> \Illuminate\Auth\Middleware\Authorize::class,
'guest'=> \App\Http\Middleware\RedirectIfAuthenticated::class,
'signed'=> \Illuminate\Routing\Middleware\ValidateSignature::class,
'throttle'=> \Illuminate\Routing\Middleware\ThrottleRequests::class,
'verified'=> \Illuminate\Auth\Middleware\EnsureEmailIsVerified::class,
];
```
Once the middleware alias has been defined in the HTTP kernel, you may use the alias when assigning middlware to routes:

```php	
Route::get('/profile', function() {
// ...
})->middleware('auth');
```
#### [Excluding Middleware](#excluding-middleware)
When assigning middleware to a group of routes, you may occasionally need to prevent the middleware from being applied to an individual route within the group. You may accomplish this using the `withoutMiddleware` method:

```php	
use App\Http\Middleware\EnsureTokenIsValid;
 
Route::middleware([EnsureTokenIsValid::class])->group(function() {
Route::get('/', function() {
// ...
 });
 
Route::get('/profile', function() {
// ...
 })->withoutMiddleware([EnsureTokenIsValid::class]);
});
```
You may also exclude a given set of middleware from an entire [group](/docs/master/routing#route-groups) of route definitions:

```php	
use App\Http\Middleware\EnsureTokenIsValid;
 
Route::withoutMiddleware([EnsureTokenIsValid::class])->group(function() {
Route::get('/profile', function() {
// ...
 });
});
```
The `withoutMiddleware` method can only remove route middleware and does not apply to [global middleware](#global-middleware).

### [Middleware Groups](#middleware-groups)
Sometimes you may want to group several middleware under a single key to make them easier to assign to routes. You may accomplish this using the `$middlewareGroups` property of your HTTP kernel.

Laravel includes predefined `web` and `api` middleware groups that contain common middleware you may want to apply to your web and API routes. Remember, these middleware groups are automatically applied by your application's `App\Providers\RouteServiceProvider` service provider to routes within your corresponding `web` and `api` route files:

```php	
/**
 * The application's route middleware groups.
 *
 * @vararray
*/
protected$middlewareGroups= [
'web'=> [
 \App\Http\Middleware\EncryptCookies::class,
 \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
 \Illuminate\Session\Middleware\StartSession::class,
 \Illuminate\View\Middleware\ShareErrorsFromSession::class,
 \App\Http\Middleware\VerifyCsrfToken::class,
 \Illuminate\Routing\Middleware\SubstituteBindings::class,
 ],
 
'api'=> [
 \Illuminate\Routing\Middleware\ThrottleRequests::class.':api',
 \Illuminate\Routing\Middleware\SubstituteBindings::class,
 ],
];
```
Middleware groups may be assigned to routes and controller actions using the same syntax as individual middleware. Again, middleware groups make it more convenient to assign many middleware to a route at once:

```php	
Route::get('/', function() {
// ...
})->middleware('web');
 
Route::middleware(['web'])->group(function() {
// ...
});
```
> **Note**  
>  Out of the box, the `web` and `api` middleware groups are automatically applied to your application's corresponding `routes/web.php` and `routes/api.php` files by the `App\Providers\RouteServiceProvider`.

### [Sorting Middleware](#sorting-middleware)
Rarely, you may need your middleware to execute in a specific order but not have control over their order when they are assigned to the route. In this case, you may specify your middleware priority using the `$middlewarePriority` property of your `app/Http/Kernel.php` file. This property may not exist in your HTTP kernel by default. If it does not exist, you may copy its default definition below:

```php	
/**
 * The priority-sorted list of middleware.
 *
 * This forces non-global middleware to always be in the given order.
 *
 * @varstring[]
*/
protected$middlewarePriority= [
 \Illuminate\Foundation\Http\Middleware\HandlePrecognitiveRequests::class,
 \Illuminate\Cookie\Middleware\EncryptCookies::class,
 \Illuminate\Session\Middleware\StartSession::class,
 \Illuminate\View\Middleware\ShareErrorsFromSession::class,
 \Illuminate\Contracts\Auth\Middleware\AuthenticatesRequests::class,
 \Illuminate\Routing\Middleware\ThrottleRequests::class,
 \Illuminate\Routing\Middleware\ThrottleRequestsWithRedis::class,
 \Illuminate\Contracts\Session\Middleware\AuthenticatesSessions::class,
 \Illuminate\Routing\Middleware\SubstituteBindings::class,
 \Illuminate\Auth\Middleware\Authorize::class,
];
```
## [Middleware Parameters](#middleware-parameters)
Middleware can also receive additional parameters. For example, if your application needs to verify that the authenticated user has a given "role" before performing a given action, you could create an `EnsureUserHasRole` middleware that receives a role name as an additional argument.

Additional middleware parameters will be passed to the middleware after the `$next` argument:

```php	
<?php
 
namespace App\Http\Middleware;
 
useClosure;
use Illuminate\Http\Request;
use Symfony\Component\HttpFoundation\Response;
 
classEnsureUserHasRole
{
/**
 * Handle an incoming request.
 *
 * @param\Closure(\Illuminate\Http\Request): (\Symfony\Component\HttpFoundation\Response) $next
*/
publicfunctionhandle(Request$request, Closure$next, string$role):Response
 {
if (!$request->user()->hasRole($role)) {
// Redirect...
 }
 
return$next($request);
 }
 
}
```
Middleware parameters may be specified when defining the route by separating the middleware name and parameters with a `:`. Multiple parameters should be delimited by commas:

```php	
Route::put('/post/{id}', function(string$id) {
// ...
})->middleware('role:editor');
```
## [Terminable Middleware](#terminable-middleware)
Sometimes a middleware may need to do some work after the HTTP response has been sent to the browser. If you define a `terminate` method on your middleware and your web server is using FastCGI, the `terminate` method will automatically be called after the response is sent to the browser:

```php	
<?php
 
namespace Illuminate\Session\Middleware;
 
useClosure;
use Illuminate\Http\Request;
use Symfony\Component\HttpFoundation\Response;
 
classTerminatingMiddleware
{
/**
 * Handle an incoming request.
 *
 * @param\Closure(\Illuminate\Http\Request): (\Symfony\Component\HttpFoundation\Response) $next
*/
publicfunctionhandle(Request$request, Closure$next):Response
 {
return$next($request);
 }
 
/**
 * Handle tasks after the response has been sent to the browser.
*/
publicfunctionterminate(Request$request, Response$response):void
 {
// ...
 }
}
```
The `terminate` method should receive both the request and the response. Once you have defined a terminable middleware, you should add it to the list of routes or global middleware in the `app/Http/Kernel.php` file.

When calling the `terminate` method on your middleware, Laravel will resolve a fresh instance of the middleware from the [service container](/docs/master/container). If you would like to use the same middleware instance when the `handle` and `terminate` methods are called, register the middleware with the container using the container's `singleton` method. Typically this should be done in the `register` method of your `AppServiceProvider`:

```php	
use App\Http\Middleware\TerminatingMiddleware;
 
/**
 * Register any application services.
*/
publicfunctionregister():void
{
$this->app->singleton(TerminatingMiddleware::class);
}
```
