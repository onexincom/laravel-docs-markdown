# URL Generation
- [Introduction](#introduction)
- [The Basics](#the-basics)
    - [Generating URLs](#generating-urls)

    - [Accessing The Current URL](#accessing-the-current-url)


- [URLs For Named Routes](#urls-for-named-routes)
    - [Signed URLs](#signed-urls)


- [URLs For Controller Actions](#urls-for-controller-actions)
- [Default Values](#default-values)

## [Introduction](#introduction)
Laravel provides several helpers to assist you in generating URLs for your application. These helpers are primarily helpful when building links in your templates and API responses, or when generating redirect responses to another part of your application.

## [The Basics](#the-basics)
### [Generating URLs](#generating-urls)
The `url` helper may be used to generate arbitrary URLs for your application. The generated URL will automatically use the scheme (HTTP or HTTPS) and host from the current request being handled by the application:

```php	
$post= App\Models\Post::find(1);
 
echourl("/posts/{$post->id}");
 
// http://example.com/posts/1
```
### [Accessing The Current URL](#accessing-the-current-url)
If no path is provided to the `url` helper, an `Illuminate\Routing\UrlGenerator` instance is returned, allowing you to access information about the current URL:

```php	
// Get the current URL without the query string...
echourl()->current();
 
// Get the current URL including the query string...
echourl()->full();
 
// Get the full URL for the previous request...
echourl()->previous();
```
Each of these methods may also be accessed via the `URL`[facade](/docs/master/facades):

```php	
use Illuminate\Support\Facades\URL;
 
echoURL::current();
```
## [URLs For Named Routes](#urls-for-named-routes)
The `route` helper may be used to generate URLs to [named routes](/docs/master/routing#named-routes). Named routes allow you to generate URLs without being coupled to the actual URL defined on the route. Therefore, if the route's URL changes, no changes need to be made to your calls to the `route` function. For example, imagine your application contains a route defined like the following:

```php	
Route::get('/post/{post}', function(Post$post) {
// ...
})->name('post.show');
```
To generate a URL to this route, you may use the `route` helper like so:

```php	
echoroute('post.show',['post'=>1]);
 
// http://example.com/post/1
```
Of course, the `route` helper may also be used to generate URLs for routes with multiple parameters:

```php	
Route::get('/post/{post}/comment/{comment}', function(Post$post, Comment$comment) {
// ...
})->name('comment.show');
 
echoroute('comment.show',['post'=>1,'comment'=>3]);
 
// http://example.com/post/1/comment/3
```
Any additional array elements that do not correspond to the route's definition parameters will be added to the URL's query string:

```php	
echoroute('post.show',['post'=>1,'search'=>'rocket']);
 
// http://example.com/post/1?search=rocket
```
#### [Eloquent Models](#eloquent-models)
You will often be generating URLs using the route key (typically the primary key) of [Eloquent models](/docs/master/eloquent). For this reason, you may pass Eloquent models as parameter values. The `route` helper will automatically extract the model's route key:

```php	
echoroute('post.show',['post'=>$post]);
```
### [Signed URLs](#signed-urls)
Laravel allows you to easily create "signed" URLs to named routes. These URLs have a "signature" hash appended to the query string which allows Laravel to verify that the URL has not been modified since it was created. Signed URLs are especially useful for routes that are publicly accessible yet need a layer of protection against URL manipulation.

For example, you might use signed URLs to implement a public "unsubscribe" link that is emailed to your customers. To create a signed URL to a named route, use the `signedRoute` method of the `URL` facade:

```php	
use Illuminate\Support\Facades\URL;
 
returnURL::signedRoute('unsubscribe', ['user'=>1]);
```
If you would like to generate a temporary signed route URL that expires after a specified amount of time, you may use the `temporarySignedRoute` method. When Laravel validates a temporary signed route URL, it will ensure that the expiration timestamp that is encoded into the signed URL has not elapsed:

```php	
use Illuminate\Support\Facades\URL;
 
returnURL::temporarySignedRoute(
'unsubscribe', now()->addMinutes(30), ['user'=>1]
);
```
#### [Validating Signed Route Requests](#validating-signed-route-requests)
To verify that an incoming request has a valid signature, you should call the `hasValidSignature` method on the incoming `Illuminate\Http\Request` instance:

```php	
use Illuminate\Http\Request;
 
Route::get('/unsubscribe/{user}', function(Request$request) {
if (!$request->hasValidSignature()) {
abort(401);
 }
 
// ...
})->name('unsubscribe');
```
Sometimes, you may need to allow your application's frontend to append data to a signed URL, such as when performing client-side pagination. Therefore, you can specify request query parameters that should be ignored when validating a signed URL using the `hasValidSignatureWhileIgnoring` method. Remember, ignoring parameters allows anyone to modify those parameters on the request:

```php	
if (!$request->hasValidSignatureWhileIgnoring(['page', 'order'])) {
abort(401);
}
```
Instead of validating signed URLs using the incoming request instance, you may assign the `Illuminate\Routing\Middleware\ValidateSignature`[middleware](/docs/master/middleware) to the route. If it is not already present, you may assign this middleware an alias in your HTTP kernel's `$middlewareAliases` array:

```php	
/**
 * The application's middleware aliases.
 *
 * Aliases may be used to conveniently assign middleware to routes and groups.
 *
 * @vararray<string, class-string|string>
*/
protected$middlewareAliases= [
'signed'=> \Illuminate\Routing\Middleware\ValidateSignature::class,
];
```
Once you have registered the middleware in your kernel, you may attach it to a route. If the incoming request does not have a valid signature, the middleware will automatically return a `403` HTTP response:

```php	
Route::post('/unsubscribe/{user}', function(Request$request) {
// ...
})->name('unsubscribe')->middleware('signed');
```
#### [Responding To Invalid Signed Routes](#responding-to-invalid-signed-routes)
When someone visits a signed URL that has expired, they will receive a generic error page for the `403` HTTP status code. However, you can customize this behavior by defining a custom "renderable" closure for the `InvalidSignatureException` exception in your exception handler. This closure should return an HTTP response:

```php	
use Illuminate\Routing\Exceptions\InvalidSignatureException;
 
/**
 * Register the exception handling callbacks for the application.
*/
publicfunctionregister():void
{
$this->renderable(function(InvalidSignatureException$e) {
returnresponse()->view('error.link-expired', [], 403);
 });
}
```
## [URLs For Controller Actions](#urls-for-controller-actions)
The `action` function generates a URL for the given controller action:

```php	
use App\Http\Controllers\HomeController;
 
$url=action([HomeController::class,'index']);
```
If the controller method accepts route parameters, you may pass an associative array of route parameters as the second argument to the function:

```php	
$url=action([UserController::class,'profile'],['id'=>1]);
```
## [Default Values](#default-values)
For some applications, you may wish to specify request-wide default values for certain URL parameters. For example, imagine many of your routes define a `{locale}` parameter:

```php	
Route::get('/{locale}/posts', function() {
// ...
})->name('post.index');
```
It is cumbersome to always pass the `locale` every time you call the `route` helper. So, you may use the `URL::defaults` method to define a default value for this parameter that will always be applied during the current request. You may wish to call this method from a [route middleware](/docs/master/middleware#assigning-middleware-to-routes) so that you have access to the current request:

```php	
<?php
 
namespace App\Http\Middleware;
 
useClosure;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\URL;
use Symfony\Component\HttpFoundation\Response;
 
classSetDefaultLocaleForUrls
{
/**
 * Handle an incoming request.
 *
 * @param\Closure(\Illuminate\Http\Request): (\Symfony\Component\HttpFoundation\Response) $next
*/
publicfunctionhandle(Request$request, Closure$next):Response
 {
URL::defaults(['locale'=>$request->user()->locale]);
 
return$next($request);
 }
}
```
Once the default value for the `locale` parameter has been set, you are no longer required to pass its value when generating URLs via the `route` helper.

#### [URL Defaults & Middleware Priority](#url-defaults-middleware-priority)
Setting URL default values can interfere with Laravel's handling of implicit model bindings. Therefore, you should [prioritize your middleware](/docs/master/middleware#sorting-middleware) that set URL defaults to be executed before Laravel's own `SubstituteBindings` middleware. You can accomplish this by making sure your middleware occurs before the `SubstituteBindings` middleware within the `$middlewarePriority` property of your application's HTTP kernel.

The `$middlewarePriority` property is defined in the base `Illuminate\Foundation\Http\Kernel` class. You may copy its definition from that class and overwrite it in your application's HTTP kernel in order to modify it:

```php	
/**
 * The priority-sorted list of middleware.
 *
 * This forces non-global middleware to always be in the given order.
 *
 * @vararray
*/
protected$middlewarePriority= [
// ...
 \App\Http\Middleware\SetDefaultLocaleForUrls::class,
 \Illuminate\Routing\Middleware\SubstituteBindings::class,
// ...
];
```
