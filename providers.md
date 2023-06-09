# Service Providers
- [Introduction](#introduction)
- [Writing Service Providers](#writing-service-providers)
    - [The Register Method](#the-register-method)

    - [The Boot Method](#the-boot-method)


- [Registering Providers](#registering-providers)
- [Deferred Providers](#deferred-providers)

## [Introduction](#introduction)
Service providers are the central place of all Laravel application bootstrapping. Your own application, as well as all of Laravel's core services, are bootstrapped via service providers.

But, what do we mean by "bootstrapped"? In general, we mean **registering** things, including registering service container bindings, event listeners, middleware, and even routes. Service providers are the central place to configure your application.

If you open the `config/app.php` file included with Laravel, you will see a `providers` array. These are all of the service provider classes that will be loaded for your application. By default, a set of Laravel core service providers are listed in this array. These providers bootstrap the core Laravel components, such as the mailer, queue, cache, and others. Many of these providers are "deferred" providers, meaning they will not be loaded on every request, but only when the services they provide are actually needed.

In this overview, you will learn how to write your own service providers and register them with your Laravel application.

> **Note**  
>  If you would like to learn more about how Laravel handles requests and works internally, check out our documentation on the Laravel [request lifecycle](/docs/master/lifecycle).

## [Writing Service Providers](#writing-service-providers)
All service providers extend the `Illuminate\Support\ServiceProvider` class. Most service providers contain a `register` and a `boot` method. Within the `register` method, you should **only bind things into the [service container](/docs/master/container)**. You should never attempt to register any event listeners, routes, or any other piece of functionality within the `register` method.

The Artisan CLI can generate a new provider via the `make:provider` command:

```shell	
php artisanmake:providerRiakServiceProvider
```
### [The Register Method](#the-register-method)
As mentioned previously, within the `register` method, you should only bind things into the [service container](/docs/master/container). You should never attempt to register any event listeners, routes, or any other piece of functionality within the `register` method. Otherwise, you may accidentally use a service that is provided by a service provider which has not loaded yet.

Let's take a look at a basic service provider. Within any of your service provider methods, you always have access to the `$app` property which provides access to the service container:

```php	
<?php
 
namespace App\Providers;
 
use App\Services\Riak\Connection;
use Illuminate\Contracts\Foundation\Application;
use Illuminate\Support\ServiceProvider;
 
classRiakServiceProviderextendsServiceProvider
{
/**
 * Register any application services.
*/
publicfunctionregister():void
 {
$this->app->singleton(Connection::class, function(Application$app) {
returnnewConnection(config('riak'));
 });
 }
}
```
This service provider only defines a `register` method, and uses that method to define an implementation of `App\Services\Riak\Connection` in the service container. If you're not yet familiar with Laravel's service container, check out [its documentation](/docs/master/container).

#### [The `bindings` And `singletons` Properties](#the-bindings-and-singletons-properties)
If your service provider registers many simple bindings, you may wish to use the `bindings` and `singletons` properties instead of manually registering each container binding. When the service provider is loaded by the framework, it will automatically check for these properties and register their bindings:

```php	
<?php
 
namespace App\Providers;
 
use App\Contracts\DowntimeNotifier;
use App\Contracts\ServerProvider;
use App\Services\DigitalOceanServerProvider;
use App\Services\PingdomDowntimeNotifier;
use App\Services\ServerToolsProvider;
use Illuminate\Support\ServiceProvider;
 
classAppServiceProviderextendsServiceProvider
{
/**
 * All of the container bindings that should be registered.
 *
 * @vararray
*/
public$bindings= [
ServerProvider::class=>DigitalOceanServerProvider::class,
 ];
 
/**
 * All of the container singletons that should be registered.
 *
 * @vararray
*/
public$singletons= [
DowntimeNotifier::class=>PingdomDowntimeNotifier::class,
ServerProvider::class=>ServerToolsProvider::class,
 ];
}
```
### [The Boot Method](#the-boot-method)
So, what if we need to register a [view composer](/docs/master/views#view-composers) within our service provider? This should be done within the `boot` method. **This method is called after all other service providers have been registered**, meaning you have access to all other services that have been registered by the framework:

```php	
<?php
 
namespace App\Providers;
 
use Illuminate\Support\Facades\View;
use Illuminate\Support\ServiceProvider;
 
classComposerServiceProviderextendsServiceProvider
{
/**
 * Bootstrap any application services.
*/
publicfunctionboot():void
 {
View::composer('view', function() {
// ...
 });
 }
}
```
#### [Boot Method Dependency Injection](#boot-method-dependency-injection)
You may type-hint dependencies for your service provider's `boot` method. The [service container](/docs/master/container) will automatically inject any dependencies you need:

```php	
use Illuminate\Contracts\Routing\ResponseFactory;
 
/**
 * Bootstrap any application services.
*/
publicfunctionboot(ResponseFactory$response):void
{
$response->macro('serialized', function(mixed$value) {
// ...
 });
}
```
## [Registering Providers](#registering-providers)
All service providers are registered in the `config/app.php` configuration file. This file contains a `providers` array where you can list the class names of your service providers. By default, a set of Laravel core service providers are listed in this array. These providers bootstrap the core Laravel components, such as the mailer, queue, cache, and others.

To register your provider, add it to the array:

```php	
'providers'=> [
// Other Service Providers
 
 App\Providers\ComposerServiceProvider::class,
],
```
## [Deferred Providers](#deferred-providers)
If your provider is **only** registering bindings in the [service container](/docs/master/container), you may choose to defer its registration until one of the registered bindings is actually needed. Deferring the loading of such a provider will improve the performance of your application, since it is not loaded from the filesystem on every request.

Laravel compiles and stores a list of all of the services supplied by deferred service providers, along with the name of its service provider class. Then, only when you attempt to resolve one of these services does Laravel load the service provider.

To defer the loading of a provider, implement the `\Illuminate\Contracts\Support\DeferrableProvider` interface and define a `provides` method. The `provides` method should return the service container bindings registered by the provider:

```php	
<?php
 
namespace App\Providers;
 
use App\Services\Riak\Connection;
use Illuminate\Contracts\Foundation\Application;
use Illuminate\Contracts\Support\DeferrableProvider;
use Illuminate\Support\ServiceProvider;
 
classRiakServiceProviderextendsServiceProviderimplementsDeferrableProvider
{
/**
 * Register any application services.
*/
publicfunctionregister():void
 {
$this->app->singleton(Connection::class, function(Application$app) {
returnnewConnection($app['config']['riak']);
 });
 }
 
/**
 * Get the services provided by the provider.
 *
 * @returnarray<int, string>
*/
publicfunctionprovides():array
 {
return [Connection::class];
 }
}
```
