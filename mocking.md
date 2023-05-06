# Mocking
- [Introduction](#introduction)
- [Mocking Objects](#mocking-objects)
- [Mocking Facades](#mocking-facades)
    - [Facade Spies](#facade-spies)


- [Interacting With Time](#interacting-with-time)

## [Introduction](#introduction)
When testing Laravel applications, you may wish to "mock" certain aspects of your application so they are not actually executed during a given test. For example, when testing a controller that dispatches an event, you may wish to mock the event listeners so they are not actually executed during the test. This allows you to only test the controller's HTTP response without worrying about the execution of the event listeners since the event listeners can be tested in their own test case.

Laravel provides helpful methods for mocking events, jobs, and other facades out of the box. These helpers primarily provide a convenience layer over Mockery so you do not have to manually make complicated Mockery method calls.

## [Mocking Objects](#mocking-objects)
When mocking an object that is going to be injected into your application via Laravel's [service container](/docs/master/container), you will need to bind your mocked instance into the container as an `instance` binding. This will instruct the container to use your mocked instance of the object instead of constructing the object itself:

```php	
use App\Service;
useMockery;
use Mockery\MockInterface;
 
publicfunctiontest_something_can_be_mocked():void
{
$this->instance(
Service::class,
Mockery::mock(Service::class, function(MockInterface$mock) {
$mock->shouldReceive('process')->once();
 })
 );
}
```
In order to make this more convenient, you may use the `mock` method that is provided by Laravel's base test case class. For example, the following example is equivalent to the example above:

```php	
use App\Service;
use Mockery\MockInterface;
 
$mock=$this->mock(Service::class, function(MockInterface$mock) {
$mock->shouldReceive('process')->once();
});
```
You may use the `partialMock` method when you only need to mock a few methods of an object. The methods that are not mocked will be executed normally when called:

```php	
use App\Service;
use Mockery\MockInterface;
 
$mock=$this->partialMock(Service::class, function(MockInterface$mock) {
$mock->shouldReceive('process')->once();
});
```
Similarly, if you want to [spy](http://docs.mockery.io/en/latest/reference/spies.html) on an object, Laravel's base test case class offers a `spy` method as a convenient wrapper around the `Mockery::spy` method. Spies are similar to mocks; however, spies record any interaction between the spy and the code being tested, allowing you to make assertions after the code is executed:

```php	
use App\Service;
 
$spy=$this->spy(Service::class);
 
// ...
 
$spy->shouldHaveReceived('process');
```
## [Mocking Facades](#mocking-facades)
Unlike traditional static method calls, [facades](/docs/master/facades) (including [real-time facades](/docs/master/facades#real-time-facades)) may be mocked. This provides a great advantage over traditional static methods and grants you the same testability that you would have if you were using traditional dependency injection. When testing, you may often want to mock a call to a Laravel facade that occurs in one of your controllers. For example, consider the following controller action:

```php	
<?php
 
namespace App\Http\Controllers;
 
use Illuminate\Support\Facades\Cache;
 
classUserControllerextendsController
{
/**
 * Retrieve a list of all users of the application.
*/
publicfunctionindex():array
 {
$value=Cache::get('key');
 
return [
// ...
 ];
 }
}
```
We can mock the call to the `Cache` facade by using the `shouldReceive` method, which will return an instance of a [Mockery](https://github.com/padraic/mockery) mock. Since facades are actually resolved and managed by the Laravel [service container](/docs/master/container), they have much more testability than a typical static class. For example, let's mock our call to the `Cache` facade's `get` method:

```php	
<?php
 
namespace Tests\Feature;
 
use Illuminate\Support\Facades\Cache;
use Tests\TestCase;
 
classUserControllerTestextendsTestCase
{
publicfunctiontest_get_index():void
 {
Cache::shouldReceive('get')
->once()
->with('key')
->andReturn('value');
 
$response=$this->get('/users');
 
// ...
 }
}
```
> **Warning**  
>  You should not mock the `Request` facade. Instead, pass the input you desire into the [HTTP testing methods](/docs/master/http-tests) such as `get` and `post` when running your test. Likewise, instead of mocking the `Config` facade, call the `Config::set` method in your tests.

### [Facade Spies](#facade-spies)
If you would like to [spy](http://docs.mockery.io/en/latest/reference/spies.html) on a facade, you may call the `spy` method on the corresponding facade. Spies are similar to mocks; however, spies record any interaction between the spy and the code being tested, allowing you to make assertions after the code is executed:

```php	
use Illuminate\Support\Facades\Cache;
 
publicfunctiontest_values_are_be_stored_in_cache():void
{
Cache::spy();
 
$response=$this->get('/');
 
$response->assertStatus(200);
 
Cache::shouldHaveReceived('put')->once()->with('name', 'Taylor', 10);
}
```
## [Interacting With Time](#interacting-with-time)
When testing, you may occasionally need to modify the time returned by helpers such as `now` or `Illuminate\Support\Carbon::now()`. Thankfully, Laravel's base feature test class includes helpers that allow you to manipulate the current time:

```php	
use Illuminate\Support\Carbon;
 
publicfunctiontest_time_can_be_manipulated():void
{
// Travel into the future...
$this->travel(5)->milliseconds();
$this->travel(5)->seconds();
$this->travel(5)->minutes();
$this->travel(5)->hours();
$this->travel(5)->days();
$this->travel(5)->weeks();
$this->travel(5)->years();
 
// Freeze time and resume normal time after executing closure...
$this->freezeTime(function(Carbon$time) {
// ...
 });
 
// Travel into the past...
$this->travel(-5)->hours();
 
// Travel to an explicit time...
$this->travelTo(now()->subHours(6));
 
// Return back to the present time...
$this->travelBack();
}
```
