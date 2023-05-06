# Authorization
- [Introduction](#introduction)
- [Gates](#gates)
    - [Writing Gates](#writing-gates)

    - [Authorizing Actions](#authorizing-actions-via-gates)

    - [Gate Responses](#gate-responses)

    - [Intercepting Gate Checks](#intercepting-gate-checks)

    - [Inline Authorization](#inline-authorization)


- [Creating Policies](#creating-policies)
    - [Generating Policies](#generating-policies)

    - [Registering Policies](#registering-policies)


- [Writing Policies](#writing-policies)
    - [Policy Methods](#policy-methods)

    - [Policy Responses](#policy-responses)

    - [Methods Without Models](#methods-without-models)

    - [Guest Users](#guest-users)

    - [Policy Filters](#policy-filters)


- [Authorizing Actions Using Policies](#authorizing-actions-using-policies)
    - [Via The User Model](#via-the-user-model)

    - [Via Controller Helpers](#via-controller-helpers)

    - [Via Middleware](#via-middleware)

    - [Via Blade Templates](#via-blade-templates)

    - [Supplying Additional Context](#supplying-additional-context)



## [Introduction](#introduction)
In addition to providing built-in [authentication](/docs/master/authentication) services, Laravel also provides a simple way to authorize user actions against a given resource. For example, even though a user is authenticated, they may not be authorized to update or delete certain Eloquent models or database records managed by your application. Laravel's authorization features provide an easy, organized way of managing these types of authorization checks.

Laravel provides two primary ways of authorizing actions: [gates](#gates) and [policies](#creating-policies). Think of gates and policies like routes and controllers. Gates provide a simple, closure-based approach to authorization while policies, like controllers, group logic around a particular model or resource. In this documentation, we'll explore gates first and then examine policies.

You do not need to choose between exclusively using gates or exclusively using policies when building an application. Most applications will most likely contain some mixture of gates and policies, and that is perfectly fine! Gates are most applicable to actions that are not related to any model or resource, such as viewing an administrator dashboard. In contrast, policies should be used when you wish to authorize an action for a particular model or resource.

## [Gates](#gates)
### [Writing Gates](#writing-gates)
> **Warning**  
>  Gates are a great way to learn the basics of Laravel's authorization features; however, when building robust Laravel applications you should consider using [policies](#creating-policies) to organize your authorization rules.

Gates are simply closures that determine if a user is authorized to perform a given action. Typically, gates are defined within the `boot` method of the `App\Providers\AuthServiceProvider` class using the `Gate` facade. Gates always receive a user instance as their first argument and may optionally receive additional arguments such as a relevant Eloquent model.

In this example, we'll define a gate to determine if a user can update a given `App\Models\Post` model. The gate will accomplish this by comparing the user's `id` against the `user_id` of the user that created the post:

```php	
use App\Models\Post;
use App\Models\User;
use Illuminate\Support\Facades\Gate;
 
/**
 * Register any authentication / authorization services.
*/
publicfunctionboot():void
{
$this->registerPolicies();
 
Gate::define('update-post', function(User$user, Post$post) {
return$user->id===$post->user_id;
 });
}
```
Like controllers, gates may also be defined using a class callback array:

```php	
use App\Policies\PostPolicy;
use Illuminate\Support\Facades\Gate;
 
/**
 * Register any authentication / authorization services.
*/
publicfunctionboot():void
{
$this->registerPolicies();
 
Gate::define('update-post', [PostPolicy::class, 'update']);
}
```
### [Authorizing Actions](#authorizing-actions-via-gates)
To authorize an action using gates, you should use the `allows` or `denies` methods provided by the `Gate` facade. Note that you are not required to pass the currently authenticated user to these methods. Laravel will automatically take care of passing the user into the gate closure. It is typical to call the gate authorization methods within your application's controllers before performing an action that requires authorization:

```php	
<?php
 
namespace App\Http\Controllers;
 
use App\Http\Controllers\Controller;
use App\Models\Post;
use Illuminate\Http\Request;
use Illuminate\Http\Response;
use Illuminate\Support\Facades\Gate;
 
classPostControllerextendsController
{
/**
 * Update the given post.
*/
publicfunctionupdate(Request$request, Post$post):Response
 {
if (!Gate::allows('update-post', $post)) {
abort(403);
 }
 
// Update the post...
 
returnresponse()->noContent();
 }
}
```
If you would like to determine if a user other than the currently authenticated user is authorized to perform an action, you may use the `forUser` method on the `Gate` facade:

```php	
if (Gate::forUser($user)->allows('update-post', $post)) {
// The user can update the post...
}
 
if (Gate::forUser($user)->denies('update-post', $post)) {
// The user can't update the post...
}
```
You may authorize multiple actions at a time using the `any` or `none` methods:

```php	
if (Gate::any(['update-post', 'delete-post'], $post)) {
// The user can update or delete the post...
}
 
if (Gate::none(['update-post', 'delete-post'], $post)) {
// The user can't update or delete the post...
}
```
#### [Authorizing Or Throwing Exceptions](#authorizing-or-throwing-exceptions)
If you would like to attempt to authorize an action and automatically throw an `Illuminate\Auth\Access\AuthorizationException` if the user is not allowed to perform the given action, you may use the `Gate` facade's `authorize` method. Instances of `AuthorizationException` are automatically converted to a 403 HTTP response by Laravel's exception handler:

```php	
Gate::authorize('update-post', $post);
 
// The action is authorized...
```
#### [Supplying Additional Context](#gates-supplying-additional-context)
The gate methods for authorizing abilities (`allows`, `denies`, `check`, `any`, `none`, `authorize`, `can`, `cannot`) and the authorization [Blade directives](#via-blade-templates) (`@can`, `@cannot`, `@canany`) can receive an array as their second argument. These array elements are passed as parameters to the gate closure, and can be used for additional context when making authorization decisions:

```php	
use App\Models\Category;
use App\Models\User;
use Illuminate\Support\Facades\Gate;
 
Gate::define('create-post', function(User$user, Category$category, bool$pinned) {
if (!$user->canPublishToGroup($category->group)) {
returnfalse;
 } elseif ($pinned&&!$user->canPinPosts()) {
returnfalse;
 }
 
returntrue;
});
 
if (Gate::check('create-post', [$category, $pinned])) {
// The user can create the post...
}
```
### [Gate Responses](#gate-responses)
So far, we have only examined gates that return simple boolean values. However, sometimes you may wish to return a more detailed response, including an error message. To do so, you may return an `Illuminate\Auth\Access\Response` from your gate:

```php	
use App\Models\User;
use Illuminate\Auth\Access\Response;
use Illuminate\Support\Facades\Gate;
 
Gate::define('edit-settings', function(User$user) {
return$user->isAdmin
?Response::allow()
:Response::deny('You must be an administrator.');
});
```
Even when you return an authorization response from your gate, the `Gate::allows` method will still return a simple boolean value; however, you may use the `Gate::inspect` method to get the full authorization response returned by the gate:

```php	
$response=Gate::inspect('edit-settings');
 
if ($response->allowed()) {
// The action is authorized...
} else {
echo$response->message();
}
```
When using the `Gate::authorize` method, which throws an `AuthorizationException` if the action is not authorized, the error message provided by the authorization response will be propagated to the HTTP response:

```php	
Gate::authorize('edit-settings');
 
// The action is authorized...
```
#### [Customizing The HTTP Response Status](#customising-gate-response-status)
When an action is denied via a Gate, a `403` HTTP response is returned; however, it can sometimes be useful to return an alternative HTTP status code. You may customize the HTTP status code returned for a failed authorization check using the `denyWithStatus` static constructor on the `Illuminate\Auth\Access\Response` class:

```php	
use App\Models\User;
use Illuminate\Auth\Access\Response;
use Illuminate\Support\Facades\Gate;
 
Gate::define('edit-settings', function(User$user) {
return$user->isAdmin
?Response::allow()
:Response::denyWithStatus(404);
});
```
Because hiding resources via a `404` response is such a common pattern for web applications, the `denyAsNotFound` method is offered for convenience:

```php	
use App\Models\User;
use Illuminate\Auth\Access\Response;
use Illuminate\Support\Facades\Gate;
 
Gate::define('edit-settings', function(User$user) {
return$user->isAdmin
?Response::allow()
:Response::denyAsNotFound();
});
```
### [Intercepting Gate Checks](#intercepting-gate-checks)
Sometimes, you may wish to grant all abilities to a specific user. You may use the `before` method to define a closure that is run before all other authorization checks:

```php	
use App\Models\User;
use Illuminate\Support\Facades\Gate;
 
Gate::before(function(User$user, string$ability) {
if ($user->isAdministrator()) {
returntrue;
 }
});
```
If the `before` closure returns a non-null result that result will be considered the result of the authorization check.

You may use the `after` method to define a closure to be executed after all other authorization checks:

```php	
use App\Models\User;
 
Gate::after(function(User$user, string$ability, bool|null$result, mixed$arguments) {
if ($user->isAdministrator()) {
returntrue;
 }
});
```
Similar to the `before` method, if the `after` closure returns a non-null result that result will be considered the result of the authorization check.

### [Inline Authorization](#inline-authorization)
Occasionally, you may wish to determine if the currently authenticated user is authorized to perform a given action without writing a dedicated gate that corresponds to the action. Laravel allows you to perform these types of "inline" authorization checks via the `Gate::allowIf` and `Gate::denyIf` methods:

```php	
use App\Models\User;
use Illuminate\Support\Facades\Gate;
 
Gate::allowIf(fn(User$user) => $user->isAdministrator());
 
Gate::denyIf(fn(User$user) => $user->banned());
```
If the action is not authorized or if no user is currently authenticated, Laravel will automatically throw an `Illuminate\Auth\Access\AuthorizationException` exception. Instances of `AuthorizationException` are automatically converted to a 403 HTTP response by Laravel's exception handler.

## [Creating Policies](#creating-policies)
### [Generating Policies](#generating-policies)
Policies are classes that organize authorization logic around a particular model or resource. For example, if your application is a blog, you may have a `App\Models\Post` model and a corresponding `App\Policies\PostPolicy` to authorize user actions such as creating or updating posts.

You may generate a policy using the `make:policy` Artisan command. The generated policy will be placed in the `app/Policies` directory. If this directory does not exist in your application, Laravel will create it for you:

```shell	
php artisanmake:policyPostPolicy
```
The `make:policy` command will generate an empty policy class. If you would like to generate a class with example policy methods related to viewing, creating, updating, and deleting the resource, you may provide a `--model` option when executing the command:

```shell	
php artisanmake:policyPostPolicy--model=Post
```
### [Registering Policies](#registering-policies)
Once the policy class has been created, it needs to be registered. Registering policies is how we can inform Laravel which policy to use when authorizing actions against a given model type.

The `App\Providers\AuthServiceProvider` included with fresh Laravel applications contains a `policies` property which maps your Eloquent models to their corresponding policies. Registering a policy will instruct Laravel which policy to utilize when authorizing actions against a given Eloquent model:

```php	
<?php
 
namespace App\Providers;
 
use App\Models\Post;
use App\Policies\PostPolicy;
use Illuminate\Foundation\Support\Providers\AuthServiceProvideras ServiceProvider;
use Illuminate\Support\Facades\Gate;
 
classAuthServiceProviderextendsServiceProvider
{
/**
 * The policy mappings for the application.
 *
 * @vararray
*/
protected$policies= [
Post::class=>PostPolicy::class,
 ];
 
/**
 * Register any application authentication / authorization services.
*/
publicfunctionboot():void
 {
$this->registerPolicies();
 
// ...
 }
}
```
#### [Policy Auto-Discovery](#policy-auto-discovery)
Instead of manually registering model policies, Laravel can automatically discover policies as long as the model and policy follow standard Laravel naming conventions. Specifically, the policies must be in a `Policies` directory at or above the directory that contains your models. So, for example, the models may be placed in the `app/Models` directory while the policies may be placed in the `app/Policies` directory. In this situation, Laravel will check for policies in `app/Models/Policies` then `app/Policies`. In addition, the policy name must match the model name and have a `Policy` suffix. So, a `User` model would correspond to a `UserPolicy` policy class.

If you would like to define your own policy discovery logic, you may register a custom policy discovery callback using the `Gate::guessPolicyNamesUsing` method. Typically, this method should be called from the `boot` method of your application's `AuthServiceProvider`:

```php	
use Illuminate\Support\Facades\Gate;
 
Gate::guessPolicyNamesUsing(function(string$modelClass) {
// Return the name of the policy class for the given model...
});
```
> **Warning**  
>  Any policies that are explicitly mapped in your `AuthServiceProvider` will take precedence over any potentially auto-discovered policies.

## [Writing Policies](#writing-policies)
### [Policy Methods](#policy-methods)
Once the policy class has been registered, you may add methods for each action it authorizes. For example, let's define an `update` method on our `PostPolicy` which determines if a given `App\Models\User` can update a given `App\Models\Post` instance.

The `update` method will receive a `User` and a `Post` instance as its arguments, and should return `true` or `false` indicating whether the user is authorized to update the given `Post`. So, in this example, we will verify that the user's `id` matches the `user_id` on the post:

```php	
<?php
 
namespace App\Policies;
 
use App\Models\Post;
use App\Models\User;
 
classPostPolicy
{
/**
 * Determine if the given post can be updated by the user.
*/
publicfunctionupdate(User$user, Post$post):bool
 {
return$user->id===$post->user_id;
 }
}
```
You may continue to define additional methods on the policy as needed for the various actions it authorizes. For example, you might define `view` or `delete` methods to authorize various `Post` related actions, but remember you are free to give your policy methods any name you like.

If you used the `--model` option when generating your policy via the Artisan console, it will already contain methods for the `viewAny`, `view`, `create`, `update`, `delete`, `restore`, and `forceDelete` actions.

> **Note**  
>  All policies are resolved via the Laravel [service container](/docs/master/container), allowing you to type-hint any needed dependencies in the policy's constructor to have them automatically injected.

### [Policy Responses](#policy-responses)
So far, we have only examined policy methods that return simple boolean values. However, sometimes you may wish to return a more detailed response, including an error message. To do so, you may return an `Illuminate\Auth\Access\Response` instance from your policy method:

```php	
use App\Models\Post;
use App\Models\User;
use Illuminate\Auth\Access\Response;
 
/**
 * Determine if the given post can be updated by the user.
*/
publicfunctionupdate(User$user, Post$post):Response
{
return$user->id===$post->user_id
?Response::allow()
:Response::deny('You do not own this post.');
}
```
When returning an authorization response from your policy, the `Gate::allows` method will still return a simple boolean value; however, you may use the `Gate::inspect` method to get the full authorization response returned by the gate:

```php	
use Illuminate\Support\Facades\Gate;
 
$response=Gate::inspect('update', $post);
 
if ($response->allowed()) {
// The action is authorized...
} else {
echo$response->message();
}
```
When using the `Gate::authorize` method, which throws an `AuthorizationException` if the action is not authorized, the error message provided by the authorization response will be propagated to the HTTP response:

```php	
Gate::authorize('update', $post);
 
// The action is authorized...
```
#### [Customizing The HTTP Response Status](#customising-policy-response-status)
When an action is denied via a policy method, a `403` HTTP response is returned; however, it can sometimes be useful to return an alternative HTTP status code. You may customize the HTTP status code returned for a failed authorization check using the `denyWithStatus` static constructor on the `Illuminate\Auth\Access\Response` class:

```php	
use App\Models\Post;
use App\Models\User;
use Illuminate\Auth\Access\Response;
 
/**
 * Determine if the given post can be updated by the user.
*/
publicfunctionupdate(User$user, Post$post):Response
{
return$user->id===$post->user_id
?Response::allow()
:Response::denyWithStatus(404);
}
```
Because hiding resources via a `404` response is such a common pattern for web applications, the `denyAsNotFound` method is offered for convenience:

```php	
use App\Models\Post;
use App\Models\User;
use Illuminate\Auth\Access\Response;
 
/**
 * Determine if the given post can be updated by the user.
*/
publicfunctionupdate(User$user, Post$post):Response
{
return$user->id===$post->user_id
?Response::allow()
:Response::denyAsNotFound();
}
```
### [Methods Without Models](#methods-without-models)
Some policy methods only receive an instance of the currently authenticated user. This situation is most common when authorizing `create` actions. For example, if you are creating a blog, you may wish to determine if a user is authorized to create any posts at all. In these situations, your policy method should only expect to receive a user instance:

```php	
/**
 * Determine if the given user can create posts.
*/
publicfunctioncreate(User$user):bool
{
return$user->role=='writer';
}
```
### [Guest Users](#guest-users)
By default, all gates and policies automatically return `false` if the incoming HTTP request was not initiated by an authenticated user. However, you may allow these authorization checks to pass through to your gates and policies by declaring an "optional" type-hint or supplying a `null` default value for the user argument definition:

```php	
<?php
 
namespace App\Policies;
 
use App\Models\Post;
use App\Models\User;
 
classPostPolicy
{
/**
 * Determine if the given post can be updated by the user.
*/
publicfunctionupdate(?User$user, Post$post):bool
 {
returnoptional($user)->id===$post->user_id;
 }
}
```
### [Policy Filters](#policy-filters)
For certain users, you may wish to authorize all actions within a given policy. To accomplish this, define a `before` method on the policy. The `before` method will be executed before any other methods on the policy, giving you an opportunity to authorize the action before the intended policy method is actually called. This feature is most commonly used for authorizing application administrators to perform any action:

```php	
use App\Models\User;
 
/**
 * Perform pre-authorization checks.
*/
publicfunctionbefore(User$user, string$ability):bool|null
{
if ($user->isAdministrator()) {
returntrue;
 }
 
returnnull;
}
```
If you would like to deny all authorization checks for a particular type of user then you may return `false` from the `before` method. If `null` is returned, the authorization check will fall through to the policy method.

> **Warning**  
>  The `before` method of a policy class will not be called if the class doesn't contain a method with a name matching the name of the ability being checked.

## [Authorizing Actions Using Policies](#authorizing-actions-using-policies)
### [Via The User Model](#via-the-user-model)
The `App\Models\User` model that is included with your Laravel application includes two helpful methods for authorizing actions: `can` and `cannot`. The `can` and `cannot` methods receive the name of the action you wish to authorize and the relevant model. For example, let's determine if a user is authorized to update a given `App\Models\Post` model. Typically, this will be done within a controller method:

```php	
<?php
 
namespace App\Http\Controllers;
 
use App\Http\Controllers\Controller;
use App\Models\Post;
use Illuminate\Http\Request;
use Illuminate\Http\Response;
 
classPostControllerextendsController
{
/**
 * Update the given post.
*/
publicfunctionupdate(Request$request, Post$post):Response
 {
if ($request->user()->cannot('update', $post)) {
abort(403);
 }
 
// Update the post...
 
returnresponse()->noContent();
 }
}
```
If a [policy is registered](#registering-policies) for the given model, the `can` method will automatically call the appropriate policy and return the boolean result. If no policy is registered for the model, the `can` method will attempt to call the closure-based Gate matching the given action name.

#### [Actions That Don't Require Models](#user-model-actions-that-dont-require-models)
Remember, some actions may correspond to policy methods like `create` that do not require a model instance. In these situations, you may pass a class name to the `can` method. The class name will be used to determine which policy to use when authorizing the action:

```php	
<?php
 
namespace App\Http\Controllers;
 
use App\Http\Controllers\Controller;
use App\Models\Post;
use Illuminate\Http\Request;
use Illuminate\Http\Response;
 
classPostControllerextendsController
{
/**
 * Create a post.
*/
publicfunctionstore(Request$request):Response
 {
if ($request->user()->cannot('create', Post::class)) {
abort(403);
 }
 
// Create the post...
 
returnresponse()->noContent();
 }
}
```
### [Via Controller Helpers](#via-controller-helpers)
In addition to helpful methods provided to the `App\Models\User` model, Laravel provides a helpful `authorize` method to any of your controllers which extend the `App\Http\Controllers\Controller` base class.

Like the `can` method, this method accepts the name of the action you wish to authorize and the relevant model. If the action is not authorized, the `authorize` method will throw an `Illuminate\Auth\Access\AuthorizationException` exception which the Laravel exception handler will automatically convert to an HTTP response with a 403 status code:

```php	
<?php
 
namespace App\Http\Controllers;
 
use App\Http\Controllers\Controller;
use App\Models\Post;
use Illuminate\Http\Request;
use Illuminate\Http\Response;
 
classPostControllerextendsController
{
/**
 * Update the given blog post.
 *
 * @throws\Illuminate\Auth\Access\AuthorizationException
*/
publicfunctionupdate(Request$request, Post$post):Response
 {
$this->authorize('update', $post);
 
// The current user can update the blog post...
 
returnresponse()->noContent();
 }
}
```
#### [Actions That Don't Require Models](#controller-actions-that-dont-require-models)
As previously discussed, some policy methods like `create` do not require a model instance. In these situations, you should pass a class name to the `authorize` method. The class name will be used to determine which policy to use when authorizing the action:

```php	
use App\Models\Post;
use Illuminate\Http\Request;
use Illuminate\Http\Response;
 
/**
 * Create a new blog post.
 *
 * @throws\Illuminate\Auth\Access\AuthorizationException
*/
publicfunctioncreate(Request$request):Response
{
$this->authorize('create', Post::class);
 
// The current user can create blog posts...
 
returnresponse()->noContent();
}
```
#### [Authorizing Resource Controllers](#authorizing-resource-controllers)
If you are utilizing [resource controllers](/docs/master/controllers#resource-controllers), you may make use of the `authorizeResource` method in your controller's constructor. This method will attach the appropriate `can` middleware definitions to the resource controller's methods.

The `authorizeResource` method accepts the model's class name as its first argument, and the name of the route / request parameter that will contain the model's ID as its second argument. You should ensure your [resource controller](/docs/master/controllers#resource-controllers) is created using the `--model` flag so that it has the required method signatures and type hints:

```php	
<?php
 
namespace App\Http\Controllers;
 
use App\Http\Controllers\Controller;
use App\Models\Post;
use Illuminate\Http\Request;
 
classPostControllerextendsController
{
/**
 * Create the controller instance.
*/
publicfunction__construct()
 {
$this->authorizeResource(Post::class, 'post');
 }
}
```
The following controller methods will be mapped to their corresponding policy method. When requests are routed to the given controller method, the corresponding policy method will automatically be invoked before the controller method is executed:

  
| Controller Method | Policy Method |   
| ---- | ---- |   
| index | viewAny |   
| show | view |   
| create | create |   
| store | create |   
| edit | update |   
| update | update |   
| destroy | delete | > **Note**  
>  You may use the `make:policy` command with the `--model` option to quickly generate a policy class for a given model: `php artisan make:policy PostPolicy --model=Post`.

### [Via Middleware](#via-middleware)
Laravel includes a middleware that can authorize actions before the incoming request even reaches your routes or controllers. By default, the `Illuminate\Auth\Middleware\Authorize` middleware is assigned the `can` key in your `App\Http\Kernel` class. Let's explore an example of using the `can` middleware to authorize that a user can update a post:

```php	
use App\Models\Post;
 
Route::put('/post/{post}', function(Post$post) {
// The current user may update the post...
})->middleware('can:update,post');
```
In this example, we're passing the `can` middleware two arguments. The first is the name of the action we wish to authorize and the second is the route parameter we wish to pass to the policy method. In this case, since we are using [implicit model binding](/docs/master/routing#implicit-binding), a `App\Models\Post` model will be passed to the policy method. If the user is not authorized to perform the given action, an HTTP response with a 403 status code will be returned by the middleware.

For convenience, you may also attach the `can` middleware to your route using the `can` method:

```php	
use App\Models\Post;
 
Route::put('/post/{post}', function(Post$post) {
// The current user may update the post...
})->can('update', 'post');
```
#### [Actions That Don't Require Models](#middleware-actions-that-dont-require-models)
Again, some policy methods like `create` do not require a model instance. In these situations, you may pass a class name to the middleware. The class name will be used to determine which policy to use when authorizing the action:

```php	
Route::post('/post', function() {
// The current user may create posts...
})->middleware('can:create,App\Models\Post');
```
Specifying the entire class name within a string middleware definition can become cumbersome. For that reason, you may choose to attach the `can` middleware to your route using the `can` method:

```php	
use App\Models\Post;
 
Route::post('/post', function() {
// The current user may create posts...
})->can('create', Post::class);
```
### [Via Blade Templates](#via-blade-templates)
When writing Blade templates, you may wish to display a portion of the page only if the user is authorized to perform a given action. For example, you may wish to show an update form for a blog post only if the user can actually update the post. In this situation, you may use the `@can` and `@cannot` directives:

```blade	
@can('update', $post)
<!-- The current user can update the post... -->
@elsecan('create', App\Models\Post::class)
<!-- The current user can create new posts... -->
@else
<!-- ... -->
@endcan
 
@cannot('update', $post)
<!-- The current user cannot update the post... -->
@elsecannot('create', App\Models\Post::class)
<!-- The current user cannot create new posts... -->
@endcannot
```
These directives are convenient shortcuts for writing `@if` and `@unless` statements. The `@can` and `@cannot` statements above are equivalent to the following statements:

```blade	
@if (Auth::user()->can('update', $post))
<!-- The current user can update the post... -->
@endif
 
@unless (Auth::user()->can('update', $post))
<!-- The current user cannot update the post... -->
@endunless
```
You may also determine if a user is authorized to perform any action from a given array of actions. To accomplish this, use the `@canany` directive:

```blade	
@canany(['update', 'view', 'delete'], $post)
<!-- The current user can update, view, or delete the post... -->
@elsecanany(['create'], \App\Models\Post::class)
<!-- The current user can create a post... -->
@endcanany
```
#### [Actions That Don't Require Models](#blade-actions-that-dont-require-models)
Like most of the other authorization methods, you may pass a class name to the `@can` and `@cannot` directives if the action does not require a model instance:

```blade	
@can('create', App\Models\Post::class)
<!-- The current user can create posts... -->
@endcan
 
@cannot('create', App\Models\Post::class)
<!-- The current user can't create posts... -->
@endcannot
```
### [Supplying Additional Context](#supplying-additional-context)
When authorizing actions using policies, you may pass an array as the second argument to the various authorization functions and helpers. The first element in the array will be used to determine which policy should be invoked, while the rest of the array elements are passed as parameters to the policy method and can be used for additional context when making authorization decisions. For example, consider the following `PostPolicy` method definition which contains an additional `$category` parameter:

```php	
/**
 * Determine if the given post can be updated by the user.
*/
publicfunctionupdate(User$user, Post$post, int$category):bool
{
return$user->id===$post->user_id&&
$user->canUpdateCategory($category);
}
```
When attempting to determine if the authenticated user can update a given post, we can invoke this policy method like so:

```php	
/**
 * Update the given blog post.
 *
 * @throws\Illuminate\Auth\Access\AuthorizationException
*/
publicfunctionupdate(Request$request, Post$post):Response
{
$this->authorize('update', [$post, $request->category]);
 
// The current user can update the blog post...
 
returnresponse()->noContent();
}
```
