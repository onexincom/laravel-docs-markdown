# Contracts
- [Introduction](#introduction)
    - [Contracts Vs. Facades](#contracts-vs-facades)


- [When To Use Contracts](#when-to-use-contracts)
- [How To Use Contracts](#how-to-use-contracts)
- [Contract Reference](#contract-reference)

## [Introduction](#introduction)
Laravel's "contracts" are a set of interfaces that define the core services provided by the framework. For example, an `Illuminate\Contracts\Queue\Queue` contract defines the methods needed for queueing jobs, while the `Illuminate\Contracts\Mail\Mailer` contract defines the methods needed for sending e-mail.

Each contract has a corresponding implementation provided by the framework. For example, Laravel provides a queue implementation with a variety of drivers, and a mailer implementation that is powered by [Symfony Mailer](https://symfony.com/doc/6.0/mailer.html).

All of the Laravel contracts live in [their own GitHub repository](https://github.com/illuminate/contracts). This provides a quick reference point for all available contracts, as well as a single, decoupled package that may be utilized when building packages that interact with Laravel services.

### [Contracts Vs. Facades](#contracts-vs-facades)
Laravel's [facades](/docs/master/facades) and helper functions provide a simple way of utilizing Laravel's services without needing to type-hint and resolve contracts out of the service container. In most cases, each facade has an equivalent contract.

Unlike facades, which do not require you to require them in your class' constructor, contracts allow you to define explicit dependencies for your classes. Some developers prefer to explicitly define their dependencies in this way and therefore prefer to use contracts, while other developers enjoy the convenience of facades. **In general, most applications can use facades without issue during development.**

## [When To Use Contracts](#when-to-use-contracts)
The decision to use contracts or facades will come down to personal taste and the tastes of your development team. Both contracts and facades can be used to create robust, well-tested Laravel applications. Contracts and facades are not mutually exclusive. Some parts of your applications may use facades while others depend on contracts. As long as you are keeping your class' responsibilities focused, you will notice very few practical differences between using contracts and facades.

In general, most applications can use facades without issue during development. If you are building a package that integrates with multiple PHP frameworks you may wish to use the `illuminate/contracts` package to define your integration with Laravel's services without the need to require Laravel's concrete implementations in your package's `composer.json` file.

## [How To Use Contracts](#how-to-use-contracts)
So, how do you get an implementation of a contract? It's actually quite simple.

Many types of classes in Laravel are resolved through the [service container](/docs/master/container), including controllers, event listeners, middleware, queued jobs, and even route closures. So, to get an implementation of a contract, you can just "type-hint" the interface in the constructor of the class being resolved.

For example, take a look at this event listener:

```php	
<?php
 
namespace App\Listeners;
 
use App\Events\OrderWasPlaced;
use App\Models\User;
use Illuminate\Contracts\Redis\Factory;
 
classCacheOrderInformation
{
/**
 * Create a new event handler instance.
*/
publicfunction__construct(
protectedFactory$redis,
) {}
 
/**
 * Handle the event.
*/
publicfunctionhandle(OrderWasPlaced$event):void
 {
// ...
 }
}
```
When the event listener is resolved, the service container will read the type-hints on the constructor of the class, and inject the appropriate value. To learn more about registering things in the service container, check out [its documentation](/docs/master/container).

## [Contract Reference](#contract-reference)
This table provides a quick reference to all of the Laravel contracts and their equivalent facades:

  
| Contract | References Facade |   
| ---- | ---- |   
| [Illuminate\Contracts\Auth\Access\Authorizable](https://github.com/illuminate/contracts/blob/master/Auth/Access/Authorizable.php) |    |   
| [Illuminate\Contracts\Auth\Access\Gate](https://github.com/illuminate/contracts/blob/master/Auth/Access/Gate.php) | `Gate` |   
| [Illuminate\Contracts\Auth\Authenticatable](https://github.com/illuminate/contracts/blob/master/Auth/Authenticatable.php) |    |   
| [Illuminate\Contracts\Auth\CanResetPassword](https://github.com/illuminate/contracts/blob/master/Auth/CanResetPassword.php) |   |   
| [Illuminate\Contracts\Auth\Factory](https://github.com/illuminate/contracts/blob/master/Auth/Factory.php) | `Auth` |   
| [Illuminate\Contracts\Auth\Guard](https://github.com/illuminate/contracts/blob/master/Auth/Guard.php) | `Auth::guard()` |   
| [Illuminate\Contracts\Auth\PasswordBroker](https://github.com/illuminate/contracts/blob/master/Auth/PasswordBroker.php) | `Password::broker()` |   
| [Illuminate\Contracts\Auth\PasswordBrokerFactory](https://github.com/illuminate/contracts/blob/master/Auth/PasswordBrokerFactory.php) | `Password` |   
| [Illuminate\Contracts\Auth\StatefulGuard](https://github.com/illuminate/contracts/blob/master/Auth/StatefulGuard.php) |   |   
| [Illuminate\Contracts\Auth\SupportsBasicAuth](https://github.com/illuminate/contracts/blob/master/Auth/SupportsBasicAuth.php) |   |   
| [Illuminate\Contracts\Auth\UserProvider](https://github.com/illuminate/contracts/blob/master/Auth/UserProvider.php) |   |   
| [Illuminate\Contracts\Bus\Dispatcher](https://github.com/illuminate/contracts/blob/master/Bus/Dispatcher.php) | `Bus` |   
| [Illuminate\Contracts\Bus\QueueingDispatcher](https://github.com/illuminate/contracts/blob/master/Bus/QueueingDispatcher.php) | `Bus::dispatchToQueue()` |   
| [Illuminate\Contracts\Broadcasting\Factory](https://github.com/illuminate/contracts/blob/master/Broadcasting/Factory.php) | `Broadcast` |   
| [Illuminate\Contracts\Broadcasting\Broadcaster](https://github.com/illuminate/contracts/blob/master/Broadcasting/Broadcaster.php) | `Broadcast::connection()` |   
| [Illuminate\Contracts\Broadcasting\ShouldBroadcast](https://github.com/illuminate/contracts/blob/master/Broadcasting/ShouldBroadcast.php) |   |   
| [Illuminate\Contracts\Broadcasting\ShouldBroadcastNow](https://github.com/illuminate/contracts/blob/master/Broadcasting/ShouldBroadcastNow.php) |   |   
| [Illuminate\Contracts\Cache\Factory](https://github.com/illuminate/contracts/blob/master/Cache/Factory.php) | `Cache` |   
| [Illuminate\Contracts\Cache\Lock](https://github.com/illuminate/contracts/blob/master/Cache/Lock.php) |   |   
| [Illuminate\Contracts\Cache\LockProvider](https://github.com/illuminate/contracts/blob/master/Cache/LockProvider.php) |   |   
| [Illuminate\Contracts\Cache\Repository](https://github.com/illuminate/contracts/blob/master/Cache/Repository.php) | `Cache::driver()` |   
| [Illuminate\Contracts\Cache\Store](https://github.com/illuminate/contracts/blob/master/Cache/Store.php) |   |   
| [Illuminate\Contracts\Config\Repository](https://github.com/illuminate/contracts/blob/master/Config/Repository.php) | `Config` |   
| [Illuminate\Contracts\Console\Application](https://github.com/illuminate/contracts/blob/master/Console/Application.php) |   |   
| [Illuminate\Contracts\Console\Kernel](https://github.com/illuminate/contracts/blob/master/Console/Kernel.php) | `Artisan` |   
| [Illuminate\Contracts\Container\Container](https://github.com/illuminate/contracts/blob/master/Container/Container.php) | `App` |   
| [Illuminate\Contracts\Cookie\Factory](https://github.com/illuminate/contracts/blob/master/Cookie/Factory.php) | `Cookie` |   
| [Illuminate\Contracts\Cookie\QueueingFactory](https://github.com/illuminate/contracts/blob/master/Cookie/QueueingFactory.php) | `Cookie::queue()` |   
| [Illuminate\Contracts\Database\ModelIdentifier](https://github.com/illuminate/contracts/blob/master/Database/ModelIdentifier.php) |   |   
| [Illuminate\Contracts\Debug\ExceptionHandler](https://github.com/illuminate/contracts/blob/master/Debug/ExceptionHandler.php) |   |   
| [Illuminate\Contracts\Encryption\Encrypter](https://github.com/illuminate/contracts/blob/master/Encryption/Encrypter.php) | `Crypt` |   
| [Illuminate\Contracts\Events\Dispatcher](https://github.com/illuminate/contracts/blob/master/Events/Dispatcher.php) | `Event` |   
| [Illuminate\Contracts\Filesystem\Cloud](https://github.com/illuminate/contracts/blob/master/Filesystem/Cloud.php) | `Storage::cloud()` |   
| [Illuminate\Contracts\Filesystem\Factory](https://github.com/illuminate/contracts/blob/master/Filesystem/Factory.php) | `Storage` |   
| [Illuminate\Contracts\Filesystem\Filesystem](https://github.com/illuminate/contracts/blob/master/Filesystem/Filesystem.php) | `Storage::disk()` |   
| [Illuminate\Contracts\Foundation\Application](https://github.com/illuminate/contracts/blob/master/Foundation/Application.php) | `App` |   
| [Illuminate\Contracts\Hashing\Hasher](https://github.com/illuminate/contracts/blob/master/Hashing/Hasher.php) | `Hash` |   
| [Illuminate\Contracts\Http\Kernel](https://github.com/illuminate/contracts/blob/master/Http/Kernel.php) |   |   
| [Illuminate\Contracts\Mail\MailQueue](https://github.com/illuminate/contracts/blob/master/Mail/MailQueue.php) | `Mail::queue()` |   
| [Illuminate\Contracts\Mail\Mailable](https://github.com/illuminate/contracts/blob/master/Mail/Mailable.php) |   |   
| [Illuminate\Contracts\Mail\Mailer](https://github.com/illuminate/contracts/blob/master/Mail/Mailer.php) | `Mail` |   
| [Illuminate\Contracts\Notifications\Dispatcher](https://github.com/illuminate/contracts/blob/master/Notifications/Dispatcher.php) | `Notification` |   
| [Illuminate\Contracts\Notifications\Factory](https://github.com/illuminate/contracts/blob/master/Notifications/Factory.php) | `Notification` |   
| [Illuminate\Contracts\Pagination\LengthAwarePaginator](https://github.com/illuminate/contracts/blob/master/Pagination/LengthAwarePaginator.php) |   |   
| [Illuminate\Contracts\Pagination\Paginator](https://github.com/illuminate/contracts/blob/master/Pagination/Paginator.php) |   |   
| [Illuminate\Contracts\Pipeline\Hub](https://github.com/illuminate/contracts/blob/master/Pipeline/Hub.php) |   |   
| [Illuminate\Contracts\Pipeline\Pipeline](https://github.com/illuminate/contracts/blob/master/Pipeline/Pipeline.php) |   |   
| [Illuminate\Contracts\Queue\EntityResolver](https://github.com/illuminate/contracts/blob/master/Queue/EntityResolver.php) |   |   
| [Illuminate\Contracts\Queue\Factory](https://github.com/illuminate/contracts/blob/master/Queue/Factory.php) | `Queue` |   
| [Illuminate\Contracts\Queue\Job](https://github.com/illuminate/contracts/blob/master/Queue/Job.php) |   |   
| [Illuminate\Contracts\Queue\Monitor](https://github.com/illuminate/contracts/blob/master/Queue/Monitor.php) | `Queue` |   
| [Illuminate\Contracts\Queue\Queue](https://github.com/illuminate/contracts/blob/master/Queue/Queue.php) | `Queue::connection()` |   
| [Illuminate\Contracts\Queue\QueueableCollection](https://github.com/illuminate/contracts/blob/master/Queue/QueueableCollection.php) |   |   
| [Illuminate\Contracts\Queue\QueueableEntity](https://github.com/illuminate/contracts/blob/master/Queue/QueueableEntity.php) |   |   
| [Illuminate\Contracts\Queue\ShouldQueue](https://github.com/illuminate/contracts/blob/master/Queue/ShouldQueue.php) |   |   
| [Illuminate\Contracts\Redis\Factory](https://github.com/illuminate/contracts/blob/master/Redis/Factory.php) | `Redis` |   
| [Illuminate\Contracts\Routing\BindingRegistrar](https://github.com/illuminate/contracts/blob/master/Routing/BindingRegistrar.php) | `Route` |   
| [Illuminate\Contracts\Routing\Registrar](https://github.com/illuminate/contracts/blob/master/Routing/Registrar.php) | `Route` |   
| [Illuminate\Contracts\Routing\ResponseFactory](https://github.com/illuminate/contracts/blob/master/Routing/ResponseFactory.php) | `Response` |   
| [Illuminate\Contracts\Routing\UrlGenerator](https://github.com/illuminate/contracts/blob/master/Routing/UrlGenerator.php) | `URL` |   
| [Illuminate\Contracts\Routing\UrlRoutable](https://github.com/illuminate/contracts/blob/master/Routing/UrlRoutable.php) |   |   
| [Illuminate\Contracts\Session\Session](https://github.com/illuminate/contracts/blob/master/Session/Session.php) | `Session::driver()` |   
| [Illuminate\Contracts\Support\Arrayable](https://github.com/illuminate/contracts/blob/master/Support/Arrayable.php) |   |   
| [Illuminate\Contracts\Support\Htmlable](https://github.com/illuminate/contracts/blob/master/Support/Htmlable.php) |   |   
| [Illuminate\Contracts\Support\Jsonable](https://github.com/illuminate/contracts/blob/master/Support/Jsonable.php) |   |   
| [Illuminate\Contracts\Support\MessageBag](https://github.com/illuminate/contracts/blob/master/Support/MessageBag.php) |   |   
| [Illuminate\Contracts\Support\MessageProvider](https://github.com/illuminate/contracts/blob/master/Support/MessageProvider.php) |   |   
| [Illuminate\Contracts\Support\Renderable](https://github.com/illuminate/contracts/blob/master/Support/Renderable.php) |   |   
| [Illuminate\Contracts\Support\Responsable](https://github.com/illuminate/contracts/blob/master/Support/Responsable.php) |   |   
| [Illuminate\Contracts\Translation\Loader](https://github.com/illuminate/contracts/blob/master/Translation/Loader.php) |   |   
| [Illuminate\Contracts\Translation\Translator](https://github.com/illuminate/contracts/blob/master/Translation/Translator.php) | `Lang` |   
| [Illuminate\Contracts\Validation\Factory](https://github.com/illuminate/contracts/blob/master/Validation/Factory.php) | `Validator` |   
| [Illuminate\Contracts\Validation\ImplicitRule](https://github.com/illuminate/contracts/blob/master/Validation/ImplicitRule.php) |   |   
| [Illuminate\Contracts\Validation\Rule](https://github.com/illuminate/contracts/blob/master/Validation/Rule.php) |   |   
| [Illuminate\Contracts\Validation\ValidatesWhenResolved](https://github.com/illuminate/contracts/blob/master/Validation/ValidatesWhenResolved.php) |   |   
| [Illuminate\Contracts\Validation\Validator](https://github.com/illuminate/contracts/blob/master/Validation/Validator.php) | `Validator::make()` |   
| [Illuminate\Contracts\View\Engine](https://github.com/illuminate/contracts/blob/master/View/Engine.php) |   |   
| [Illuminate\Contracts\View\Factory](https://github.com/illuminate/contracts/blob/master/View/Factory.php) | `View` |   
| [Illuminate\Contracts\View\View](https://github.com/illuminate/contracts/blob/master/View/View.php) | `View::make()` | 