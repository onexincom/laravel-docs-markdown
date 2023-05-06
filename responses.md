# HTTP Responses
- [Creating Responses](#creating-responses)
    - [Attaching Headers To Responses](#attaching-headers-to-responses)

    - [Attaching Cookies To Responses](#attaching-cookies-to-responses)

    - [Cookies & Encryption](#cookies-and-encryption)


- [Redirects](#redirects)
    - [Redirecting To Named Routes](#redirecting-named-routes)

    - [Redirecting To Controller Actions](#redirecting-controller-actions)

    - [Redirecting To External Domains](#redirecting-external-domains)

    - [Redirecting With Flashed Session Data](#redirecting-with-flashed-session-data)


- [Other Response Types](#other-response-types)
    - [View Responses](#view-responses)

    - [JSON Responses](#json-responses)

    - [File Downloads](#file-downloads)

    - [File Responses](#file-responses)


- [Response Macros](#response-macros)

## [Creating Responses](#creating-responses)
#### [Strings & Arrays](#strings-arrays)
All routes and controllers should return a response to be sent back to the user's browser. Laravel provides several different ways to return responses. The most basic response is returning a string from a route or controller. The framework will automatically convert the string into a full HTTP response:

```php	
Route::get('/', function() {
return'Hello World';
});
```
In addition to returning strings from your routes and controllers, you may also return arrays. The framework will automatically convert the array into a JSON response:

```php	
Route::get('/', function() {
return [1, 2, 3];
});
```
> **Note**  
>  Did you know you can also return [Eloquent collections](/docs/master/eloquent-collections) from your routes or controllers? They will automatically be converted to JSON. Give it a shot!

#### [Response Objects](#response-objects)
Typically, you won't just be returning simple strings or arrays from your route actions. Instead, you will be returning full `Illuminate\Http\Response` instances or [views](/docs/master/views).

Returning a full `Response` instance allows you to customize the response's HTTP status code and headers. A `Response` instance inherits from the `Symfony\Component\HttpFoundation\Response` class, which provides a variety of methods for building HTTP responses:

```php	
Route::get('/home', function() {
returnresponse('Hello World',200)
->header('Content-Type', 'text/plain');
});
```
#### [Eloquent Models & Collections](#eloquent-models-and-collections)
You may also return [Eloquent ORM](/docs/master/eloquent) models and collections directly from your routes and controllers. When you do, Laravel will automatically convert the models and collections to JSON responses while respecting the model's [hidden attributes](/docs/master/eloquent-serialization#hiding-attributes-from-json):

```php	
use App\Models\User;
 
Route::get('/user/{user}', function(User$user) {
return$user;
});
```
### [Attaching Headers To Responses](#attaching-headers-to-responses)
Keep in mind that most response methods are chainable, allowing for the fluent construction of response instances. For example, you may use the `header` method to add a series of headers to the response before sending it back to the user:

```php	
returnresponse($content)
->header('Content-Type', $type)
->header('X-Header-One', 'Header Value')
->header('X-Header-Two', 'Header Value');
```
Or, you may use the `withHeaders` method to specify an array of headers to be added to the response:

```php	
returnresponse($content)
->withHeaders([
'Content-Type'=>$type,
'X-Header-One'=>'Header Value',
'X-Header-Two'=>'Header Value',
 ]);
```
#### [Cache Control Middleware](#cache-control-middleware)
Laravel includes a `cache.headers` middleware, which may be used to quickly set the `Cache-Control` header for a group of routes. Directives should be provided using the "snake case" equivalent of the corresponding cache-control directive and should be separated by a semicolon. If `etag` is specified in the list of directives, an MD5 hash of the response content will automatically be set as the ETag identifier:

```php	
Route::middleware('cache.headers:public;max_age=2628000;etag')->group(function() {
Route::get('/privacy', function() {
// ...
 });
 
Route::get('/terms', function() {
// ...
 });
});
```
### [Attaching Cookies To Responses](#attaching-cookies-to-responses)
You may attach a cookie to an outgoing `Illuminate\Http\Response` instance using the `cookie` method. You should pass the name, value, and the number of minutes the cookie should be considered valid to this method:

```php	
returnresponse('Hello World')->cookie(
'name', 'value', $minutes
);
```
The `cookie` method also accepts a few more arguments which are used less frequently. Generally, these arguments have the same purpose and meaning as the arguments that would be given to PHP's native [setcookie](https://secure.php.net/manual/en/function.setcookie.php) method:

```php	
returnresponse('Hello World')->cookie(
'name', 'value', $minutes, $path, $domain, $secure, $httpOnly
);
```
If you would like to ensure that a cookie is sent with the outgoing response but you do not yet have an instance of that response, you can use the `Cookie` facade to "queue" cookies for attachment to the response when it is sent. The `queue` method accepts the arguments needed to create a cookie instance. These cookies will be attached to the outgoing response before it is sent to the browser:

```php	
use Illuminate\Support\Facades\Cookie;
 
Cookie::queue('name', 'value', $minutes);
```
#### [Generating Cookie Instances](#generating-cookie-instances)
If you would like to generate a `Symfony\Component\HttpFoundation\Cookie` instance that can be attached to a response instance at a later time, you may use the global `cookie` helper. This cookie will not be sent back to the client unless it is attached to a response instance:

```php	
$cookie=cookie('name','value',$minutes);
 
returnresponse('Hello World')->cookie($cookie);
```
#### [Expiring Cookies Early](#expiring-cookies-early)
You may remove a cookie by expiring it via the `withoutCookie` method of an outgoing response:

```php	
returnresponse('Hello World')->withoutCookie('name');
```
If you do not yet have an instance of the outgoing response, you may use the `Cookie` facade's `expire` method to expire a cookie:

```php	
Cookie::expire('name');
```
### [Cookies & Encryption](#cookies-and-encryption)
By default, all cookies generated by Laravel are encrypted and signed so that they can't be modified or read by the client. If you would like to disable encryption for a subset of cookies generated by your application, you may use the `$except` property of the `App\Http\Middleware\EncryptCookies` middleware, which is located in the `app/Http/Middleware` directory:

```php	
/**
 * The names of the cookies that should not be encrypted.
 *
 * @vararray
*/
protected$except= [
'cookie_name',
];
```
## [Redirects](#redirects)
Redirect responses are instances of the `Illuminate\Http\RedirectResponse` class, and contain the proper headers needed to redirect the user to another URL. There are several ways to generate a `RedirectResponse` instance. The simplest method is to use the global `redirect` helper:

```php	
Route::get('/dashboard', function() {
returnredirect('home/dashboard');
});
```
Sometimes you may wish to redirect the user to their previous location, such as when a submitted form is invalid. You may do so by using the global `back` helper function. Since this feature utilizes the [session](/docs/master/session), make sure the route calling the `back` function is using the `web` middleware group:

```php	
Route::post('/user/profile', function() {
// Validate the request...
 
returnback()->withInput();
});
```
### [Redirecting To Named Routes](#redirecting-named-routes)
When you call the `redirect` helper with no parameters, an instance of `Illuminate\Routing\Redirector` is returned, allowing you to call any method on the `Redirector` instance. For example, to generate a `RedirectResponse` to a named route, you may use the `route` method:

```php	
returnredirect()->route('login');
```
If your route has parameters, you may pass them as the second argument to the `route` method:

```php	
// For a route with the following URI: /profile/{id}
 
returnredirect()->route('profile', ['id'=>1]);
```
#### [Populating Parameters Via Eloquent Models](#populating-parameters-via-eloquent-models)
If you are redirecting to a route with an "ID" parameter that is being populated from an Eloquent model, you may pass the model itself. The ID will be extracted automatically:

```php	
// For a route with the following URI: /profile/{id}
 
returnredirect()->route('profile', [$user]);
```
If you would like to customize the value that is placed in the route parameter, you can specify the column in the route parameter definition (`/profile/{id:slug}`) or you can override the `getRouteKey` method on your Eloquent model:

```php	
/**
 * Get the value of the model's route key.
*/
publicfunctiongetRouteKey():mixed
{
return$this->slug;
}
```
### [Redirecting To Controller Actions](#redirecting-controller-actions)
You may also generate redirects to [controller actions](/docs/master/controllers). To do so, pass the controller and action name to the `action` method:

```php	
use App\Http\Controllers\UserController;
 
returnredirect()->action([UserController::class, 'index']);
```
If your controller route requires parameters, you may pass them as the second argument to the `action` method:

```php	
returnredirect()->action(
 [UserController::class, 'profile'], ['id'=>1]
);
```
### [Redirecting To External Domains](#redirecting-external-domains)
Sometimes you may need to redirect to a domain outside of your application. You may do so by calling the `away` method, which creates a `RedirectResponse` without any additional URL encoding, validation, or verification:

```php	
returnredirect()->away('https://www.google.com');
```
### [Redirecting With Flashed Session Data](#redirecting-with-flashed-session-data)
Redirecting to a new URL and [flashing data to the session](/docs/master/session#flash-data) are usually done at the same time. Typically, this is done after successfully performing an action when you flash a success message to the session. For convenience, you may create a `RedirectResponse` instance and flash data to the session in a single, fluent method chain:

```php	
Route::post('/user/profile', function() {
// ...
 
returnredirect('dashboard')->with('status', 'Profile updated!');
});
```
After the user is redirected, you may display the flashed message from the [session](/docs/master/session). For example, using [Blade syntax](/docs/master/blade):

```php	
@if (session('status'))
<divclass="alert alert-success">
 {{ session('status') }}
</div>
@endif
```
#### [Redirecting With Input](#redirecting-with-input)
You may use the `withInput` method provided by the `RedirectResponse` instance to flash the current request's input data to the session before redirecting the user to a new location. This is typically done if the user has encountered a validation error. Once the input has been flashed to the session, you may easily [retrieve it](/docs/master/requests#retrieving-old-input) during the next request to repopulate the form:

```php	
returnback()->withInput();
```
## [Other Response Types](#other-response-types)
The `response` helper may be used to generate other types of response instances. When the `response` helper is called without arguments, an implementation of the `Illuminate\Contracts\Routing\ResponseFactory`[contract](/docs/master/contracts) is returned. This contract provides several helpful methods for generating responses.

### [View Responses](#view-responses)
If you need control over the response's status and headers but also need to return a [view](/docs/master/views) as the response's content, you should use the `view` method:

```php	
returnresponse()
->view('hello', $data, 200)
->header('Content-Type', $type);
```
Of course, if you do not need to pass a custom HTTP status code or custom headers, you may use the global `view` helper function.

### [JSON Responses](#json-responses)
The `json` method will automatically set the `Content-Type` header to `application/json`, as well as convert the given array to JSON using the `json_encode` PHP function:

```php	
returnresponse()->json([
'name'=>'Abigail',
'state'=>'CA',
]);
```
If you would like to create a JSONP response, you may use the `json` method in combination with the `withCallback` method:

```php	
returnresponse()
->json(['name'=>'Abigail', 'state'=>'CA'])
->withCallback($request->input('callback'));
```
### [File Downloads](#file-downloads)
The `download` method may be used to generate a response that forces the user's browser to download the file at the given path. The `download` method accepts a filename as the second argument to the method, which will determine the filename that is seen by the user downloading the file. Finally, you may pass an array of HTTP headers as the third argument to the method:

```php	
returnresponse()->download($pathToFile);
 
returnresponse()->download($pathToFile, $name, $headers);
```
> **Warning**  
>  Symfony HttpFoundation, which manages file downloads, requires the file being downloaded to have an ASCII filename.

#### [Streamed Downloads](#streamed-downloads)
Sometimes you may wish to turn the string response of a given operation into a downloadable response without having to write the contents of the operation to disk. You may use the `streamDownload` method in this scenario. This method accepts a callback, filename, and an optional array of headers as its arguments:

```php	
use App\Services\GitHub;
 
returnresponse()->streamDownload(function() {
echoGitHub::api('repo')
->contents()
->readme('laravel', 'laravel')['contents'];
}, 'laravel-readme.md');
```
### [File Responses](#file-responses)
The `file` method may be used to display a file, such as an image or PDF, directly in the user's browser instead of initiating a download. This method accepts the path to the file as its first argument and an array of headers as its second argument:

```php	
returnresponse()->file($pathToFile);
 
returnresponse()->file($pathToFile, $headers);
```
## [Response Macros](#response-macros)
If you would like to define a custom response that you can re-use in a variety of your routes and controllers, you may use the `macro` method on the `Response` facade. Typically, you should call this method from the `boot` method of one of your application's [service providers](/docs/master/providers), such as the `App\Providers\AppServiceProvider` service provider:

```php	
<?php
 
namespace App\Providers;
 
use Illuminate\Support\Facades\Response;
use Illuminate\Support\ServiceProvider;
 
classAppServiceProviderextendsServiceProvider
{
/**
 * Bootstrap any application services.
*/
publicfunctionboot():void
 {
Response::macro('caps', function(string$value) {
returnResponse::make(strtoupper($value));
 });
 }
}
```
The `macro` function accepts a name as its first argument and a closure as its second argument. The macro's closure will be executed when calling the macro name from a `ResponseFactory` implementation or the `response` helper:

```php	
returnresponse()->caps('foo');
```
