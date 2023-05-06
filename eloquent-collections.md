# Eloquent: Collections
- [Introduction](#introduction)
- [Available Methods](#available-methods)
- [Custom Collections](#custom-collections)

## [Introduction](#introduction)
All Eloquent methods that return more than one model result will return instances of the `Illuminate\Database\Eloquent\Collection` class, including results retrieved via the `get` method or accessed via a relationship. The Eloquent collection object extends Laravel's [base collection](/docs/master/collections), so it naturally inherits dozens of methods used to fluently work with the underlying array of Eloquent models. Be sure to review the Laravel collection documentation to learn all about these helpful methods!

All collections also serve as iterators, allowing you to loop over them as if they were simple PHP arrays:

```php	
use App\Models\User;
 
$users=User::where('active', 1)->get();
 
foreach ($usersas$user) {
echo$user->name;
}
```
However, as previously mentioned, collections are much more powerful than arrays and expose a variety of map / reduce operations that may be chained using an intuitive interface. For example, we may remove all inactive models and then gather the first name for each remaining user:

```php	
$names=User::all()->reject(function(User$user) {
return$user->active===false;
})->map(function(User$user) {
return$user->name;
});
```
#### [Eloquent Collection Conversion](#eloquent-collection-conversion)
While most Eloquent collection methods return a new instance of an Eloquent collection, the `collapse`, `flatten`, `flip`, `keys`, `pluck`, and `zip` methods return a [base collection](/docs/master/collections) instance. Likewise, if a `map` operation returns a collection that does not contain any Eloquent models, it will be converted to a base collection instance.

## [Available Methods](#available-methods)
All Eloquent collections extend the base [Laravel collection](/docs/master/collections#available-methods) object; therefore, they inherit all of the powerful methods provided by the base collection class.

In addition, the `Illuminate\Database\Eloquent\Collection` class provides a superset of methods to aid with managing your model collections. Most methods return `Illuminate\Database\Eloquent\Collection` instances; however, some methods, like `modelKeys`, return an `Illuminate\Support\Collection` instance.

[append](#method-append)[contains](#method-contains)[diff](#method-diff)[except](#method-except)[find](#method-find)[fresh](#method-fresh)[intersect](#method-intersect)[load](#method-load)[loadMissing](#method-loadMissing)[modelKeys](#method-modelKeys)[makeVisible](#method-makeVisible)[makeHidden](#method-makeHidden)[only](#method-only)[setVisible](#method-setVisible)[setHidden](#method-setHidden)[toQuery](#method-toquery)[unique](#method-unique)

#### [`append($attributes)`](#method-append)
The `append` method may be used to indicate that an attribute should be [appended](/docs/master/eloquent-serialization#appending-values-to-json) for every model in the collection. This method accepts an array of attributes or a single attribute:

```php	
$users->append('team');
 
$users->append(['team', 'is_admin']);
```
#### [`contains($key, $operator = null, $value = null)`](#method-contains)
The `contains` method may be used to determine if a given model instance is contained by the collection. This method accepts a primary key or a model instance:

```php	
$users->contains(1);
 
$users->contains(User::find(1));
```
#### [`diff($items)`](#method-diff)
The `diff` method returns all of the models that are not present in the given collection:

```php	
use App\Models\User;
 
$users=$users->diff(User::whereIn('id', [1, 2, 3])->get());
```
#### [`except($keys)`](#method-except)
The `except` method returns all of the models that do not have the given primary keys:

```php	
$users=$users->except([1, 2, 3]);
```
#### [`find($key)`](#method-find)
The `find` method returns the model that has a primary key matching the given key. If `$key` is a model instance, `find` will attempt to return a model matching the primary key. If `$key` is an array of keys, `find` will return all models which have a primary key in the given array:

```php	
$users=User::all();
 
$user=$users->find(1);
```
#### [`fresh($with = [])`](#method-fresh)
The `fresh` method retrieves a fresh instance of each model in the collection from the database. In addition, any specified relationships will be eager loaded:

```php	
$users=$users->fresh();
 
$users=$users->fresh('comments');
```
#### [`intersect($items)`](#method-intersect)
The `intersect` method returns all of the models that are also present in the given collection:

```php	
use App\Models\User;
 
$users=$users->intersect(User::whereIn('id', [1, 2, 3])->get());
```
#### [`load($relations)`](#method-load)
The `load` method eager loads the given relationships for all models in the collection:

```php	
$users->load(['comments', 'posts']);
 
$users->load('comments.author');
 
$users->load(['comments', 'posts'=>fn($query) => $query->where('active', 1)]);
```
#### [`loadMissing($relations)`](#method-loadMissing)
The `loadMissing` method eager loads the given relationships for all models in the collection if the relationships are not already loaded:

```php	
$users->loadMissing(['comments', 'posts']);
 
$users->loadMissing('comments.author');
 
$users->loadMissing(['comments', 'posts'=>fn($query) => $query->where('active', 1)]);
```
#### [`modelKeys()`](#method-modelKeys)
The `modelKeys` method returns the primary keys for all models in the collection:

```php	
$users->modelKeys();
 
// [1, 2, 3, 4, 5]
```
#### [`makeVisible($attributes)`](#method-makeVisible)
The `makeVisible` method [makes attributes visible](/docs/master/eloquent-serialization#hiding-attributes-from-json) that are typically "hidden" on each model in the collection:

```php	
$users=$users->makeVisible(['address', 'phone_number']);
```
#### [`makeHidden($attributes)`](#method-makeHidden)
The `makeHidden` method [hides attributes](/docs/master/eloquent-serialization#hiding-attributes-from-json) that are typically "visible" on each model in the collection:

```php	
$users=$users->makeHidden(['address', 'phone_number']);
```
#### [`only($keys)`](#method-only)
The `only` method returns all of the models that have the given primary keys:

```php	
$users=$users->only([1, 2, 3]);
```
#### [`setVisible($attributes)`](#method-setVisible)
The `setVisible` method [temporarily overrides](/docs/master/eloquent-serialization#temporarily-modifying-attribute-visibility) all of the visible attributes on each model in the collection:

```php	
$users=$users->setVisible(['id', 'name']);
```
#### [`setHidden($attributes)`](#method-setHidden)
The `setHidden` method [temporarily overrides](/docs/master/eloquent-serialization#temporarily-modifying-attribute-visibility) all of the hidden attributes on each model in the collection:

```php	
$users=$users->setHidden(['email', 'password', 'remember_token']);
```
#### [`toQuery()`](#method-toquery)
The `toQuery` method returns an Eloquent query builder instance containing a `whereIn` constraint on the collection model's primary keys:

```php	
use App\Models\User;
 
$users=User::where('status', 'VIP')->get();
 
$users->toQuery()->update([
'status'=>'Administrator',
]);
```
#### [`unique($key = null, $strict = false)`](#method-unique)
The `unique` method returns all of the unique models in the collection. Any models of the same type with the same primary key as another model in the collection are removed:

```php	
$users=$users->unique();
```
## [Custom Collections](#custom-collections)
If you would like to use a custom `Collection` object when interacting with a given model, you may define a `newCollection` method on your model:

```php	
<?php
 
namespace App\Models;
 
use App\Support\UserCollection;
use Illuminate\Database\Eloquent\Collection;
use Illuminate\Database\Eloquent\Model;
 
classUserextendsModel
{
/**
 * Create a new Eloquent Collection instance.
 *
 * @paramarray<int, \Illuminate\Database\Eloquent\Model> $models
 * @return\Illuminate\Database\Eloquent\Collection<int, \Illuminate\Database\Eloquent\Model>
*/
publicfunctionnewCollection(array$models= []):Collection
 {
returnnewUserCollection($models);
 }
}
```
Once you have defined a `newCollection` method, you will receive an instance of your custom collection anytime Eloquent would normally return an `Illuminate\Database\Eloquent\Collection` instance. If you would like to use a custom collection for every model in your application, you should define the `newCollection` method on a base model class that is extended by all of your application's models.

