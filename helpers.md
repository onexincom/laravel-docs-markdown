# Helpers
- [Introduction](#introduction)
- [Available Methods](#available-methods)
- [Other Utilities](#other-utilities)
    - [Benchmarking](#benchmarking)

    - [Lottery](#lottery)



## [Introduction](#introduction)
Laravel includes a variety of global "helper" PHP functions. Many of these functions are used by the framework itself; however, you are free to use them in your own applications if you find them convenient.

## [Available Methods](#available-methods)
### [Arrays & Objects](#arrays-and-objects-method-list)
[Arr::accessible](#method-array-accessible)[Arr::add](#method-array-add)[Arr::collapse](#method-array-collapse)[Arr::crossJoin](#method-array-crossjoin)[Arr::divide](#method-array-divide)[Arr::dot](#method-array-dot)[Arr::except](#method-array-except)[Arr::exists](#method-array-exists)[Arr::first](#method-array-first)[Arr::flatten](#method-array-flatten)[Arr::forget](#method-array-forget)[Arr::get](#method-array-get)[Arr::has](#method-array-has)[Arr::hasAny](#method-array-hasany)[Arr::isAssoc](#method-array-isassoc)[Arr::isList](#method-array-islist)[Arr::join](#method-array-join)[Arr::keyBy](#method-array-keyby)[Arr::last](#method-array-last)[Arr::map](#method-array-map)[Arr::only](#method-array-only)[Arr::pluck](#method-array-pluck)[Arr::prepend](#method-array-prepend)[Arr::prependKeysWith](#method-array-prependkeyswith)[Arr::pull](#method-array-pull)[Arr::query](#method-array-query)[Arr::random](#method-array-random)[Arr::set](#method-array-set)[Arr::shuffle](#method-array-shuffle)[Arr::sort](#method-array-sort)[Arr::sortDesc](#method-array-sort-desc)[Arr::sortRecursive](#method-array-sort-recursive)[Arr::toCssClasses](#method-array-to-css-classes)[Arr::undot](#method-array-undot)[Arr::where](#method-array-where)[Arr::whereNotNull](#method-array-where-not-null)[Arr::wrap](#method-array-wrap)[data_fill](#method-data-fill)[data_get](#method-data-get)[data_set](#method-data-set)[head](#method-head)[last](#method-last)

### [Paths](#paths-method-list)
[app_path](#method-app-path)[base_path](#method-base-path)[config_path](#method-config-path)[database_path](#method-database-path)[lang_path](#method-lang-path)[mix](#method-mix)[public_path](#method-public-path)[resource_path](#method-resource-path)[storage_path](#method-storage-path)

### [Strings](#strings-method-list)
[__](#method-__)[class_basename](#method-class-basename)[e](#method-e)[preg_replace_array](#method-preg-replace-array)[Str::after](#method-str-after)[Str::afterLast](#method-str-after-last)[Str::ascii](#method-str-ascii)[Str::before](#method-str-before)[Str::beforeLast](#method-str-before-last)[Str::between](#method-str-between)[Str::betweenFirst](#method-str-between-first)[Str::camel](#method-camel-case)[Str::contains](#method-str-contains)[Str::containsAll](#method-str-contains-all)[Str::endsWith](#method-ends-with)[Str::excerpt](#method-excerpt)[Str::finish](#method-str-finish)[Str::headline](#method-str-headline)[Str::inlineMarkdown](#method-str-inline-markdown)[Str::is](#method-str-is)[Str::isAscii](#method-str-is-ascii)[Str::isJson](#method-str-is-json)[Str::isUlid](#method-str-is-ulid)[Str::isUuid](#method-str-is-uuid)[Str::kebab](#method-kebab-case)[Str::lcfirst](#method-str-lcfirst)[Str::length](#method-str-length)[Str::limit](#method-str-limit)[Str::lower](#method-str-lower)[Str::markdown](#method-str-markdown)[Str::mask](#method-str-mask)[Str::orderedUuid](#method-str-ordered-uuid)[Str::padBoth](#method-str-padboth)[Str::padLeft](#method-str-padleft)[Str::padRight](#method-str-padright)[Str::password](#method-str-password)[Str::plural](#method-str-plural)[Str::pluralStudly](#method-str-plural-studly)[Str::random](#method-str-random)[Str::remove](#method-str-remove)[Str::replace](#method-str-replace)[Str::replaceArray](#method-str-replace-array)[Str::replaceFirst](#method-str-replace-first)[Str::replaceLast](#method-str-replace-last)[Str::reverse](#method-str-reverse)[Str::singular](#method-str-singular)[Str::slug](#method-str-slug)[Str::snake](#method-snake-case)[Str::squish](#method-str-squish)[Str::start](#method-str-start)[Str::startsWith](#method-starts-with)[Str::studly](#method-studly-case)[Str::substr](#method-str-substr)[Str::substrCount](#method-str-substrcount)[Str::substrReplace](#method-str-substrreplace)[Str::swap](#method-str-swap)[Str::title](#method-title-case)[Str::toHtmlString](#method-str-to-html-string)[Str::ucfirst](#method-str-ucfirst)[Str::ucsplit](#method-str-ucsplit)[Str::upper](#method-str-upper)[Str::ulid](#method-str-ulid)[Str::uuid](#method-str-uuid)[Str::wordCount](#method-str-word-count)[Str::words](#method-str-words)[str](#method-str)[trans](#method-trans)[trans_choice](#method-trans-choice)

### [Fluent Strings](#fluent-strings-method-list)
[after](#method-fluent-str-after)[afterLast](#method-fluent-str-after-last)[append](#method-fluent-str-append)[ascii](#method-fluent-str-ascii)[basename](#method-fluent-str-basename)[before](#method-fluent-str-before)[beforeLast](#method-fluent-str-before-last)[between](#method-fluent-str-between)[betweenFirst](#method-fluent-str-between-first)[camel](#method-fluent-str-camel)[classBasename](#method-fluent-str-class-basename)[contains](#method-fluent-str-contains)[containsAll](#method-fluent-str-contains-all)[dirname](#method-fluent-str-dirname)[endsWith](#method-fluent-str-ends-with)[excerpt](#method-fluent-str-excerpt)[exactly](#method-fluent-str-exactly)[explode](#method-fluent-str-explode)[finish](#method-fluent-str-finish)[headline](#method-fluent-str-headline)[inlineMarkdown](#method-fluent-str-inline-markdown)[is](#method-fluent-str-is)[isAscii](#method-fluent-str-is-ascii)[isEmpty](#method-fluent-str-is-empty)[isNotEmpty](#method-fluent-str-is-not-empty)[isJson](#method-fluent-str-is-json)[isUlid](#method-fluent-str-is-ulid)[isUuid](#method-fluent-str-is-uuid)[kebab](#method-fluent-str-kebab)[lcfirst](#method-fluent-str-lcfirst)[length](#method-fluent-str-length)[limit](#method-fluent-str-limit)[lower](#method-fluent-str-lower)[ltrim](#method-fluent-str-ltrim)[markdown](#method-fluent-str-markdown)[mask](#method-fluent-str-mask)[match](#method-fluent-str-match)[matchAll](#method-fluent-str-match-all)[newLine](#method-fluent-str-new-line)[padBoth](#method-fluent-str-padboth)[padLeft](#method-fluent-str-padleft)[padRight](#method-fluent-str-padright)[pipe](#method-fluent-str-pipe)[plural](#method-fluent-str-plural)[prepend](#method-fluent-str-prepend)[remove](#method-fluent-str-remove)[replace](#method-fluent-str-replace)[replaceArray](#method-fluent-str-replace-array)[replaceFirst](#method-fluent-str-replace-first)[replaceLast](#method-fluent-str-replace-last)[replaceMatches](#method-fluent-str-replace-matches)[rtrim](#method-fluent-str-rtrim)[scan](#method-fluent-str-scan)[singular](#method-fluent-str-singular)[slug](#method-fluent-str-slug)[snake](#method-fluent-str-snake)[split](#method-fluent-str-split)[squish](#method-fluent-str-squish)[start](#method-fluent-str-start)[startsWith](#method-fluent-str-starts-with)[studly](#method-fluent-str-studly)[substr](#method-fluent-str-substr)[substrReplace](#method-fluent-str-substrreplace)[swap](#method-fluent-str-swap)[tap](#method-fluent-str-tap)[test](#method-fluent-str-test)[title](#method-fluent-str-title)[trim](#method-fluent-str-trim)[ucfirst](#method-fluent-str-ucfirst)[ucsplit](#method-fluent-str-ucsplit)[upper](#method-fluent-str-upper)[when](#method-fluent-str-when)[whenContains](#method-fluent-str-when-contains)[whenContainsAll](#method-fluent-str-when-contains-all)[whenEmpty](#method-fluent-str-when-empty)[whenNotEmpty](#method-fluent-str-when-not-empty)[whenStartsWith](#method-fluent-str-when-starts-with)[whenEndsWith](#method-fluent-str-when-ends-with)[whenExactly](#method-fluent-str-when-exactly)[whenNotExactly](#method-fluent-str-when-not-exactly)[whenIs](#method-fluent-str-when-is)[whenIsAscii](#method-fluent-str-when-is-ascii)[whenIsUlid](#method-fluent-str-when-is-ulid)[whenIsUuid](#method-fluent-str-when-is-uuid)[whenTest](#method-fluent-str-when-test)[wordCount](#method-fluent-str-word-count)[words](#method-fluent-str-words)

### [URLs](#urls-method-list)
[action](#method-action)[asset](#method-asset)[route](#method-route)[secure_asset](#method-secure-asset)[secure_url](#method-secure-url)[to_route](#method-to-route)[url](#method-url)

### [Miscellaneous](#miscellaneous-method-list)
[abort](#method-abort)[abort_if](#method-abort-if)[abort_unless](#method-abort-unless)[app](#method-app)[auth](#method-auth)[back](#method-back)[bcrypt](#method-bcrypt)[blank](#method-blank)[broadcast](#method-broadcast)[cache](#method-cache)[class_uses_recursive](#method-class-uses-recursive)[collect](#method-collect)[config](#method-config)[cookie](#method-cookie)[csrf_field](#method-csrf-field)[csrf_token](#method-csrf-token)[decrypt](#method-decrypt)[dd](#method-dd)[dispatch](#method-dispatch)[dump](#method-dump)[encrypt](#method-encrypt)[env](#method-env)[event](#method-event)[fake](#method-fake)[filled](#method-filled)[info](#method-info)[logger](#method-logger)[method_field](#method-method-field)[now](#method-now)[old](#method-old)[optional](#method-optional)[policy](#method-policy)[redirect](#method-redirect)[report](#method-report)[report_if](#method-report-if)[report_unless](#method-report-unless)[request](#method-request)[rescue](#method-rescue)[resolve](#method-resolve)[response](#method-response)[retry](#method-retry)[session](#method-session)[tap](#method-tap)[throw_if](#method-throw-if)[throw_unless](#method-throw-unless)[today](#method-today)[trait_uses_recursive](#method-trait-uses-recursive)[transform](#method-transform)[validator](#method-validator)[value](#method-value)[view](#method-view)[with](#method-with)

## [Method Listing](#method-listing)
## [Arrays & Objects](#arrays)
#### [`Arr::accessible()`](#method-array-accessible)
The `Arr::accessible` method determines if the given value is array accessible:

```php	
use Illuminate\Support\Arr;
use Illuminate\Support\Collection;
 
$isAccessible=Arr::accessible(['a'=>1, 'b'=>2]);
 
// true
 
$isAccessible=Arr::accessible(newCollection);
 
// true
 
$isAccessible=Arr::accessible('abc');
 
// false
 
$isAccessible=Arr::accessible(newstdClass);
 
// false
```
#### [`Arr::add()`](#method-array-add)
The `Arr::add` method adds a given key / value pair to an array if the given key doesn't already exist in the array or is set to `null`:

```php	
use Illuminate\Support\Arr;
 
$array=Arr::add(['name'=>'Desk'], 'price', 100);
 
// ['name' => 'Desk', 'price' => 100]
 
$array=Arr::add(['name'=>'Desk', 'price'=>null], 'price', 100);
 
// ['name' => 'Desk', 'price' => 100]
```
#### [`Arr::collapse()`](#method-array-collapse)
The `Arr::collapse` method collapses an array of arrays into a single array:

```php	
use Illuminate\Support\Arr;
 
$array=Arr::collapse([[1, 2, 3], [4, 5, 6], [7, 8, 9]]);
 
// [1, 2, 3, 4, 5, 6, 7, 8, 9]
```
#### [`Arr::crossJoin()`](#method-array-crossjoin)
The `Arr::crossJoin` method cross joins the given arrays, returning a Cartesian product with all possible permutations:

```php	
use Illuminate\Support\Arr;
 
$matrix=Arr::crossJoin([1, 2], ['a', 'b']);
 
/*
 [
 [1, 'a'],
 [1, 'b'],
 [2, 'a'],
 [2, 'b'],
 ]
*/
 
$matrix=Arr::crossJoin([1, 2], ['a', 'b'], ['I', 'II']);
 
/*
 [
 [1, 'a', 'I'],
 [1, 'a', 'II'],
 [1, 'b', 'I'],
 [1, 'b', 'II'],
 [2, 'a', 'I'],
 [2, 'a', 'II'],
 [2, 'b', 'I'],
 [2, 'b', 'II'],
 ]
*/
```
#### [`Arr::divide()`](#method-array-divide)
The `Arr::divide` method returns two arrays: one containing the keys and the other containing the values of the given array:

```php	
use Illuminate\Support\Arr;
 
[$keys, $values] =Arr::divide(['name'=>'Desk']);
 
// $keys: ['name']
 
// $values: ['Desk']
```
#### [`Arr::dot()`](#method-array-dot)
The `Arr::dot` method flattens a multi-dimensional array into a single level array that uses "dot" notation to indicate depth:

```php	
use Illuminate\Support\Arr;
 
$array= ['products'=> ['desk'=> ['price'=>100]]];
 
$flattened=Arr::dot($array);
 
// ['products.desk.price' => 100]
```
#### [`Arr::except()`](#method-array-except)
The `Arr::except` method removes the given key / value pairs from an array:

```php	
use Illuminate\Support\Arr;
 
$array= ['name'=>'Desk', 'price'=>100];
 
$filtered=Arr::except($array, ['price']);
 
// ['name' => 'Desk']
```
#### [`Arr::exists()`](#method-array-exists)
The `Arr::exists` method checks that the given key exists in the provided array:

```php	
use Illuminate\Support\Arr;
 
$array= ['name'=>'John Doe', 'age'=>17];
 
$exists=Arr::exists($array, 'name');
 
// true
 
$exists=Arr::exists($array, 'salary');
 
// false
```
#### [`Arr::first()`](#method-array-first)
The `Arr::first` method returns the first element of an array passing a given truth test:

```php	
use Illuminate\Support\Arr;
 
$array= [100, 200, 300];
 
$first=Arr::first($array, function(int$value, int$key) {
return$value>=150;
});
 
// 200
```
A default value may also be passed as the third parameter to the method. This value will be returned if no value passes the truth test:

```php	
use Illuminate\Support\Arr;
 
$first=Arr::first($array, $callback, $default);
```
#### [`Arr::flatten()`](#method-array-flatten)
The `Arr::flatten` method flattens a multi-dimensional array into a single level array:

```php	
use Illuminate\Support\Arr;
 
$array= ['name'=>'Joe', 'languages'=> ['PHP', 'Ruby']];
 
$flattened=Arr::flatten($array);
 
// ['Joe', 'PHP', 'Ruby']
```
#### [`Arr::forget()`](#method-array-forget)
The `Arr::forget` method removes a given key / value pair from a deeply nested array using "dot" notation:

```php	
use Illuminate\Support\Arr;
 
$array= ['products'=> ['desk'=> ['price'=>100]]];
 
Arr::forget($array, 'products.desk');
 
// ['products' => []]
```
#### [`Arr::get()`](#method-array-get)
The `Arr::get` method retrieves a value from a deeply nested array using "dot" notation:

```php	
use Illuminate\Support\Arr;
 
$array= ['products'=> ['desk'=> ['price'=>100]]];
 
$price=Arr::get($array, 'products.desk.price');
 
// 100
```
The `Arr::get` method also accepts a default value, which will be returned if the specified key is not present in the array:

```php	
use Illuminate\Support\Arr;
 
$discount=Arr::get($array, 'products.desk.discount', 0);
 
// 0
```
#### [`Arr::has()`](#method-array-has)
The `Arr::has` method checks whether a given item or items exists in an array using "dot" notation:

```php	
use Illuminate\Support\Arr;
 
$array= ['product'=> ['name'=>'Desk', 'price'=>100]];
 
$contains=Arr::has($array, 'product.name');
 
// true
 
$contains=Arr::has($array, ['product.price', 'product.discount']);
 
// false
```
#### [`Arr::hasAny()`](#method-array-hasany)
The `Arr::hasAny` method checks whether any item in a given set exists in an array using "dot" notation:

```php	
use Illuminate\Support\Arr;
 
$array= ['product'=> ['name'=>'Desk', 'price'=>100]];
 
$contains=Arr::hasAny($array, 'product.name');
 
// true
 
$contains=Arr::hasAny($array, ['product.name', 'product.discount']);
 
// true
 
$contains=Arr::hasAny($array, ['category', 'product.discount']);
 
// false
```
#### [`Arr::isAssoc()`](#method-array-isassoc)
The `Arr::isAssoc` method returns `true` if the given array is an associative array. An array is considered "associative" if it doesn't have sequential numerical keys beginning with zero:

```php	
use Illuminate\Support\Arr;
 
$isAssoc=Arr::isAssoc(['product'=> ['name'=>'Desk', 'price'=>100]]);
 
// true
 
$isAssoc=Arr::isAssoc([1, 2, 3]);
 
// false
```
#### [`Arr::isList()`](#method-array-islist)
The `Arr::isList` method returns `true` if the given array's keys are sequential integers beginning from zero:

```php	
use Illuminate\Support\Arr;
 
$isList=Arr::isList(['foo', 'bar', 'baz']);
 
// true
 
$isList=Arr::isList(['product'=> ['name'=>'Desk', 'price'=>100]]);
 
// false
```
#### [`Arr::join()`](#method-array-join)
The `Arr::join` method joins array elements with a string. Using this method's second argument, you may also specify the joining string for the final element of the array:

```php	
use Illuminate\Support\Arr;
 
$array= ['Tailwind', 'Alpine', 'Laravel', 'Livewire'];
 
$joined=Arr::join($array, ', ');
 
// Tailwind, Alpine, Laravel, Livewire
 
$joined=Arr::join($array, ', ', ' and ');
 
// Tailwind, Alpine, Laravel and Livewire
```
#### [`Arr::keyBy()`](#method-array-keyby)
The `Arr::keyBy` method keys the array by the given key. If multiple items have the same key, only the last one will appear in the new array:

```php	
use Illuminate\Support\Arr;
 
$array= [
 ['product_id'=>'prod-100', 'name'=>'Desk'],
 ['product_id'=>'prod-200', 'name'=>'Chair'],
];
 
$keyed=Arr::keyBy($array, 'product_id');
 
/*
 [
 'prod-100' => ['product_id' => 'prod-100', 'name' => 'Desk'],
 'prod-200' => ['product_id' => 'prod-200', 'name' => 'Chair'],
 ]
*/
```
#### [`Arr::last()`](#method-array-last)
The `Arr::last` method returns the last element of an array passing a given truth test:

```php	
use Illuminate\Support\Arr;
 
$array= [100, 200, 300, 110];
 
$last=Arr::last($array, function(int$value, int$key) {
return$value>=150;
});
 
// 300
```
A default value may be passed as the third argument to the method. This value will be returned if no value passes the truth test:

```php	
use Illuminate\Support\Arr;
 
$last=Arr::last($array, $callback, $default);
```
#### [`Arr::map()`](#method-array-map)
The `Arr::map` method iterates through the array and passes each value and key to the given callback. The array value is replaced by the value returned by the callback:

```php	
use Illuminate\Support\Arr;
 
$array= ['first'=>'james', 'last'=>'kirk'];
 
$mapped=Arr::map($array, function(string$value, string$key) {
returnucfirst($value);
});
 
// ['first' => 'James', 'last' => 'Kirk']
```
#### [`Arr::only()`](#method-array-only)
The `Arr::only` method returns only the specified key / value pairs from the given array:

```php	
use Illuminate\Support\Arr;
 
$array= ['name'=>'Desk', 'price'=>100, 'orders'=>10];
 
$slice=Arr::only($array, ['name', 'price']);
 
// ['name' => 'Desk', 'price' => 100]
```
#### [`Arr::pluck()`](#method-array-pluck)
The `Arr::pluck` method retrieves all of the values for a given key from an array:

```php	
use Illuminate\Support\Arr;
 
$array= [
 ['developer'=> ['id'=>1, 'name'=>'Taylor']],
 ['developer'=> ['id'=>2, 'name'=>'Abigail']],
];
 
$names=Arr::pluck($array, 'developer.name');
 
// ['Taylor', 'Abigail']
```
You may also specify how you wish the resulting list to be keyed:

```php	
use Illuminate\Support\Arr;
 
$names=Arr::pluck($array, 'developer.name', 'developer.id');
 
// [1 => 'Taylor', 2 => 'Abigail']
```
#### [`Arr::prepend()`](#method-array-prepend)
The `Arr::prepend` method will push an item onto the beginning of an array:

```php	
use Illuminate\Support\Arr;
 
$array= ['one', 'two', 'three', 'four'];
 
$array=Arr::prepend($array, 'zero');
 
// ['zero', 'one', 'two', 'three', 'four']
```
If needed, you may specify the key that should be used for the value:

```php	
use Illuminate\Support\Arr;
 
$array= ['price'=>100];
 
$array=Arr::prepend($array, 'Desk', 'name');
 
// ['name' => 'Desk', 'price' => 100]
```
#### [`Arr::prependKeysWith()`](#method-array-prependkeyswith)
The `Arr::prependKeysWith` prepends all key names of an associative array with the given prefix:

```php	
use Illuminate\Support\Arr;
 
$array= [
'name'=>'Desk',
'price'=>100,
];
 
$keyed=Arr::prependKeysWith($array, 'product.');
 
/*
 [
 'product.name' => 'Desk',
 'product.price' => 100,
 ]
*/
```
#### [`Arr::pull()`](#method-array-pull)
The `Arr::pull` method returns and removes a key / value pair from an array:

```php	
use Illuminate\Support\Arr;
 
$array= ['name'=>'Desk', 'price'=>100];
 
$name=Arr::pull($array, 'name');
 
// $name: Desk
 
// $array: ['price' => 100]
```
A default value may be passed as the third argument to the method. This value will be returned if the key doesn't exist:

```php	
use Illuminate\Support\Arr;
 
$value=Arr::pull($array, $key, $default);
```
#### [`Arr::query()`](#method-array-query)
The `Arr::query` method converts the array into a query string:

```php	
use Illuminate\Support\Arr;
 
$array= [
'name'=>'Taylor',
'order'=> [
'column'=>'created_at',
'direction'=>'desc'
 ]
];
 
Arr::query($array);
 
// name=Taylor&order[column]=created_at&order[direction]=desc
```
#### [`Arr::random()`](#method-array-random)
The `Arr::random` method returns a random value from an array:

```php	
use Illuminate\Support\Arr;
 
$array= [1, 2, 3, 4, 5];
 
$random=Arr::random($array);
 
// 4 - (retrieved randomly)
```
You may also specify the number of items to return as an optional second argument. Note that providing this argument will return an array even if only one item is desired:

```php	
use Illuminate\Support\Arr;
 
$items=Arr::random($array, 2);
 
// [2, 5] - (retrieved randomly)
```
#### [`Arr::set()`](#method-array-set)
The `Arr::set` method sets a value within a deeply nested array using "dot" notation:

```php	
use Illuminate\Support\Arr;
 
$array= ['products'=> ['desk'=> ['price'=>100]]];
 
Arr::set($array, 'products.desk.price', 200);
 
// ['products' => ['desk' => ['price' => 200]]]
```
#### [`Arr::shuffle()`](#method-array-shuffle)
The `Arr::shuffle` method randomly shuffles the items in the array:

```php	
use Illuminate\Support\Arr;
 
$array=Arr::shuffle([1, 2, 3, 4, 5]);
 
// [3, 2, 5, 1, 4] - (generated randomly)
```
#### [`Arr::sort()`](#method-array-sort)
The `Arr::sort` method sorts an array by its values:

```php	
use Illuminate\Support\Arr;
 
$array= ['Desk', 'Table', 'Chair'];
 
$sorted=Arr::sort($array);
 
// ['Chair', 'Desk', 'Table']
```
You may also sort the array by the results of a given closure:

```php	
use Illuminate\Support\Arr;
 
$array= [
 ['name'=>'Desk'],
 ['name'=>'Table'],
 ['name'=>'Chair'],
];
 
$sorted=array_values(Arr::sort($array,function(array$value){
return$value['name'];
}));
 
/*
 [
 ['name' => 'Chair'],
 ['name' => 'Desk'],
 ['name' => 'Table'],
 ]
*/
```
#### [`Arr::sortDesc()`](#method-array-sort-desc)
The `Arr::sortDesc` method sorts an array in descending order by its values:

```php	
use Illuminate\Support\Arr;
 
$array= ['Desk', 'Table', 'Chair'];
 
$sorted=Arr::sortDesc($array);
 
// ['Table', 'Desk', 'Chair']
```
You may also sort the array by the results of a given closure:

```php	
use Illuminate\Support\Arr;
 
$array= [
 ['name'=>'Desk'],
 ['name'=>'Table'],
 ['name'=>'Chair'],
];
 
$sorted=array_values(Arr::sortDesc($array,function(array$value){
return$value['name'];
}));
 
/*
 [
 ['name' => 'Table'],
 ['name' => 'Desk'],
 ['name' => 'Chair'],
 ]
*/
```
#### [`Arr::sortRecursive()`](#method-array-sort-recursive)
The `Arr::sortRecursive` method recursively sorts an array using the `sort` function for numerically indexed sub-arrays and the `ksort` function for associative sub-arrays:

```php	
use Illuminate\Support\Arr;
 
$array= [
 ['Roman', 'Taylor', 'Li'],
 ['PHP', 'Ruby', 'JavaScript'],
 ['one'=>1, 'two'=>2, 'three'=>3],
];
 
$sorted=Arr::sortRecursive($array);
 
/*
 [
 ['JavaScript', 'PHP', 'Ruby'],
 ['one' => 1, 'three' => 3, 'two' => 2],
 ['Li', 'Roman', 'Taylor'],
 ]
*/
```
#### [`Arr::toCssClasses()`](#method-array-to-css-classes)
The `Arr::toCssClasses` conditionally compiles a CSS class string. The method accepts an array of classes where the array key contains the class or classes you wish to add, while the value is a boolean expression. If the array element has a numeric key, it will always be included in the rendered class list:

```php	
use Illuminate\Support\Arr;
 
$isActive=false;
$hasError=true;
 
$array= ['p-4', 'font-bold'=>$isActive, 'bg-red'=>$hasError];
 
$classes=Arr::toCssClasses($array);
 
/*
 'p-4 bg-red'
*/
```
This method powers Laravel's functionality allowing [merging classes with a Blade component's attribute bag](/docs/master/blade#conditionally-merge-classes) as well as the `@class`[Blade directive](/docs/master/blade#conditional-classes).

#### [`Arr::undot()`](#method-array-undot)
The `Arr::undot` method expands a single-dimensional array that uses "dot" notation into a multi-dimensional array:

```php	
use Illuminate\Support\Arr;
 
$array= [
'user.name'=>'Kevin Malone',
'user.occupation'=>'Accountant',
];
 
$array=Arr::undot($array);
 
// ['user' => ['name' => 'Kevin Malone', 'occupation' => 'Accountant']]
```
#### [`Arr::where()`](#method-array-where)
The `Arr::where` method filters an array using the given closure:

```php	
use Illuminate\Support\Arr;
 
$array= [100, '200', 300, '400', 500];
 
$filtered=Arr::where($array, function(string|int$value, int$key) {
returnis_string($value);
});
 
// [1 => '200', 3 => '400']
```
#### [`Arr::whereNotNull()`](#method-array-where-not-null)
The `Arr::whereNotNull` method removes all `null` values from the given array:

```php	
use Illuminate\Support\Arr;
 
$array= [0, null];
 
$filtered=Arr::whereNotNull($array);
 
// [0 => 0]
```
#### [`Arr::wrap()`](#method-array-wrap)
The `Arr::wrap` method wraps the given value in an array. If the given value is already an array it will be returned without modification:

```php	
use Illuminate\Support\Arr;
 
$string='Laravel';
 
$array=Arr::wrap($string);
 
// ['Laravel']
```
If the given value is `null`, an empty array will be returned:

```php	
use Illuminate\Support\Arr;
 
$array=Arr::wrap(null);
 
// []
```
#### [`data_fill()`](#method-data-fill)
The `data_fill` function sets a missing value within a nested array or object using "dot" notation:

```php	
$data= ['products'=> ['desk'=> ['price'=>100]]];
 
data_fill($data,'products.desk.price',200);
 
// ['products' => ['desk' => ['price' => 100]]]
 
data_fill($data,'products.desk.discount',10);
 
// ['products' => ['desk' => ['price' => 100, 'discount' => 10]]]
```
This function also accepts asterisks as wildcards and will fill the target accordingly:

```php	
$data= [
'products'=> [
 ['name'=>'Desk 1', 'price'=>100],
 ['name'=>'Desk 2'],
 ],
];
 
data_fill($data,'products.*.price',200);
 
/*
 [
 'products' => [
 ['name' => 'Desk 1', 'price' => 100],
 ['name' => 'Desk 2', 'price' => 200],
 ],
 ]
*/
```
#### [`data_get()`](#method-data-get)
The `data_get` function retrieves a value from a nested array or object using "dot" notation:

```php	
$data= ['products'=> ['desk'=> ['price'=>100]]];
 
$price=data_get($data,'products.desk.price');
 
// 100
```
The `data_get` function also accepts a default value, which will be returned if the specified key is not found:

```php	
$discount=data_get($data,'products.desk.discount',0);
 
// 0
```
The function also accepts wildcards using asterisks, which may target any key of the array or object:

```php	
$data= [
'product-one'=> ['name'=>'Desk 1', 'price'=>100],
'product-two'=> ['name'=>'Desk 2', 'price'=>150],
];
 
data_get($data,'*.name');
 
// ['Desk 1', 'Desk 2'];
```
#### [`data_set()`](#method-data-set)
The `data_set` function sets a value within a nested array or object using "dot" notation:

```php	
$data= ['products'=> ['desk'=> ['price'=>100]]];
 
data_set($data,'products.desk.price',200);
 
// ['products' => ['desk' => ['price' => 200]]]
```
This function also accepts wildcards using asterisks and will set values on the target accordingly:

```php	
$data= [
'products'=> [
 ['name'=>'Desk 1', 'price'=>100],
 ['name'=>'Desk 2', 'price'=>150],
 ],
];
 
data_set($data,'products.*.price',200);
 
/*
 [
 'products' => [
 ['name' => 'Desk 1', 'price' => 200],
 ['name' => 'Desk 2', 'price' => 200],
 ],
 ]
*/
```
By default, any existing values are overwritten. If you wish to only set a value if it doesn't exist, you may pass `false` as the fourth argument to the function:

```php	
$data= ['products'=> ['desk'=> ['price'=>100]]];
 
data_set($data,'products.desk.price',200, overwrite: false);
 
// ['products' => ['desk' => ['price' => 100]]]
```
#### [`head()`](#method-head)
The `head` function returns the first element in the given array:

```php	
$array= [100, 200, 300];
 
$first=head($array);
 
// 100
```
#### [`last()`](#method-last)
The `last` function returns the last element in the given array:

```php	
$array= [100, 200, 300];
 
$last=last($array);
 
// 300
```
## [Paths](#paths)
#### [`app_path()`](#method-app-path)
The `app_path` function returns the fully qualified path to your application's `app` directory. You may also use the `app_path` function to generate a fully qualified path to a file relative to the application directory:

```php	
$path=app_path();
 
$path=app_path('Http/Controllers/Controller.php');
```
#### [`base_path()`](#method-base-path)
The `base_path` function returns the fully qualified path to your application's root directory. You may also use the `base_path` function to generate a fully qualified path to a given file relative to the project root directory:

```php	
$path=base_path();
 
$path=base_path('vendor/bin');
```
#### [`config_path()`](#method-config-path)
The `config_path` function returns the fully qualified path to your application's `config` directory. You may also use the `config_path` function to generate a fully qualified path to a given file within the application's configuration directory:

```php	
$path=config_path();
 
$path=config_path('app.php');
```
#### [`database_path()`](#method-database-path)
The `database_path` function returns the fully qualified path to your application's `database` directory. You may also use the `database_path` function to generate a fully qualified path to a given file within the database directory:

```php	
$path=database_path();
 
$path=database_path('factories/UserFactory.php');
```
#### [`lang_path()`](#method-lang-path)
The `lang_path` function returns the fully qualified path to your application's `lang` directory. You may also use the `lang_path` function to generate a fully qualified path to a given file within the directory:

```php	
$path=lang_path();
 
$path=lang_path('en/messages.php');
```
> **Note** By default, the Laravel application skeleton does not include the `lang` directory. If you would like to customize Laravel's language files, you may publish them via the `lang:publish` Artisan command.

#### [`mix()`](#method-mix)
The `mix` function returns the path to a [versioned Mix file](/docs/master/mix):

```php	
$path=mix('css/app.css');
```
#### [`public_path()`](#method-public-path)
The `public_path` function returns the fully qualified path to your application's `public` directory. You may also use the `public_path` function to generate a fully qualified path to a given file within the public directory:

```php	
$path=public_path();
 
$path=public_path('css/app.css');
```
#### [`resource_path()`](#method-resource-path)
The `resource_path` function returns the fully qualified path to your application's `resources` directory. You may also use the `resource_path` function to generate a fully qualified path to a given file within the resources directory:

```php	
$path=resource_path();
 
$path=resource_path('sass/app.scss');
```
#### [`storage_path()`](#method-storage-path)
The `storage_path` function returns the fully qualified path to your application's `storage` directory. You may also use the `storage_path` function to generate a fully qualified path to a given file within the storage directory:

```php	
$path=storage_path();
 
$path=storage_path('app/file.txt');
```
## [Strings](#strings)
#### [`__()`](#method-__)
The `__` function translates the given translation string or translation key using your [language files](/docs/master/localization):

```php	
echo__('Welcome to our application');
 
echo__('messages.welcome');
```
If the specified translation string or key does not exist, the `__` function will return the given value. So, using the example above, the `__` function would return `messages.welcome` if that translation key does not exist.

#### [`class_basename()`](#method-class-basename)
The `class_basename` function returns the class name of the given class with the class's namespace removed:

```php	
$class=class_basename('Foo\Bar\Baz');
 
// Baz
```
#### [`e()`](#method-e)
The `e` function runs PHP's `htmlspecialchars` function with the `double_encode` option set to `true` by default:

```php	
echoe('<html>foo</html>');
 
// &lt;html&gt;foo&lt;/html&gt;
```
#### [`preg_replace_array()`](#method-preg-replace-array)
The `preg_replace_array` function replaces a given pattern in the string sequentially using an array:

```php	
$string='The event will take place between :start and :end';
 
$replaced=preg_replace_array('/:[a-z_]+/',['8:30','9:00'],$string);
 
// The event will take place between 8:30 and 9:00
```
#### [`Str::after()`](#method-str-after)
The `Str::after` method returns everything after the given value in a string. The entire string will be returned if the value does not exist within the string:

```php	
use Illuminate\Support\Str;
 
$slice=Str::after('This is my name', 'This is');
 
// ' my name'
```
#### [`Str::afterLast()`](#method-str-after-last)
The `Str::afterLast` method returns everything after the last occurrence of the given value in a string. The entire string will be returned if the value does not exist within the string:

```php	
use Illuminate\Support\Str;
 
$slice=Str::afterLast('App\Http\Controllers\Controller', '\\');
 
// 'Controller'
```
#### [`Str::ascii()`](#method-str-ascii)
The `Str::ascii` method will attempt to transliterate the string into an ASCII value:

```php	
use Illuminate\Support\Str;
 
$slice=Str::ascii('û');
 
// 'u'
```
#### [`Str::before()`](#method-str-before)
The `Str::before` method returns everything before the given value in a string:

```php	
use Illuminate\Support\Str;
 
$slice=Str::before('This is my name', 'my name');
 
// 'This is '
```
#### [`Str::beforeLast()`](#method-str-before-last)
The `Str::beforeLast` method returns everything before the last occurrence of the given value in a string:

```php	
use Illuminate\Support\Str;
 
$slice=Str::beforeLast('This is my name', 'is');
 
// 'This '
```
#### [`Str::between()`](#method-str-between)
The `Str::between` method returns the portion of a string between two values:

```php	
use Illuminate\Support\Str;
 
$slice=Str::between('This is my name', 'This', 'name');
 
// ' is my '
```
#### [`Str::betweenFirst()`](#method-str-between-first)
The `Str::betweenFirst` method returns the smallest possible portion of a string between two values:

```php	
use Illuminate\Support\Str;
 
$slice=Str::betweenFirst('[a] bc [d]', '[', ']');
 
// 'a'
```
#### [`Str::camel()`](#method-camel-case)
The `Str::camel` method converts the given string to `camelCase`:

```php	
use Illuminate\Support\Str;
 
$converted=Str::camel('foo_bar');
 
// fooBar
```
#### [`Str::contains()`](#method-str-contains)
The `Str::contains` method determines if the given string contains the given value. This method is case sensitive:

```php	
use Illuminate\Support\Str;
 
$contains=Str::contains('This is my name', 'my');
 
// true
```
You may also pass an array of values to determine if the given string contains any of the values in the array:

```php	
use Illuminate\Support\Str;
 
$contains=Str::contains('This is my name', ['my', 'foo']);
 
// true
```
#### [`Str::containsAll()`](#method-str-contains-all)
The `Str::containsAll` method determines if the given string contains all of the values in a given array:

```php	
use Illuminate\Support\Str;
 
$containsAll=Str::containsAll('This is my name', ['my', 'name']);
 
// true
```
#### [`Str::endsWith()`](#method-ends-with)
The `Str::endsWith` method determines if the given string ends with the given value:

```php	
use Illuminate\Support\Str;
 
$result=Str::endsWith('This is my name', 'name');
 
// true
```
You may also pass an array of values to determine if the given string ends with any of the values in the array:

```php	
use Illuminate\Support\Str;
 
$result=Str::endsWith('This is my name', ['name', 'foo']);
 
// true
 
$result=Str::endsWith('This is my name', ['this', 'foo']);
 
// false
```
#### [`Str::excerpt()`](#method-excerpt)
The `Str::excerpt` method extracts an excerpt from a given string that matches the first instance of a phrase within that string:

```php	
use Illuminate\Support\Str;
 
$excerpt=Str::excerpt('This is my name', 'my', [
'radius'=>3
]);
 
// '...is my na...'
```
The `radius` option, which defaults to `100`, allows you to define the number of characters that should appear on each side of the truncated string.

In addition, you may use the `omission` option to define the string that will be prepended and appended to the truncated string:

```php	
use Illuminate\Support\Str;
 
$excerpt=Str::excerpt('This is my name', 'name', [
'radius'=>3,
'omission'=>'(...) '
]);
 
// '(...) my name'
```
#### [`Str::finish()`](#method-str-finish)
The `Str::finish` method adds a single instance of the given value to a string if it does not already end with that value:

```php	
use Illuminate\Support\Str;
 
$adjusted=Str::finish('this/string', '/');
 
// this/string/
 
$adjusted=Str::finish('this/string/', '/');
 
// this/string/
```
#### [`Str::headline()`](#method-str-headline)
The `Str::headline` method will convert strings delimited by casing, hyphens, or underscores into a space delimited string with each word's first letter capitalized:

```php	
use Illuminate\Support\Str;
 
$headline=Str::headline('steve_jobs');
 
// Steve Jobs
 
$headline=Str::headline('EmailNotificationSent');
 
// Email Notification Sent
```
#### [`Str::inlineMarkdown()`](#method-str-inline-markdown)
The `Str::inlineMarkdown` method converts GitHub flavored Markdown into inline HTML using [CommonMark](https://commonmark.thephpleague.com/). However, unlike the `markdown` method, it does not wrap all generated HTML in a block-level element:

```php	
use Illuminate\Support\Str;
 
$html=Str::inlineMarkdown('**Laravel**');
 
// <strong>Laravel</strong>
```
#### [`Str::is()`](#method-str-is)
The `Str::is` method determines if a given string matches a given pattern. Asterisks may be used as wildcard values:

```php	
use Illuminate\Support\Str;
 
$matches=Str::is('foo*', 'foobar');
 
// true
 
$matches=Str::is('baz*', 'foobar');
 
// false
```
#### [`Str::isAscii()`](#method-str-is-ascii)
The `Str::isAscii` method determines if a given string is 7 bit ASCII:

```php	
use Illuminate\Support\Str;
 
$isAscii=Str::isAscii('Taylor');
 
// true
 
$isAscii=Str::isAscii('ü');
 
// false
```
#### [`Str::isJson()`](#method-str-is-json)
The `Str::isJson` method determines if the given string is valid JSON:

```php	
use Illuminate\Support\Str;
 
$result=Str::isJson('[1,2,3]');
 
// true
 
$result=Str::isJson('{"first": "John", "last": "Doe"}');
 
// true
 
$result=Str::isJson('{first: "John", last: "Doe"}');
 
// false
```
#### [`Str::isUlid()`](#method-str-is-ulid)
The `Str::isUlid` method determines if the given string is a valid ULID:

```php	
use Illuminate\Support\Str;
 
$isUlid=Str::isUlid('01gd6r360bp37zj17nxb55yv40');
 
// true
 
$isUlid=Str::isUlid('laravel');
 
// false
```
#### [`Str::isUuid()`](#method-str-is-uuid)
The `Str::isUuid` method determines if the given string is a valid UUID:

```php	
use Illuminate\Support\Str;
 
$isUuid=Str::isUuid('a0a2a2d2-0b87-4a18-83f2-2529882be2de');
 
// true
 
$isUuid=Str::isUuid('laravel');
 
// false
```
#### [`Str::kebab()`](#method-kebab-case)
The `Str::kebab` method converts the given string to `kebab-case`:

```php	
use Illuminate\Support\Str;
 
$converted=Str::kebab('fooBar');
 
// foo-bar
```
#### [`Str::lcfirst()`](#method-str-lcfirst)
The `Str::lcfirst` method returns the given string with the first character lowercased:

```php	
use Illuminate\Support\Str;
 
$string=Str::lcfirst('Foo Bar');
 
// foo Bar
```
#### [`Str::length()`](#method-str-length)
The `Str::length` method returns the length of the given string:

```php	
use Illuminate\Support\Str;
 
$length=Str::length('Laravel');
 
// 7
```
#### [`Str::limit()`](#method-str-limit)
The `Str::limit` method truncates the given string to the specified length:

```php	
use Illuminate\Support\Str;
 
$truncated=Str::limit('The quick brown fox jumps over the lazy dog', 20);
 
// The quick brown fox...
```
You may pass a third argument to the method to change the string that will be appended to the end of the truncated string:

```php	
use Illuminate\Support\Str;
 
$truncated=Str::limit('The quick brown fox jumps over the lazy dog', 20, ' (...)');
 
// The quick brown fox (...)
```
#### [`Str::lower()`](#method-str-lower)
The `Str::lower` method converts the given string to lowercase:

```php	
use Illuminate\Support\Str;
 
$converted=Str::lower('LARAVEL');
 
// laravel
```
#### [`Str::markdown()`](#method-str-markdown)
The `Str::markdown` method converts GitHub flavored Markdown into HTML using [CommonMark](https://commonmark.thephpleague.com/):

```php	
use Illuminate\Support\Str;
 
$html=Str::markdown('# Laravel');
 
// <h1>Laravel</h1>
 
$html=Str::markdown('# Taylor <b>Otwell</b>', [
'html_input'=>'strip',
]);
 
// <h1>Taylor Otwell</h1>
```
#### [`Str::mask()`](#method-str-mask)
The `Str::mask` method masks a portion of a string with a repeated character, and may be used to obfuscate segments of strings such as email addresses and phone numbers:

```php	
use Illuminate\Support\Str;
 
$string=Str::mask('[[email protected]](/cdn-cgi/l/email-protection)', '*', 3);
 
// tay***************
```
If needed, you provide a negative number as the third argument to the `mask` method, which will instruct the method to begin masking at the given distance from the end of the string:

```php	
$string=Str::mask('[[email protected]](/cdn-cgi/l/email-protection)', '*', -15, 3);
 
// tay***@example.com
```
#### [`Str::orderedUuid()`](#method-str-ordered-uuid)
The `Str::orderedUuid` method generates a "timestamp first" UUID that may be efficiently stored in an indexed database column. Each UUID that is generated using this method will be sorted after UUIDs previously generated using the method:

```php	
use Illuminate\Support\Str;
 
return (string) Str::orderedUuid();
```
#### [`Str::padBoth()`](#method-str-padboth)
The `Str::padBoth` method wraps PHP's `str_pad` function, padding both sides of a string with another string until the final string reaches a desired length:

```php	
use Illuminate\Support\Str;
 
$padded=Str::padBoth('James', 10, '_');
 
// '__James___'
 
$padded=Str::padBoth('James', 10);
 
// ' James '
```
#### [`Str::padLeft()`](#method-str-padleft)
The `Str::padLeft` method wraps PHP's `str_pad` function, padding the left side of a string with another string until the final string reaches a desired length:

```php	
use Illuminate\Support\Str;
 
$padded=Str::padLeft('James', 10, '-=');
 
// '-=-=-James'
 
$padded=Str::padLeft('James', 10);
 
// ' James'
```
#### [`Str::padRight()`](#method-str-padright)
The `Str::padRight` method wraps PHP's `str_pad` function, padding the right side of a string with another string until the final string reaches a desired length:

```php	
use Illuminate\Support\Str;
 
$padded=Str::padRight('James', 10, '-');
 
// 'James-----'
 
$padded=Str::padRight('James', 10);
 
// 'James '
```
#### [`Str::password()`](#method-str-password)
The `Str::password` method may be used to generate a secure, random password of a given length. The password will consist of a combination of letters, numbers, symbols, and spaces. By default, passwords are 32 characters long:

```php	
use Illuminate\Support\Str;
 
$password=Str::password();
 
// 'EbJo2vE-AS:U,$%_gkrV4n,q~1xy/-_4'
 
$password=Str::password(12);
 
// 'qwuar>#V|i]N'
```
#### [`Str::plural()`](#method-str-plural)
The `Str::plural` method converts a singular word string to its plural form. This function supports [any of the languages support by Laravel's pluralizer](/docs/master/localization#pluralization-language):

```php	
use Illuminate\Support\Str;
 
$plural=Str::plural('car');
 
// cars
 
$plural=Str::plural('child');
 
// children
```
You may provide an integer as a second argument to the function to retrieve the singular or plural form of the string:

```php	
use Illuminate\Support\Str;
 
$plural=Str::plural('child', 2);
 
// children
 
$singular=Str::plural('child', 1);
 
// child
```
#### [`Str::pluralStudly()`](#method-str-plural-studly)
The `Str::pluralStudly` method converts a singular word string formatted in studly caps case to its plural form. This function supports [any of the languages support by Laravel's pluralizer](/docs/master/localization#pluralization-language):

```php	
use Illuminate\Support\Str;
 
$plural=Str::pluralStudly('VerifiedHuman');
 
// VerifiedHumans
 
$plural=Str::pluralStudly('UserFeedback');
 
// UserFeedback
```
You may provide an integer as a second argument to the function to retrieve the singular or plural form of the string:

```php	
use Illuminate\Support\Str;
 
$plural=Str::pluralStudly('VerifiedHuman', 2);
 
// VerifiedHumans
 
$singular=Str::pluralStudly('VerifiedHuman', 1);
 
// VerifiedHuman
```
#### [`Str::random()`](#method-str-random)
The `Str::random` method generates a random string of the specified length. This function uses PHP's `random_bytes` function:

```php	
use Illuminate\Support\Str;
 
$random=Str::random(40);
```
#### [`Str::remove()`](#method-str-remove)
The `Str::remove` method removes the given value or array of values from the string:

```php	
use Illuminate\Support\Str;
 
$string='Peter Piper picked a peck of pickled peppers.';
 
$removed=Str::remove('e', $string);
 
// Ptr Pipr pickd a pck of pickld ppprs.
```
You may also pass `false` as a third argument to the `remove` method to ignore case when removing strings.

#### [`Str::replace()`](#method-str-replace)
The `Str::replace` method replaces a given string within the string:

```php	
use Illuminate\Support\Str;
 
$string='Laravel 8.x';
 
$replaced=Str::replace('8.x', '9.x', $string);
 
// Laravel 9.x
```
#### [`Str::replaceArray()`](#method-str-replace-array)
The `Str::replaceArray` method replaces a given value in the string sequentially using an array:

```php	
use Illuminate\Support\Str;
 
$string='The event will take place between ? and ?';
 
$replaced=Str::replaceArray('?', ['8:30', '9:00'], $string);
 
// The event will take place between 8:30 and 9:00
```
#### [`Str::replaceFirst()`](#method-str-replace-first)
The `Str::replaceFirst` method replaces the first occurrence of a given value in a string:

```php	
use Illuminate\Support\Str;
 
$replaced=Str::replaceFirst('the', 'a', 'the quick brown fox jumps over the lazy dog');
 
// a quick brown fox jumps over the lazy dog
```
#### [`Str::replaceLast()`](#method-str-replace-last)
The `Str::replaceLast` method replaces the last occurrence of a given value in a string:

```php	
use Illuminate\Support\Str;
 
$replaced=Str::replaceLast('the', 'a', 'the quick brown fox jumps over the lazy dog');
 
// the quick brown fox jumps over a lazy dog
```
#### [`Str::reverse()`](#method-str-reverse)
The `Str::reverse` method reverses the given string:

```php	
use Illuminate\Support\Str;
 
$reversed=Str::reverse('Hello World');
 
// dlroW olleH
```
#### [`Str::singular()`](#method-str-singular)
The `Str::singular` method converts a string to its singular form. This function supports [any of the languages support by Laravel's pluralizer](/docs/master/localization#pluralization-language):

```php	
use Illuminate\Support\Str;
 
$singular=Str::singular('cars');
 
// car
 
$singular=Str::singular('children');
 
// child
```
#### [`Str::slug()`](#method-str-slug)
The `Str::slug` method generates a URL friendly "slug" from the given string:

```php	
use Illuminate\Support\Str;
 
$slug=Str::slug('Laravel 5 Framework', '-');
 
// laravel-5-framework
```
#### [`Str::snake()`](#method-snake-case)
The `Str::snake` method converts the given string to `snake_case`:

```php	
use Illuminate\Support\Str;
 
$converted=Str::snake('fooBar');
 
// foo_bar
 
$converted=Str::snake('fooBar', '-');
 
// foo-bar
```
#### [`Str::squish()`](#method-str-squish)
The `Str::squish` method removes all extraneous white space from a string, including extraneous white space between words:

```php	
use Illuminate\Support\Str;
 
$string=Str::squish(' laravel framework ');
 
// laravel framework
```
#### [`Str::start()`](#method-str-start)
The `Str::start` method adds a single instance of the given value to a string if it does not already start with that value:

```php	
use Illuminate\Support\Str;
 
$adjusted=Str::start('this/string', '/');
 
// /this/string
 
$adjusted=Str::start('/this/string', '/');
 
// /this/string
```
#### [`Str::startsWith()`](#method-starts-with)
The `Str::startsWith` method determines if the given string begins with the given value:

```php	
use Illuminate\Support\Str;
 
$result=Str::startsWith('This is my name', 'This');
 
// true
```
If an array of possible values is passed, the `startsWith` method will return `true` if the string begins with any of the given values:

```php	
$result=Str::startsWith('This is my name', ['This', 'That', 'There']);
 
// true
```
#### [`Str::studly()`](#method-studly-case)
The `Str::studly` method converts the given string to `StudlyCase`:

```php	
use Illuminate\Support\Str;
 
$converted=Str::studly('foo_bar');
 
// FooBar
```
#### [`Str::substr()`](#method-str-substr)
The `Str::substr` method returns the portion of string specified by the start and length parameters:

```php	
use Illuminate\Support\Str;
 
$converted=Str::substr('The Laravel Framework', 4, 7);
 
// Laravel
```
#### [`Str::substrCount()`](#method-str-substrcount)
The `Str::substrCount` method returns the number of occurrences of a given value in the given string:

```php	
use Illuminate\Support\Str;
 
$count=Str::substrCount('If you like ice cream, you will like snow cones.', 'like');
 
// 2
```
#### [`Str::substrReplace()`](#method-str-substrreplace)
The `Str::substrReplace` method replaces text within a portion of a string, starting at the position specified by the third argument and replacing the number of characters specified by the fourth argument. Passing `0` to the method's fourth argument will insert the string at the specified position without replacing any of the existing characters in the string:

```php	
use Illuminate\Support\Str;
 
$result=Str::substrReplace('1300', ':', 2);
// 13:
 
$result=Str::substrReplace('1300', ':', 2, 0);
// 13:00
```
#### [`Str::swap()`](#method-str-swap)
The `Str::swap` method replaces multiple values in the given string using PHP's `strtr` function:

```php	
use Illuminate\Support\Str;
 
$string=Str::swap([
'Tacos'=>'Burritos',
'great'=>'fantastic',
], 'Tacos are great!');
 
// Burritos are fantastic!
```
#### [`Str::title()`](#method-title-case)
The `Str::title` method converts the given string to `Title Case`:

```php	
use Illuminate\Support\Str;
 
$converted=Str::title('a nice title uses the correct case');
 
// A Nice Title Uses The Correct Case
```
#### [`Str::toHtmlString()`](#method-str-to-html-string)
The `Str::toHtmlString` method converts the string instance to an instance of `Illuminate\Support\HtmlString`, which may be displayed in Blade templates:

```php	
use Illuminate\Support\Str;
 
$htmlString=Str::of('Nuno Maduro')->toHtmlString();
```
#### [`Str::ucfirst()`](#method-str-ucfirst)
The `Str::ucfirst` method returns the given string with the first character capitalized:

```php	
use Illuminate\Support\Str;
 
$string=Str::ucfirst('foo bar');
 
// Foo bar
```
#### [`Str::ucsplit()`](#method-str-ucsplit)
The `Str::ucsplit` method splits the given string into an array by uppercase characters:

```php	
use Illuminate\Support\Str;
 
$segments=Str::ucsplit('FooBar');
 
// [0 => 'Foo', 1 => 'Bar']
```
#### [`Str::upper()`](#method-str-upper)
The `Str::upper` method converts the given string to uppercase:

```php	
use Illuminate\Support\Str;
 
$string=Str::upper('laravel');
 
// LARAVEL
```
#### [`Str::ulid()`](#method-str-ulid)
The `Str::ulid` method generates a ULID:

```php	
use Illuminate\Support\Str;
 
return (string) Str::ulid();
 
// 01gd6r360bp37zj17nxb55yv40
```
#### [`Str::uuid()`](#method-str-uuid)
The `Str::uuid` method generates a UUID (version 4):

```php	
use Illuminate\Support\Str;
 
return (string) Str::uuid();
```
#### [`Str::wordCount()`](#method-str-word-count)
The `Str::wordCount` method returns the number of words that a string contains:

```php	
use Illuminate\Support\Str;
 
Str::wordCount('Hello, world!'); // 2
```
#### [`Str::words()`](#method-str-words)
The `Str::words` method limits the number of words in a string. An additional string may be passed to this method via its third argument to specify which string should be appended to the end of the truncated string:

```php	
use Illuminate\Support\Str;
 
returnStr::words('Perfectly balanced, as all things should be.', 3, ' >>>');
 
// Perfectly balanced, as >>>
```
#### [`str()`](#method-str)
The `str` function returns a new `Illuminate\Support\Stringable` instance of the given string. This function is equivalent to the `Str::of` method:

```php	
$string=str('Taylor')->append(' Otwell');
 
// 'Taylor Otwell'
```
If no argument is provided to the `str` function, the function returns an instance of `Illuminate\Support\Str`:

```php	
$snake=str()->snake('FooBar');
 
// 'foo_bar'
```
#### [`trans()`](#method-trans)
The `trans` function translates the given translation key using your [language files](/docs/master/localization):

```php	
echotrans('messages.welcome');
```
If the specified translation key does not exist, the `trans` function will return the given key. So, using the example above, the `trans` function would return `messages.welcome` if the translation key does not exist.

#### [`trans_choice()`](#method-trans-choice)
The `trans_choice` function translates the given translation key with inflection:

```php	
echotrans_choice('messages.notifications',$unreadCount);
```
If the specified translation key does not exist, the `trans_choice` function will return the given key. So, using the example above, the `trans_choice` function would return `messages.notifications` if the translation key does not exist.

## [Fluent Strings](#fluent-strings)
Fluent strings provide a more fluent, object-oriented interface for working with string values, allowing you to chain multiple string operations together using a more readable syntax compared to traditional string operations.

#### [`after`](#method-fluent-str-after)
The `after` method returns everything after the given value in a string. The entire string will be returned if the value does not exist within the string:

```php	
use Illuminate\Support\Str;
 
$slice=Str::of('This is my name')->after('This is');
 
// ' my name'
```
#### [`afterLast`](#method-fluent-str-after-last)
The `afterLast` method returns everything after the last occurrence of the given value in a string. The entire string will be returned if the value does not exist within the string:

```php	
use Illuminate\Support\Str;
 
$slice=Str::of('App\Http\Controllers\Controller')->afterLast('\\');
 
// 'Controller'
```
#### [`append`](#method-fluent-str-append)
The `append` method appends the given values to the string:

```php	
use Illuminate\Support\Str;
 
$string=Str::of('Taylor')->append(' Otwell');
 
// 'Taylor Otwell'
```
#### [`ascii`](#method-fluent-str-ascii)
The `ascii` method will attempt to transliterate the string into an ASCII value:

```php	
use Illuminate\Support\Str;
 
$string=Str::of('ü')->ascii();
 
// 'u'
```
#### [`basename`](#method-fluent-str-basename)
The `basename` method will return the trailing name component of the given string:

```php	
use Illuminate\Support\Str;
 
$string=Str::of('/foo/bar/baz')->basename();
 
// 'baz'
```
If needed, you may provide an "extension" that will be removed from the trailing component:

```php	
use Illuminate\Support\Str;
 
$string=Str::of('/foo/bar/baz.jpg')->basename('.jpg');
 
// 'baz'
```
#### [`before`](#method-fluent-str-before)
The `before` method returns everything before the given value in a string:

```php	
use Illuminate\Support\Str;
 
$slice=Str::of('This is my name')->before('my name');
 
// 'This is '
```
#### [`beforeLast`](#method-fluent-str-before-last)
The `beforeLast` method returns everything before the last occurrence of the given value in a string:

```php	
use Illuminate\Support\Str;
 
$slice=Str::of('This is my name')->beforeLast('is');
 
// 'This '
```
#### [`between`](#method-fluent-str-between)
The `between` method returns the portion of a string between two values:

```php	
use Illuminate\Support\Str;
 
$converted=Str::of('This is my name')->between('This', 'name');
 
// ' is my '
```
#### [`betweenFirst`](#method-fluent-str-between-first)
The `betweenFirst` method returns the smallest possible portion of a string between two values:

```php	
use Illuminate\Support\Str;
 
$converted=Str::of('[a] bc [d]')->betweenFirst('[', ']');
 
// 'a'
```
#### [`camel`](#method-fluent-str-camel)
The `camel` method converts the given string to `camelCase`:

```php	
use Illuminate\Support\Str;
 
$converted=Str::of('foo_bar')->camel();
 
// fooBar
```
#### [`classBasename`](#method-fluent-str-class-basename)
The `classBasename` method returns the class name of the given class with the class's namespace removed:

```php	
use Illuminate\Support\Str;
 
$class=Str::of('Foo\Bar\Baz')->classBasename();
 
// Baz
```
#### [`contains`](#method-fluent-str-contains)
The `contains` method determines if the given string contains the given value. This method is case sensitive:

```php	
use Illuminate\Support\Str;
 
$contains=Str::of('This is my name')->contains('my');
 
// true
```
You may also pass an array of values to determine if the given string contains any of the values in the array:

```php	
use Illuminate\Support\Str;
 
$contains=Str::of('This is my name')->contains(['my', 'foo']);
 
// true
```
#### [`containsAll`](#method-fluent-str-contains-all)
The `containsAll` method determines if the given string contains all of the values in the given array:

```php	
use Illuminate\Support\Str;
 
$containsAll=Str::of('This is my name')->containsAll(['my', 'name']);
 
// true
```
#### [`dirname`](#method-fluent-str-dirname)
The `dirname` method returns the parent directory portion of the given string:

```php	
use Illuminate\Support\Str;
 
$string=Str::of('/foo/bar/baz')->dirname();
 
// '/foo/bar'
```
If necessary, you may specify how many directory levels you wish to trim from the string:

```php	
use Illuminate\Support\Str;
 
$string=Str::of('/foo/bar/baz')->dirname(2);
 
// '/foo'
```
#### [`excerpt`](#method-fluent-str-excerpt)
The `excerpt` method extracts an excerpt from the string that matches the first instance of a phrase within that string:

```php	
use Illuminate\Support\Str;
 
$excerpt=Str::of('This is my name')->excerpt('my', [
'radius'=>3
]);
 
// '...is my na...'
```
The `radius` option, which defaults to `100`, allows you to define the number of characters that should appear on each side of the truncated string.

In addition, you may use the `omission` option to change the string that will be prepended and appended to the truncated string:

```php	
use Illuminate\Support\Str;
 
$excerpt=Str::of('This is my name')->excerpt('name', [
'radius'=>3,
'omission'=>'(...) '
]);
 
// '(...) my name'
```
#### [`endsWith`](#method-fluent-str-ends-with)
The `endsWith` method determines if the given string ends with the given value:

```php	
use Illuminate\Support\Str;
 
$result=Str::of('This is my name')->endsWith('name');
 
// true
```
You may also pass an array of values to determine if the given string ends with any of the values in the array:

```php	
use Illuminate\Support\Str;
 
$result=Str::of('This is my name')->endsWith(['name', 'foo']);
 
// true
 
$result=Str::of('This is my name')->endsWith(['this', 'foo']);
 
// false
```
#### [`exactly`](#method-fluent-str-exactly)
The `exactly` method determines if the given string is an exact match with another string:

```php	
use Illuminate\Support\Str;
 
$result=Str::of('Laravel')->exactly('Laravel');
 
// true
```
#### [`explode`](#method-fluent-str-explode)
The `explode` method splits the string by the given delimiter and returns a collection containing each section of the split string:

```php	
use Illuminate\Support\Str;
 
$collection=Str::of('foo bar baz')->explode('');
 
// collect(['foo', 'bar', 'baz'])
```
#### [`finish`](#method-fluent-str-finish)
The `finish` method adds a single instance of the given value to a string if it does not already end with that value:

```php	
use Illuminate\Support\Str;
 
$adjusted=Str::of('this/string')->finish('/');
 
// this/string/
 
$adjusted=Str::of('this/string/')->finish('/');
 
// this/string/
```
#### [`headline`](#method-fluent-str-headline)
The `headline` method will convert strings delimited by casing, hyphens, or underscores into a space delimited string with each word's first letter capitalized:

```php	
use Illuminate\Support\Str;
 
$headline=Str::of('taylor_otwell')->headline();
 
// Taylor Otwell
 
$headline=Str::of('EmailNotificationSent')->headline();
 
// Email Notification Sent
```
#### [`inlineMarkdown`](#method-fluent-str-inline-markdown)
The `inlineMarkdown` method converts GitHub flavored Markdown into inline HTML using [CommonMark](https://commonmark.thephpleague.com/). However, unlike the `markdown` method, it does not wrap all generated HTML in a block-level element:

```php	
use Illuminate\Support\Str;
 
$html=Str::of('**Laravel**')->inlineMarkdown();
 
// <strong>Laravel</strong>
```
#### [`is`](#method-fluent-str-is)
The `is` method determines if a given string matches a given pattern. Asterisks may be used as wildcard values

```php	
use Illuminate\Support\Str;
 
$matches=Str::of('foobar')->is('foo*');
 
// true
 
$matches=Str::of('foobar')->is('baz*');
 
// false
```
#### [`isAscii`](#method-fluent-str-is-ascii)
The `isAscii` method determines if a given string is an ASCII string:

```php	
use Illuminate\Support\Str;
 
$result=Str::of('Taylor')->isAscii();
 
// true
 
$result=Str::of('ü')->isAscii();
 
// false
```
#### [`isEmpty`](#method-fluent-str-is-empty)
The `isEmpty` method determines if the given string is empty:

```php	
use Illuminate\Support\Str;
 
$result=Str::of('')->trim()->isEmpty();
 
// true
 
$result=Str::of('Laravel')->trim()->isEmpty();
 
// false
```
#### [`isNotEmpty`](#method-fluent-str-is-not-empty)
The `isNotEmpty` method determines if the given string is not empty:

```php	
use Illuminate\Support\Str;
 
$result=Str::of('')->trim()->isNotEmpty();
 
// false
 
$result=Str::of('Laravel')->trim()->isNotEmpty();
 
// true
```
#### [`isJson`](#method-fluent-str-is-json)
The `isJson` method determines if a given string is valid JSON:

```php	
use Illuminate\Support\Str;
 
$result=Str::of('[1,2,3]')->isJson();
 
// true
 
$result=Str::of('{"first": "John", "last": "Doe"}')->isJson();
 
// true
 
$result=Str::of('{first: "John", last: "Doe"}')->isJson();
 
// false
```
#### [`isUlid`](#method-fluent-str-is-ulid)
The `isUlid` method determines if a given string is a ULID:

```php	
use Illuminate\Support\Str;
 
$result=Str::of('01gd6r360bp37zj17nxb55yv40')->isUlid();
 
// true
 
$result=Str::of('Taylor')->isUlid();
 
// false
```
#### [`isUuid`](#method-fluent-str-is-uuid)
The `isUuid` method determines if a given string is a UUID:

```php	
use Illuminate\Support\Str;
 
$result=Str::of('5ace9ab9-e9cf-4ec6-a19d-5881212a452c')->isUuid();
 
// true
 
$result=Str::of('Taylor')->isUuid();
 
// false
```
#### [`kebab`](#method-fluent-str-kebab)
The `kebab` method converts the given string to `kebab-case`:

```php	
use Illuminate\Support\Str;
 
$converted=Str::of('fooBar')->kebab();
 
// foo-bar
```
#### [`lcfirst`](#method-fluent-str-lcfirst)
The `lcfirst` method returns the given string with the first character lowercased:

```php	
use Illuminate\Support\Str;
 
$string=Str::of('Foo Bar')->lcfirst();
 
// foo Bar
```
#### [`length`](#method-fluent-str-length)
The `length` method returns the length of the given string:

```php	
use Illuminate\Support\Str;
 
$length=Str::of('Laravel')->length();
 
// 7
```
#### [`limit`](#method-fluent-str-limit)
The `limit` method truncates the given string to the specified length:

```php	
use Illuminate\Support\Str;
 
$truncated=Str::of('The quick brown fox jumps over the lazy dog')->limit(20);
 
// The quick brown fox...
```
You may also pass a second argument to change the string that will be appended to the end of the truncated string:

```php	
use Illuminate\Support\Str;
 
$truncated=Str::of('The quick brown fox jumps over the lazy dog')->limit(20, ' (...)');
 
// The quick brown fox (...)
```
#### [`lower`](#method-fluent-str-lower)
The `lower` method converts the given string to lowercase:

```php	
use Illuminate\Support\Str;
 
$result=Str::of('LARAVEL')->lower();
 
// 'laravel'
```
#### [`ltrim`](#method-fluent-str-ltrim)
The `ltrim` method trims the left side of the string:

```php	
use Illuminate\Support\Str;
 
$string=Str::of(' Laravel ')->ltrim();
 
// 'Laravel '
 
$string=Str::of('/Laravel/')->ltrim('/');
 
// 'Laravel/'
```
#### [`markdown`](#method-fluent-str-markdown)
The `markdown` method converts GitHub flavored Markdown into HTML:

```php	
use Illuminate\Support\Str;
 
$html=Str::of('# Laravel')->markdown();
 
// <h1>Laravel</h1>
 
$html=Str::of('# Taylor <b>Otwell</b>')->markdown([
'html_input'=>'strip',
]);
 
// <h1>Taylor Otwell</h1>
```
#### [`mask`](#method-fluent-str-mask)
The `mask` method masks a portion of a string with a repeated character, and may be used to obfuscate segments of strings such as email addresses and phone numbers:

```php	
use Illuminate\Support\Str;
 
$string=Str::of('[[email protected]](/cdn-cgi/l/email-protection)')->mask('*', 3);
 
// tay***************
```
If needed, you may provide negative numbers as the third or fourth argument to the `mask` method, which will instruct the method to begin masking at the given distance from the end of the string:

```php	
$string=Str::of('[[email protected]](/cdn-cgi/l/email-protection)')->mask('*', -15, 3);
 
// tay***@example.com
 
$string=Str::of('[[email protected]](/cdn-cgi/l/email-protection)')->mask('*', 4, -4);
 
// tayl**********.com
```
#### [`match`](#method-fluent-str-match)
The `match` method will return the portion of a string that matches a given regular expression pattern:

```php	
use Illuminate\Support\Str;
 
$result=Str::of('foo bar')->match('/bar/');
 
// 'bar'
 
$result=Str::of('foo bar')->match('/foo (.*)/');
 
// 'bar'
```
#### [`matchAll`](#method-fluent-str-match-all)
The `matchAll` method will return a collection containing the portions of a string that match a given regular expression pattern:

```php	
use Illuminate\Support\Str;
 
$result=Str::of('bar foo bar')->matchAll('/bar/');
 
// collect(['bar', 'bar'])
```
If you specify a matching group within the expression, Laravel will return a collection of that group's matches:

```php	
use Illuminate\Support\Str;
 
$result=Str::of('bar fun bar fly')->matchAll('/f(\w*)/');
 
// collect(['un', 'ly']);
```
If no matches are found, an empty collection will be returned.

#### [`newLine`](#method-fluent-str-new-line)
The `newLine` method appends an "end of line" character to a string:

```php	
use Illuminate\Support\Str;
 
$padded=Str::of('Laravel')->newLine()->append('Framework');
 
// 'Laravel
// Framework'
```
#### [`padBoth`](#method-fluent-str-padboth)
The `padBoth` method wraps PHP's `str_pad` function, padding both sides of a string with another string until the final string reaches the desired length:

```php	
use Illuminate\Support\Str;
 
$padded=Str::of('James')->padBoth(10, '_');
 
// '__James___'
 
$padded=Str::of('James')->padBoth(10);
 
// ' James '
```
#### [`padLeft`](#method-fluent-str-padleft)
The `padLeft` method wraps PHP's `str_pad` function, padding the left side of a string with another string until the final string reaches the desired length:

```php	
use Illuminate\Support\Str;
 
$padded=Str::of('James')->padLeft(10, '-=');
 
// '-=-=-James'
 
$padded=Str::of('James')->padLeft(10);
 
// ' James'
```
#### [`padRight`](#method-fluent-str-padright)
The `padRight` method wraps PHP's `str_pad` function, padding the right side of a string with another string until the final string reaches the desired length:

```php	
use Illuminate\Support\Str;
 
$padded=Str::of('James')->padRight(10, '-');
 
// 'James-----'
 
$padded=Str::of('James')->padRight(10);
 
// 'James '
```
#### [`pipe`](#method-fluent-str-pipe)
The `pipe` method allows you to transform the string by passing its current value to the given callable:

```php	
use Illuminate\Support\Str;
use Illuminate\Support\Stringable;
 
$hash=Str::of('Laravel')->pipe('md5')->prepend('Checksum: ');
 
// 'Checksum: a5c95b86291ea299fcbe64458ed12702'
 
$closure=Str::of('foo')->pipe(function(Stringable$str) {
return'bar';
});
 
// 'bar'
```
#### [`plural`](#method-fluent-str-plural)
The `plural` method converts a singular word string to its plural form. This function supports [any of the languages support by Laravel's pluralizer](/docs/master/localization#pluralization-language):

```php	
use Illuminate\Support\Str;
 
$plural=Str::of('car')->plural();
 
// cars
 
$plural=Str::of('child')->plural();
 
// children
```
You may provide an integer as a second argument to the function to retrieve the singular or plural form of the string:

```php	
use Illuminate\Support\Str;
 
$plural=Str::of('child')->plural(2);
 
// children
 
$plural=Str::of('child')->plural(1);
 
// child
```
#### [`prepend`](#method-fluent-str-prepend)
The `prepend` method prepends the given values onto the string:

```php	
use Illuminate\Support\Str;
 
$string=Str::of('Framework')->prepend('Laravel ');
 
// Laravel Framework
```
#### [`remove`](#method-fluent-str-remove)
The `remove` method removes the given value or array of values from the string:

```php	
use Illuminate\Support\Str;
 
$string=Str::of('Arkansas is quite beautiful!')->remove('quite');
 
// Arkansas is beautiful!
```
You may also pass `false` as a second parameter to ignore case when removing strings.

#### [`replace`](#method-fluent-str-replace)
The `replace` method replaces a given string within the string:

```php	
use Illuminate\Support\Str;
 
$replaced=Str::of('Laravel 6.x')->replace('6.x', '7.x');
 
// Laravel 7.x
```
#### [`replaceArray`](#method-fluent-str-replace-array)
The `replaceArray` method replaces a given value in the string sequentially using an array:

```php	
use Illuminate\Support\Str;
 
$string='The event will take place between ? and ?';
 
$replaced=Str::of($string)->replaceArray('?', ['8:30', '9:00']);
 
// The event will take place between 8:30 and 9:00
```
#### [`replaceFirst`](#method-fluent-str-replace-first)
The `replaceFirst` method replaces the first occurrence of a given value in a string:

```php	
use Illuminate\Support\Str;
 
$replaced=Str::of('the quick brown fox jumps over the lazy dog')->replaceFirst('the', 'a');
 
// a quick brown fox jumps over the lazy dog
```
#### [`replaceLast`](#method-fluent-str-replace-last)
The `replaceLast` method replaces the last occurrence of a given value in a string:

```php	
use Illuminate\Support\Str;
 
$replaced=Str::of('the quick brown fox jumps over the lazy dog')->replaceLast('the', 'a');
 
// the quick brown fox jumps over a lazy dog
```
#### [`replaceMatches`](#method-fluent-str-replace-matches)
The `replaceMatches` method replaces all portions of a string matching a pattern with the given replacement string:

```php	
use Illuminate\Support\Str;
 
$replaced=Str::of('(+1) 501-555-1000')->replaceMatches('/[^A-Za-z0-9]++/', '')
 
// '15015551000'
```
The `replaceMatches` method also accepts a closure that will be invoked with each portion of the string matching the given pattern, allowing you to perform the replacement logic within the closure and return the replaced value:

```php	
use Illuminate\Support\Str;
use Illuminate\Support\Stringable;
 
$replaced=Str::of('123')->replaceMatches('/\d/', function(array$matches) {
return'['.$matches[0].']';
});
 
// '[1][2][3]'
```
#### [`rtrim`](#method-fluent-str-rtrim)
The `rtrim` method trims the right side of the given string:

```php	
use Illuminate\Support\Str;
 
$string=Str::of(' Laravel ')->rtrim();
 
// ' Laravel'
 
$string=Str::of('/Laravel/')->rtrim('/');
 
// '/Laravel'
```
#### [`scan`](#method-fluent-str-scan)
The `scan` method parses input from a string into a collection according to a format supported by the [`sscanf` PHP function](https://www.php.net/manual/en/function.sscanf.php):

```php	
use Illuminate\Support\Str;
 
$collection=Str::of('filename.jpg')->scan('%[^.].%s');
 
// collect(['filename', 'jpg'])
```
#### [`singular`](#method-fluent-str-singular)
The `singular` method converts a string to its singular form. This function supports [any of the languages support by Laravel's pluralizer](/docs/master/localization#pluralization-language):

```php	
use Illuminate\Support\Str;
 
$singular=Str::of('cars')->singular();
 
// car
 
$singular=Str::of('children')->singular();
 
// child
```
#### [`slug`](#method-fluent-str-slug)
The `slug` method generates a URL friendly "slug" from the given string:

```php	
use Illuminate\Support\Str;
 
$slug=Str::of('Laravel Framework')->slug('-');
 
// laravel-framework
```
#### [`snake`](#method-fluent-str-snake)
The `snake` method converts the given string to `snake_case`:

```php	
use Illuminate\Support\Str;
 
$converted=Str::of('fooBar')->snake();
 
// foo_bar
```
#### [`split`](#method-fluent-str-split)
The `split` method splits a string into a collection using a regular expression:

```php	
use Illuminate\Support\Str;
 
$segments=Str::of('one, two, three')->split('/[\s,]+/');
 
// collect(["one", "two", "three"])
```
#### [`squish`](#method-fluent-str-squish)
The `squish` method removes all extraneous white space from a string, including extraneous white space between words:

```php	
use Illuminate\Support\Str;
 
$string=Str::of(' laravel framework ')->squish();
 
// laravel framework
```
#### [`start`](#method-fluent-str-start)
The `start` method adds a single instance of the given value to a string if it does not already start with that value:

```php	
use Illuminate\Support\Str;
 
$adjusted=Str::of('this/string')->start('/');
 
// /this/string
 
$adjusted=Str::of('/this/string')->start('/');
 
// /this/string
```
#### [`startsWith`](#method-fluent-str-starts-with)
The `startsWith` method determines if the given string begins with the given value:

```php	
use Illuminate\Support\Str;
 
$result=Str::of('This is my name')->startsWith('This');
 
// true
```
#### [`studly`](#method-fluent-str-studly)
The `studly` method converts the given string to `StudlyCase`:

```php	
use Illuminate\Support\Str;
 
$converted=Str::of('foo_bar')->studly();
 
// FooBar
```
#### [`substr`](#method-fluent-str-substr)
The `substr` method returns the portion of the string specified by the given start and length parameters:

```php	
use Illuminate\Support\Str;
 
$string=Str::of('Laravel Framework')->substr(8);
 
// Framework
 
$string=Str::of('Laravel Framework')->substr(8, 5);
 
// Frame
```
#### [`substrReplace`](#method-fluent-str-substrreplace)
The `substrReplace` method replaces text within a portion of a string, starting at the position specified by the second argument and replacing the number of characters specified by the third argument. Passing `0` to the method's third argument will insert the string at the specified position without replacing any of the existing characters in the string:

```php	
use Illuminate\Support\Str;
 
$string=Str::of('1300')->substrReplace(':', 2);
 
// 13:
 
$string=Str::of('The Framework')->substrReplace(' Laravel', 3, 0);
 
// The Laravel Framework
```
#### [`swap`](#method-fluent-str-swap)
The `swap` method replaces multiple values in the string using PHP's `strtr` function:

```php	
use Illuminate\Support\Str;
 
$string=Str::of('Tacos are great!')
->swap([
'Tacos'=>'Burritos',
'great'=>'fantastic',
 ]);
 
// Burritos are fantastic!
```
#### [`tap`](#method-fluent-str-tap)
The `tap` method passes the string to the given closure, allowing you to examine and interact with the string while not affecting the string itself. The original string is returned by the `tap` method regardless of what is returned by the closure:

```php	
use Illuminate\Support\Str;
use Illuminate\Support\Stringable;
 
$string=Str::of('Laravel')
->append(' Framework')
->tap(function(Stringable$string) {
dump('String after append: '.$string);
 })
->upper();
 
// LARAVEL FRAMEWORK
```
#### [`test`](#method-fluent-str-test)
The `test` method determines if a string matches the given regular expression pattern:

```php	
use Illuminate\Support\Str;
 
$result=Str::of('Laravel Framework')->test('/Laravel/');
 
// true
```
#### [`title`](#method-fluent-str-title)
The `title` method converts the given string to `Title Case`:

```php	
use Illuminate\Support\Str;
 
$converted=Str::of('a nice title uses the correct case')->title();
 
// A Nice Title Uses The Correct Case
```
#### [`trim`](#method-fluent-str-trim)
The `trim` method trims the given string:

```php	
use Illuminate\Support\Str;
 
$string=Str::of(' Laravel ')->trim();
 
// 'Laravel'
 
$string=Str::of('/Laravel/')->trim('/');
 
// 'Laravel'
```
#### [`ucfirst`](#method-fluent-str-ucfirst)
The `ucfirst` method returns the given string with the first character capitalized:

```php	
use Illuminate\Support\Str;
 
$string=Str::of('foo bar')->ucfirst();
 
// Foo bar
```
#### [`ucsplit`](#method-fluent-str-ucsplit)
The `ucsplit` method splits the given string into a collection by uppercase characters:

```php	
use Illuminate\Support\Str;
 
$string=Str::of('Foo Bar')->ucsplit();
 
// collect(['Foo', 'Bar'])
```
#### [`upper`](#method-fluent-str-upper)
The `upper` method converts the given string to uppercase:

```php	
use Illuminate\Support\Str;
 
$adjusted=Str::of('laravel')->upper();
 
// LARAVEL
```
#### [`when`](#method-fluent-str-when)
The `when` method invokes the given closure if a given condition is `true`. The closure will receive the fluent string instance:

```php	
use Illuminate\Support\Str;
use Illuminate\Support\Stringable;
 
$string=Str::of('Taylor')
->when(true, function(Stringable$string) {
return$string->append(' Otwell');
 });
 
// 'Taylor Otwell'
```
If necessary, you may pass another closure as the third parameter to the `when` method. This closure will execute if the condition parameter evaluates to `false`.

#### [`whenContains`](#method-fluent-str-when-contains)
The `whenContains` method invokes the given closure if the string contains the given value. The closure will receive the fluent string instance:

```php	
use Illuminate\Support\Str;
use Illuminate\Support\Stringable;
 
$string=Str::of('tony stark')
->whenContains('tony', function(Stringable$string) {
return$string->title();
 });
 
// 'Tony Stark'
```
If necessary, you may pass another closure as the third parameter to the `when` method. This closure will execute if the string does not contain the given value.

You may also pass an array of values to determine if the given string contains any of the values in the array:

```php	
use Illuminate\Support\Str;
use Illuminate\Support\Stringable;
 
$string=Str::of('tony stark')
->whenContains(['tony', 'hulk'], function(Stringable$string) {
return$string->title();
 });
 
// Tony Stark
```
#### [`whenContainsAll`](#method-fluent-str-when-contains-all)
The `whenContainsAll` method invokes the given closure if the string contains all of the given sub-strings. The closure will receive the fluent string instance:

```php	
use Illuminate\Support\Str;
use Illuminate\Support\Stringable;
 
$string=Str::of('tony stark')
->whenContainsAll(['tony', 'stark'], function(Stringable$string) {
return$string->title();
 });
 
// 'Tony Stark'
```
If necessary, you may pass another closure as the third parameter to the `when` method. This closure will execute if the condition parameter evaluates to `false`.

#### [`whenEmpty`](#method-fluent-str-when-empty)
The `whenEmpty` method invokes the given closure if the string is empty. If the closure returns a value, that value will also be returned by the `whenEmpty` method. If the closure does not return a value, the fluent string instance will be returned:

```php	
use Illuminate\Support\Str;
use Illuminate\Support\Stringable;
 
$string=Str::of('')->whenEmpty(function(Stringable$string) {
return$string->trim()->prepend('Laravel');
});
 
// 'Laravel'
```
#### [`whenNotEmpty`](#method-fluent-str-when-not-empty)
The `whenNotEmpty` method invokes the given closure if the string is not empty. If the closure returns a value, that value will also be returned by the `whenNotEmpty` method. If the closure does not return a value, the fluent string instance will be returned:

```php	
use Illuminate\Support\Str;
use Illuminate\Support\Stringable;
 
$string=Str::of('Framework')->whenNotEmpty(function(Stringable$string) {
return$string->prepend('Laravel ');
});
 
// 'Laravel Framework'
```
#### [`whenStartsWith`](#method-fluent-str-when-starts-with)
The `whenStartsWith` method invokes the given closure if the string starts with the given sub-string. The closure will receive the fluent string instance:

```php	
use Illuminate\Support\Str;
use Illuminate\Support\Stringable;
 
$string=Str::of('disney world')->whenStartsWith('disney', function(Stringable$string) {
return$string->title();
});
 
// 'Disney World'
```
#### [`whenEndsWith`](#method-fluent-str-when-ends-with)
The `whenEndsWith` method invokes the given closure if the string ends with the given sub-string. The closure will receive the fluent string instance:

```php	
use Illuminate\Support\Str;
use Illuminate\Support\Stringable;
 
$string=Str::of('disney world')->whenEndsWith('world', function(Stringable$string) {
return$string->title();
});
 
// 'Disney World'
```
#### [`whenExactly`](#method-fluent-str-when-exactly)
The `whenExactly` method invokes the given closure if the string exactly matches the given string. The closure will receive the fluent string instance:

```php	
use Illuminate\Support\Str;
use Illuminate\Support\Stringable;
 
$string=Str::of('laravel')->whenExactly('laravel', function(Stringable$string) {
return$string->title();
});
 
// 'Laravel'
```
#### [`whenNotExactly`](#method-fluent-str-when-not-exactly)
The `whenNotExactly` method invokes the given closure if the string does not exactly match the given string. The closure will receive the fluent string instance:

```php	
use Illuminate\Support\Str;
use Illuminate\Support\Stringable;
 
$string=Str::of('framework')->whenNotExactly('laravel', function(Stringable$string) {
return$string->title();
});
 
// 'Framework'
```
#### [`whenIs`](#method-fluent-str-when-is)
The `whenIs` method invokes the given closure if the string matches a given pattern. Asterisks may be used as wildcard values. The closure will receive the fluent string instance:

```php	
use Illuminate\Support\Str;
use Illuminate\Support\Stringable;
 
$string=Str::of('foo/bar')->whenIs('foo/*', function(Stringable$string) {
return$string->append('/baz');
});
 
// 'foo/bar/baz'
```
#### [`whenIsAscii`](#method-fluent-str-when-is-ascii)
The `whenIsAscii` method invokes the given closure if the string is 7 bit ASCII. The closure will receive the fluent string instance:

```php	
use Illuminate\Support\Str;
use Illuminate\Support\Stringable;
 
$string=Str::of('laravel')->whenIsAscii(function(Stringable$string) {
return$string->title();
});
 
// 'Laravel'
```
#### [`whenIsUlid`](#method-fluent-str-when-is-ulid)
The `whenIsUlid` method invokes the given closure if the string is a valid ULID. The closure will receive the fluent string instance:

```php	
use Illuminate\Support\Str;
 
$string=Str::of('01gd6r360bp37zj17nxb55yv40')->whenIsUlid(function(Stringable$string) {
return$string->substr(0, 8);
});
 
// '01gd6r36'
```
#### [`whenIsUuid`](#method-fluent-str-when-is-uuid)
The `whenIsUuid` method invokes the given closure if the string is a valid UUID. The closure will receive the fluent string instance:

```php	
use Illuminate\Support\Str;
use Illuminate\Support\Stringable;
 
$string=Str::of('a0a2a2d2-0b87-4a18-83f2-2529882be2de')->whenIsUuid(function(Stringable$string) {
return$string->substr(0, 8);
});
 
// 'a0a2a2d2'
```
#### [`whenTest`](#method-fluent-str-when-test)
The `whenTest` method invokes the given closure if the string matches the given regular expression. The closure will receive the fluent string instance:

```php	
use Illuminate\Support\Str;
use Illuminate\Support\Stringable;
 
$string=Str::of('laravel framework')->whenTest('/laravel/', function(Stringable$string) {
return$string->title();
});
 
// 'Laravel Framework'
```
#### [`wordCount`](#method-fluent-str-word-count)
The `wordCount` method returns the number of words that a string contains:

```php	
use Illuminate\Support\Str;
 
Str::of('Hello, world!')->wordCount(); // 2
```
#### [`words`](#method-fluent-str-words)
The `words` method limits the number of words in a string. If necessary, you may specify an additional string that will be appended to the truncated string:

```php	
use Illuminate\Support\Str;
 
$string=Str::of('Perfectly balanced, as all things should be.')->words(3, ' >>>');
 
// Perfectly balanced, as >>>
```
## [URLs](#urls)
#### [`action()`](#method-action)
The `action` function generates a URL for the given controller action:

```php	
use App\Http\Controllers\HomeController;
 
$url=action([HomeController::class,'index']);
```
If the method accepts route parameters, you may pass them as the second argument to the method:

```php	
$url=action([UserController::class,'profile'],['id'=>1]);
```
#### [`asset()`](#method-asset)
The `asset` function generates a URL for an asset using the current scheme of the request (HTTP or HTTPS):

```php	
$url=asset('img/photo.jpg');
```
You can configure the asset URL host by setting the `ASSET_URL` variable in your `.env` file. This can be useful if you host your assets on an external service like Amazon S3 or another CDN:

```php	
// ASSET_URL=http://example.com/assets
 
$url=asset('img/photo.jpg'); // http://example.com/assets/img/photo.jpg
```
#### [`route()`](#method-route)
The `route` function generates a URL for a given [named route](/docs/master/routing#named-routes):

```php	
$url=route('route.name');
```
If the route accepts parameters, you may pass them as the second argument to the function:

```php	
$url=route('route.name',['id'=>1]);
```
By default, the `route` function generates an absolute URL. If you wish to generate a relative URL, you may pass `false` as the third argument to the function:

```php	
$url=route('route.name',['id'=>1], false);
```
#### [`secure_asset()`](#method-secure-asset)
The `secure_asset` function generates a URL for an asset using HTTPS:

```php	
$url=secure_asset('img/photo.jpg');
```
#### [`secure_url()`](#method-secure-url)
The `secure_url` function generates a fully qualified HTTPS URL to the given path. Additional URL segments may be passed in the function's second argument:

```php	
$url=secure_url('user/profile');
 
$url=secure_url('user/profile',[1]);
```
#### [`to_route()`](#method-to-route)
The `to_route` function generates a [redirect HTTP response](/docs/master/responses#redirects) for a given [named route](/docs/master/routing#named-routes):

```php	
returnto_route('users.show',['user'=>1]);
```
If necessary, you may pass the HTTP status code that should be assigned to the redirect and any additional response headers as the third and fourth arguments to the `to_route` method:

```php	
returnto_route('users.show',['user'=>1],302,['X-Framework'=>'Laravel']);
```
#### [`url()`](#method-url)
The `url` function generates a fully qualified URL to the given path:

```php	
$url=url('user/profile');
 
$url=url('user/profile',[1]);
```
If no path is provided, an `Illuminate\Routing\UrlGenerator` instance is returned:

```php	
$current=url()->current();
 
$full=url()->full();
 
$previous=url()->previous();
```
## [Miscellaneous](#miscellaneous)
#### [`abort()`](#method-abort)
The `abort` function throws [an HTTP exception](/docs/master/errors#http-exceptions) which will be rendered by the [exception handler](/docs/master/errors#the-exception-handler):

```php	
abort(403);
```
You may also provide the exception's message and custom HTTP response headers that should be sent to the browser:

```php	
abort(403,'Unauthorized.',$headers);
```
#### [`abort_if()`](#method-abort-if)
The `abort_if` function throws an HTTP exception if a given boolean expression evaluates to `true`:

```php	
abort_if(!Auth::user()->isAdmin(),403);
```
Like the `abort` method, you may also provide the exception's response text as the third argument and an array of custom response headers as the fourth argument to the function.

#### [`abort_unless()`](#method-abort-unless)
The `abort_unless` function throws an HTTP exception if a given boolean expression evaluates to `false`:

```php	
abort_unless(Auth::user()->isAdmin(),403);
```
Like the `abort` method, you may also provide the exception's response text as the third argument and an array of custom response headers as the fourth argument to the function.

#### [`app()`](#method-app)
The `app` function returns the [service container](/docs/master/container) instance:

```php	
$container=app();
```
You may pass a class or interface name to resolve it from the container:

```php	
$api=app('HelpSpot\API');
```
#### [`auth()`](#method-auth)
The `auth` function returns an [authenticator](/docs/master/authentication) instance. You may use it as an alternative to the `Auth` facade:

```php	
$user=auth()->user();
```
If needed, you may specify which guard instance you would like to access:

```php	
$user=auth('admin')->user();
```
#### [`back()`](#method-back)
The `back` function generates a [redirect HTTP response](/docs/master/responses#redirects) to the user's previous location:

```php	
returnback($status=302,$headers=[],$fallback='/');
 
returnback();
```
#### [`bcrypt()`](#method-bcrypt)
The `bcrypt` function [hashes](/docs/master/hashing) the given value using Bcrypt. You may use this function as an alternative to the `Hash` facade:

```php	
$password=bcrypt('my-secret-password');
```
#### [`blank()`](#method-blank)
The `blank` function determines whether the given value is "blank":

```php	
blank('');
blank('');
blank(null);
blank(collect());
 
// true
 
blank(0);
blank(true);
blank(false);
 
// false
```
For the inverse of `blank`, see the [`filled`](#method-filled) method.

#### [`broadcast()`](#method-broadcast)
The `broadcast` function [broadcasts](/docs/master/broadcasting) the given [event](/docs/master/events) to its listeners:

```php	
broadcast(newUserRegistered($user));
 
broadcast(newUserRegistered($user))->toOthers();
```
#### [`cache()`](#method-cache)
The `cache` function may be used to get values from the [cache](/docs/master/cache). If the given key does not exist in the cache, an optional default value will be returned:

```php	
$value=cache('key');
 
$value=cache('key','default');
```
You may add items to the cache by passing an array of key / value pairs to the function. You should also pass the number of seconds or duration the cached value should be considered valid:

```php	
cache(['key'=>'value'],300);
 
cache(['key'=>'value'], now()->addSeconds(10));
```
#### [`class_uses_recursive()`](#method-class-uses-recursive)
The `class_uses_recursive` function returns all traits used by a class, including traits used by all of its parent classes:

```php	
$traits=class_uses_recursive(App\Models\User::class);
```
#### [`collect()`](#method-collect)
The `collect` function creates a [collection](/docs/master/collections) instance from the given value:

```php	
$collection=collect(['taylor','abigail']);
```
#### [`config()`](#method-config)
The `config` function gets the value of a [configuration](/docs/master/configuration) variable. The configuration values may be accessed using "dot" syntax, which includes the name of the file and the option you wish to access. A default value may be specified and is returned if the configuration option does not exist:

```php	
$value=config('app.timezone');
 
$value=config('app.timezone',$default);
```
You may set configuration variables at runtime by passing an array of key / value pairs. However, note that this function only affects the configuration value for the current request and does not update your actual configuration values:

```php	
config(['app.debug'=> true]);
```
#### [`cookie()`](#method-cookie)
The `cookie` function creates a new [cookie](/docs/master/requests#cookies) instance:

```php	
$cookie=cookie('name','value',$minutes);
```
#### [`csrf_field()`](#method-csrf-field)
The `csrf_field` function generates an HTML `hidden` input field containing the value of the CSRF token. For example, using [Blade syntax](/docs/master/blade):

```php	
{{ csrf_field() }}
```
#### [`csrf_token()`](#method-csrf-token)
The `csrf_token` function retrieves the value of the current CSRF token:

```php	
$token=csrf_token();
```
#### [`decrypt()`](#method-decrypt)
The `decrypt` function [decrypts](/docs/master/encryption) the given value. You may use this function as an alternative to the `Crypt` facade:

```php	
$password=decrypt($value);
```
#### [`dd()`](#method-dd)
The `dd` function dumps the given variables and ends execution of the script:

```php	
dd($value);
 
dd($value1,$value2,$value3,...);
```
If you do not want to halt the execution of your script, use the [`dump`](#method-dump) function instead.

#### [`dispatch()`](#method-dispatch)
The `dispatch` function pushes the given [job](/docs/master/queues#creating-jobs) onto the Laravel [job queue](/docs/master/queues):

```php	
dispatch(new App\Jobs\SendEmails);
```
#### [`dump()`](#method-dump)
The `dump` function dumps the given variables:

```php	
dump($value);
 
dump($value1,$value2,$value3,...);
```
If you want to stop executing the script after dumping the variables, use the [`dd`](#method-dd) function instead.

#### [`encrypt()`](#method-encrypt)
The `encrypt` function [encrypts](/docs/master/encryption) the given value. You may use this function as an alternative to the `Crypt` facade:

```php	
$secret=encrypt('my-secret-value');
```
#### [`env()`](#method-env)
The `env` function retrieves the value of an [environment variable](/docs/master/configuration#environment-configuration) or returns a default value:

```php	
$env=env('APP_ENV');
 
$env=env('APP_ENV','production');
```
> **Warning**  
>  If you execute the `config:cache` command during your deployment process, you should be sure that you are only calling the `env` function from within your configuration files. Once the configuration has been cached, the `.env` file will not be loaded and all calls to the `env` function will return `null`.

#### [`event()`](#method-event)
The `event` function dispatches the given [event](/docs/master/events) to its listeners:

```php	
event(newUserRegistered($user));
```
#### [`fake()`](#method-fake)
The `fake` function resolves a [Faker](https://github.com/FakerPHP/Faker) singleton from the container, which can be useful when creating fake data in model factories, database seeding, tests, and prototyping views:

```blade	
@for($i=0; $i<10; $i++)
<dl>
<dt>Name</dt>
<dd>{{fake()->name() }}</dd>
 
<dt>Email</dt>
<dd>{{fake()->unique()->safeEmail() }}</dd>
</dl>
@endfor
```
By default, the `fake` function will utilize the `app.faker_locale` configuration option in your `config/app.php` configuration file; however, you may also specify the locale by passing it to the `fake` function. Each locale will resolve an individual singleton:

```php	
fake('nl_NL')->name()
```
#### [`filled()`](#method-filled)
The `filled` function determines whether the given value is not "blank":

```php	
filled(0);
filled(true);
filled(false);
 
// true
 
filled('');
filled('');
filled(null);
filled(collect());
 
// false
```
For the inverse of `filled`, see the [`blank`](#method-blank) method.

#### [`info()`](#method-info)
The `info` function will write information to your application's [log](/docs/master/logging):

```php	
info('Some helpful information!');
```
An array of contextual data may also be passed to the function:

```php	
info('User login attempt failed.',['id'=>$user->id]);
```
#### [`logger()`](#method-logger)
The `logger` function can be used to write a `debug` level message to the [log](/docs/master/logging):

```php	
logger('Debug message');
```
An array of contextual data may also be passed to the function:

```php	
logger('User has logged in.',['id'=>$user->id]);
```
A [logger](/docs/master/errors#logging) instance will be returned if no value is passed to the function:

```php	
logger()->error('You are not allowed here.');
```
#### [`method_field()`](#method-method-field)
The `method_field` function generates an HTML `hidden` input field containing the spoofed value of the form's HTTP verb. For example, using [Blade syntax](/docs/master/blade):

```php	
<formmethod="POST">
 {{ method_field('DELETE') }}
</form>
```
#### [`now()`](#method-now)
The `now` function creates a new `Illuminate\Support\Carbon` instance for the current time:

```php	
$now=now();
```
#### [`old()`](#method-old)
The `old` function [retrieves](/docs/master/requests#retrieving-input) an [old input](/docs/master/requests#old-input) value flashed into the session:

```php	
$value=old('value');
 
$value=old('value','default');
```
Since the "default value" provided as the second argument to the `old` function is often an attribute of an Eloquent model, Laravel allows you to simply pass the entire Eloquent model as the second argument to the `old` function. When doing so, Laravel will assume the first argument provided to the `old` function is the name of the Eloquent attribute that should be considered the "default value":

```php	
{{ old('name',$user->name) }}
 
// Is equivalent to...
 
{{ old('name',$user) }}
```
#### [`optional()`](#method-optional)
The `optional` function accepts any argument and allows you to access properties or call methods on that object. If the given object is `null`, properties and methods will return `null` instead of causing an error:

```php	
returnoptional($user->address)->street;
 
{!!old('name', optional($user)->name) !!}
```
The `optional` function also accepts a closure as its second argument. The closure will be invoked if the value provided as the first argument is not null:

```php	
returnoptional(User::find($id),function(User$user){
return$user->name;
});
```
#### [`policy()`](#method-policy)
The `policy` method retrieves a [policy](/docs/master/authorization#creating-policies) instance for a given class:

```php	
$policy=policy(App\Models\User::class);
```
#### [`redirect()`](#method-redirect)
The `redirect` function returns a [redirect HTTP response](/docs/master/responses#redirects), or returns the redirector instance if called with no arguments:

```php	
returnredirect($to= null,$status=302,$headers=[],$https= null);
 
returnredirect('/home');
 
returnredirect()->route('route.name');
```
#### [`report()`](#method-report)
The `report` function will report an exception using your [exception handler](/docs/master/errors#the-exception-handler):

```php	
report($e);
```
The `report` function also accepts a string as an argument. When a string is given to the function, the function will create an exception with the given string as its message:

```php	
report('Something went wrong.');
```
#### [`report_if()`](#method-report-if)
The `report_if` function will report an exception using your [exception handler](/docs/master/errors#the-exception-handler) if the given condition is `true`:

```php	
report_if($shouldReport,$e);
 
report_if($shouldReport,'Something went wrong.');
```
#### [`report_unless()`](#method-report-unless)
The `report_unless` function will report an exception using your [exception handler](/docs/master/errors#the-exception-handler) if the given condition is `false`:

```php	
report_unless($reportingDisabled,$e);
 
report_unless($reportingDisabled,'Something went wrong.');
```
#### [`request()`](#method-request)
The `request` function returns the current [request](/docs/master/requests) instance or obtains an input field's value from the current request:

```php	
$request=request();
 
$value=request('key',$default);
```
#### [`rescue()`](#method-rescue)
The `rescue` function executes the given closure and catches any exceptions that occur during its execution. All exceptions that are caught will be sent to your [exception handler](/docs/master/errors#the-exception-handler); however, the request will continue processing:

```php	
returnrescue(function(){
return$this->method();
});
```
You may also pass a second argument to the `rescue` function. This argument will be the "default" value that should be returned if an exception occurs while executing the closure:

```php	
returnrescue(function(){
return$this->method();
}, false);
 
returnrescue(function(){
return$this->method();
},function(){
return$this->failure();
});
```
#### [`resolve()`](#method-resolve)
The `resolve` function resolves a given class or interface name to an instance using the [service container](/docs/master/container):

```php	
$api=resolve('HelpSpot\API');
```
#### [`response()`](#method-response)
The `response` function creates a [response](/docs/master/responses) instance or obtains an instance of the response factory:

```php	
returnresponse('Hello World',200,$headers);
 
returnresponse()->json(['foo'=>'bar'], 200, $headers);
```
#### [`retry()`](#method-retry)
The `retry` function attempts to execute the given callback until the given maximum attempt threshold is met. If the callback does not throw an exception, its return value will be returned. If the callback throws an exception, it will automatically be retried. If the maximum attempt count is exceeded, the exception will be thrown:

```php	
returnretry(5,function(){
 // Attempt 5 times while resting 100ms between attempts...
},100);
```
If you would like to manually calculate the number of milliseconds to sleep between attempts, you may pass a closure as the third argument to the `retry` function:

```php	
useException;
 
returnretry(5,function(){
 // ...
},function(int$attempt,Exception$exception){
return$attempt*100;
});
```
For convenience, you may provide an array as the first argument to the `retry` function. This array will be used to determine how many milliseconds to sleep between subsequent attempts:

```php	
returnretry([100,200],function(){
 // Sleep for 100ms on first retry, 200ms on second retry...
});
```
To only retry under specific conditions, you may pass a closure as the fourth argument to the `retry` function:

```php	
useException;
 
returnretry(5,function(){
 // ...
},100,function(Exception$exception){
return$exceptioninstanceofRetryException;
});
```
#### [`session()`](#method-session)
The `session` function may be used to get or set [session](/docs/master/session) values:

```php	
$value=session('key');
```
You may set values by passing an array of key / value pairs to the function:

```php	
session(['chairs'=>7,'instruments'=>3]);
```
The session store will be returned if no value is passed to the function:

```php	
$value=session()->get('key');
 
session()->put('key', $value);
```
#### [`tap()`](#method-tap)
The `tap` function accepts two arguments: an arbitrary `$value` and a closure. The `$value` will be passed to the closure and then be returned by the `tap` function. The return value of the closure is irrelevant:

```php	
$user=tap(User::first(),function(User$user){
$user->name='taylor';
 
$user->save();
});
```
If no closure is passed to the `tap` function, you may call any method on the given `$value`. The return value of the method you call will always be `$value`, regardless of what the method actually returns in its definition. For example, the Eloquent `update` method typically returns an integer. However, we can force the method to return the model itself by chaining the `update` method call through the `tap` function:

```php	
$user=tap($user)->update([
'name'=>$name,
'email'=>$email,
]);
```
To add a `tap` method to a class, you may add the `Illuminate\Support\Traits\Tappable` trait to the class. The `tap` method of this trait accepts a Closure as its only argument. The object instance itself will be passed to the Closure and then be returned by the `tap` method:

```php	
return$user->tap(function(User$user) {
// ...
});
```
#### [`throw_if()`](#method-throw-if)
The `throw_if` function throws the given exception if a given boolean expression evaluates to `true`:

```php	
throw_if(!Auth::user()->isAdmin(),AuthorizationException::class);
 
throw_if(
!Auth::user()->isAdmin(),
AuthorizationException::class,
'You are not allowed to access this page.'
);
```
#### [`throw_unless()`](#method-throw-unless)
The `throw_unless` function throws the given exception if a given boolean expression evaluates to `false`:

```php	
throw_unless(Auth::user()->isAdmin(),AuthorizationException::class);
 
throw_unless(
Auth::user()->isAdmin(),
AuthorizationException::class,
'You are not allowed to access this page.'
);
```
#### [`today()`](#method-today)
The `today` function creates a new `Illuminate\Support\Carbon` instance for the current date:

```php	
$today=today();
```
#### [`trait_uses_recursive()`](#method-trait-uses-recursive)
The `trait_uses_recursive` function returns all traits used by a trait:

```php	
$traits=trait_uses_recursive(\Illuminate\Notifications\Notifiable::class);
```
#### [`transform()`](#method-transform)
The `transform` function executes a closure on a given value if the value is not [blank](#method-blank) and then returns the return value of the closure:

```php	
$callback=function(int$value) {
return$value*2;
};
 
$result=transform(5,$callback);
 
// 10
```
A default value or closure may be passed as the third argument to the function. This value will be returned if the given value is blank:

```php	
$result=transform(null,$callback,'The value is blank');
 
// The value is blank
```
#### [`validator()`](#method-validator)
The `validator` function creates a new [validator](/docs/master/validation) instance with the given arguments. You may use it as an alternative to the `Validator` facade:

```php	
$validator=validator($data,$rules,$messages);
```
#### [`value()`](#method-value)
The `value` function returns the value it is given. However, if you pass a closure to the function, the closure will be executed and its returned value will be returned:

```php	
$result=value(true);
 
// true
 
$result=value(function(){
return false;
});
 
// false
```
Additional arguments may be passed to the `value` function. If the first argument is a closure then the additional parameters will be passed to the closure as arguments, otherwise they will be ignored:

```php	
$result=value(function(string$name){
return$name;
},'Taylor');
 
// 'Taylor'
```
#### [`view()`](#method-view)
The `view` function retrieves a [view](/docs/master/views) instance:

```php	
returnview('auth.login');
```
#### [`with()`](#method-with)
The `with` function returns the value it is given. If a closure is passed as the second argument to the function, the closure will be executed and its returned value will be returned:

```php	
$callback=function(mixed$value) {
returnis_numeric($value) ?$value*2:0;
};
 
$result=with(5,$callback);
 
// 10
 
$result=with(null,$callback);
 
// 0
 
$result=with(5, null);
 
// 5
```
## [Other Utilities](#other-utilities)
### [Benchmarking](#benchmarking)
Sometimes you may wish to quickly test the performance of certain parts of your application. On those occasions, you may utilize the `Benchmark` support class to measure the number of milliseconds it takes for the given callbacks to complete:

```php	
<?php
 
use App\Models\User;
use Illuminate\Support\Benchmark;
 
Benchmark::dd(fn() => User::find(1)); // 0.1 ms
 
Benchmark::dd([
'Scenario 1'=>fn() => User::count(), // 0.5 ms
'Scenario 2'=>fn() => User::all()->count(), // 20.0 ms
]);
```
By default, the given callbacks will be executed once (one iteration), and their duration will be displayed in the browser / console.

To invoke a callback more than once, you may specify the number of iterations that the callback should be invoked as the second argument to the method. When executing a callback more than once, the `Benchmark` class will return the average amount of milliseconds it took to execute the callback across all iterations:

```php	
Benchmark::dd(fn() => User::count(), iterations: 10); // 0.5 ms
```
### [Lottery](#lottery)
Laravel's lottery class may be used to execute callbacks based on a set of given odds. This can be particularly useful when you only want to execute code for a percentage of your incoming requests:

```php	
use Illuminate\Support\Lottery;
 
Lottery::odds(1, 20)
->winner(fn() => $user->won())
->loser(fn() => $user->lost())
->choose();
```
You may combine Laravel's lottery class with other Laravel features. For example, you may wish to only report a small percentage of slow queries to your exception handler. And, since the lottery class is callable, we may pass an instance of the class into any method that accepts callables:

```php	
use Carbon\CarbonInterval;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Lottery;
 
DB::whenQueryingForLongerThan(
CarbonInterval::seconds(2),
Lottery::odds(1, 100)->winner(fn() => report('Querying > 2 seconds.')),
);
```
#### [Testing Lotteries](#testing-lotteries)
Laravel provides some simple methods to allow you to easily test your application's lottery invocations:

```php	
// Lottery will always win...
Lottery::alwaysWin();
 
// Lottery will always lose...
Lottery::alwaysLose();
 
// Lottery will win then lose, and finally return to normal behavior...
Lottery::fix([true, false]);
 
// Lottery will return to normal behavior...
Lottery::determineResultsNormally();
```
