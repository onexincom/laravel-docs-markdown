# Views
- [Introduction](#introduction)
    - [Writing Views In React / Vue](#writing-views-in-react-or-vue)


- [Creating & Rendering Views](#creating-and-rendering-views)
    - [Nested View Directories](#nested-view-directories)

    - [Creating The First Available View](#creating-the-first-available-view)

    - [Determining If A View Exists](#determining-if-a-view-exists)


- [Passing Data To Views](#passing-data-to-views)
    - [Sharing Data With All Views](#sharing-data-with-all-views)


- [View Composers](#view-composers)
    - [View Creators](#view-creators)


- [Optimizing Views](#optimizing-views)

## [Introduction](#introduction)
Of course, it's not practical to return entire HTML documents strings directly from your routes and controllers. Thankfully, views provide a convenient way to place all of our HTML in separate files.

Views separate your controller / application logic from your presentation logic and are stored in the `resources/views` directory. When using Laravel, view templates are usually written using the [Blade templating language](/docs/master/blade). A simple view might look something like this:

```blade	
<!-- View stored in resources/views/greeting.blade.php -->
 
<html>
<body>
<h1>Hello, {{$name}}</h1>
</body>
</html>
```
Since this view is stored at `resources/views/greeting.blade.php`, we may return it using the global `view` helper like so:

```php	
Route::get('/', function() {
returnview('greeting',['name'=>'James']);
});
```
> **Note**  
>  Looking for more information on how to write Blade templates? Check out the full [Blade documentation](/docs/master/blade) to get started.

### [Writing Views In React / Vue](#writing-views-in-react-or-vue)
Instead of writing their frontend templates in PHP via Blade, many developers have begun to prefer to write their templates using React or Vue. Laravel makes this painless thanks to [Inertia](https://inertiajs.com/), a library that makes it a cinch to tie your React / Vue frontend to your Laravel backend without the typical complexities of building an SPA.

Our Breeze and Jetstream [starter kits](/docs/master/starter-kits) give you a great starting point for your next Laravel application powered by Inertia. In addition, the [Laravel Bootcamp](https://bootcamp.laravel.com) provides a full demonstration of building a Laravel application powered by Inertia, including examples in Vue and React.

## [Creating & Rendering Views](#creating-and-rendering-views)
You may create a view by placing a file with the `.blade.php` extension in your application's `resources/views` directory. The `.blade.php` extension informs the framework that the file contains a [Blade template](/docs/master/blade). Blade templates contain HTML as well as Blade directives that allow you to easily echo values, create "if" statements, iterate over data, and more.

Once you have created a view, you may return it from one of your application's routes or controllers using the global `view` helper:

```php	
Route::get('/', function() {
returnview('greeting',['name'=>'James']);
});
```
Views may also be returned using the `View` facade:

```php	
use Illuminate\Support\Facades\View;
 
returnView::make('greeting', ['name'=>'James']);
```
As you can see, the first argument passed to the `view` helper corresponds to the name of the view file in the `resources/views` directory. The second argument is an array of data that should be made available to the view. In this case, we are passing the `name` variable, which is displayed in the view using [Blade syntax](/docs/master/blade).

### [Nested View Directories](#nested-view-directories)
Views may also be nested within subdirectories of the `resources/views` directory. "Dot" notation may be used to reference nested views. For example, if your view is stored at `resources/views/admin/profile.blade.php`, you may return it from one of your application's routes / controllers like so:

```php	
returnview('admin.profile',$data);
```
> **Warning**  
>  View directory names should not contain the `.` character.

### [Creating The First Available View](#creating-the-first-available-view)
Using the `View` facade's `first` method, you may create the first view that exists in a given array of views. This may be useful if your application or package allows views to be customized or overwritten:

```php	
use Illuminate\Support\Facades\View;
 
returnView::first(['custom.admin', 'admin'], $data);
```
### [Determining If A View Exists](#determining-if-a-view-exists)
If you need to determine if a view exists, you may use the `View` facade. The `exists` method will return `true` if the view exists:

```php	
use Illuminate\Support\Facades\View;
 
if (View::exists('emails.customer')) {
// ...
}
```
## [Passing Data To Views](#passing-data-to-views)
As you saw in the previous examples, you may pass an array of data to views to make that data available to the view:

```php	
returnview('greetings',['name'=>'Victoria']);
```
When passing information in this manner, the data should be an array with key / value pairs. After providing data to a view, you can then access each value within your view using the data's keys, such as `<?php echo $name; ?>`.

As an alternative to passing a complete array of data to the `view` helper function, you may use the `with` method to add individual pieces of data to the view. The `with` method returns an instance of the view object so that you can continue chaining methods before returning the view:

```php	
returnview('greeting')
->with('name', 'Victoria')
->with('occupation', 'Astronaut');
```
### [Sharing Data With All Views](#sharing-data-with-all-views)
Occasionally, you may need to share data with all views that are rendered by your application. You may do so using the `View` facade's `share` method. Typically, you should place calls to the `share` method within a service provider's `boot` method. You are free to add them to the `App\Providers\AppServiceProvider` class or generate a separate service provider to house them:

```php	
<?php
 
namespace App\Providers;
 
use Illuminate\Support\Facades\View;
 
classAppServiceProviderextendsServiceProvider
{
/**
 * Register any application services.
*/
publicfunctionregister():void
 {
// ...
 }
 
/**
 * Bootstrap any application services.
*/
publicfunctionboot():void
 {
View::share('key', 'value');
 }
}
```
## [View Composers](#view-composers)
View composers are callbacks or class methods that are called when a view is rendered. If you have data that you want to be bound to a view each time that view is rendered, a view composer can help you organize that logic into a single location. View composers may prove particularly useful if the same view is returned by multiple routes or controllers within your application and always needs a particular piece of data.

Typically, view composers will be registered within one of your application's [service providers](/docs/master/providers). In this example, we'll assume that we have created a new `App\Providers\ViewServiceProvider` to house this logic.

We'll use the `View` facade's `composer` method to register the view composer. Laravel does not include a default directory for class based view composers, so you are free to organize them however you wish. For example, you could create an `app/View/Composers` directory to house all of your application's view composers:

```php	
<?php
 
namespace App\Providers;
 
use App\View\Composers\ProfileComposer;
use Illuminate\Support\Facades;
use Illuminate\Support\ServiceProvider;
use Illuminate\View\View;
 
classViewServiceProviderextendsServiceProvider
{
/**
 * Register any application services.
*/
publicfunctionregister():void
 {
// ...
 }
 
/**
 * Bootstrap any application services.
*/
publicfunctionboot():void
 {
// Using class based composers...
 Facades\View::composer('profile', ProfileComposer::class);
 
// Using closure based composers...
 Facades\View::composer('welcome', function(View$view) {
// ...
 });
 
 Facades\View::composer('dashboard', function(View$view) {
// ...
 });
 }
}
```
> **Warning**  
>  Remember, if you create a new service provider to contain your view composer registrations, you will need to add the service provider to the `providers` array in the `config/app.php` configuration file.

Now that we have registered the composer, the `compose` method of the `App\View\Composers\ProfileComposer` class will be executed each time the `profile` view is being rendered. Let's take a look at an example of the composer class:

```php	
<?php
 
namespace App\View\Composers;
 
use App\Repositories\UserRepository;
use Illuminate\View\View;
 
classProfileComposer
{
/**
 * Create a new profile composer.
*/
publicfunction__construct(
protectedUserRepository$users,
) {}
 
/**
 * Bind data to the view.
*/
publicfunctioncompose(View$view):void
 {
$view->with('count', $this->users->count());
 }
}
```
As you can see, all view composers are resolved via the [service container](/docs/master/container), so you may type-hint any dependencies you need within a composer's constructor.

#### [Attaching A Composer To Multiple Views](#attaching-a-composer-to-multiple-views)
You may attach a view composer to multiple views at once by passing an array of views as the first argument to the `composer` method:

```php	
use App\Views\Composers\MultiComposer;
use Illuminate\Support\Facades\View;
 
View::composer(
 ['profile', 'dashboard'],
MultiComposer::class
);
```
The `composer` method also accepts the `*` character as a wildcard, allowing you to attach a composer to all views:

```php	
use Illuminate\Support\Facades;
use Illuminate\View\View;
 
Facades\View::composer('*', function(View$view) {
// ...
});
```
### [View Creators](#view-creators)
View "creators" are very similar to view composers; however, they are executed immediately after the view is instantiated instead of waiting until the view is about to render. To register a view creator, use the `creator` method:

```php	
use App\View\Creators\ProfileCreator;
use Illuminate\Support\Facades\View;
 
View::creator('profile', ProfileCreator::class);
```
## [Optimizing Views](#optimizing-views)
By default, Blade template views are compiled on demand. When a request is executed that renders a view, Laravel will determine if a compiled version of the view exists. If the file exists, Laravel will then determine if the uncompiled view has been modified more recently than the compiled view. If the compiled view either does not exist, or the uncompiled view has been modified, Laravel will recompile the view.

Compiling views during the request may have a small negative impact on performance, so Laravel provides the `view:cache` Artisan command to precompile all of the views utilized by your application. For increased performance, you may wish to run this command as part of your deployment process:

```shell	
php artisanview:cache
```
You may use the `view:clear` command to clear the view cache:

```shell	
php artisanview:clear
```
