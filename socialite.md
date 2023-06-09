# Laravel Socialite
- [Introduction](#introduction)
- [Installation](#installation)
- [Upgrading Socialite](#upgrading-socialite)
- [Configuration](#configuration)
- [Authentication](#authentication)
    - [Routing](#routing)

    - [Authentication & Storage](#authentication-and-storage)

    - [Access Scopes](#access-scopes)

    - [Optional Parameters](#optional-parameters)


- [Retrieving User Details](#retrieving-user-details)

## [Introduction](#introduction)
In addition to typical, form based authentication, Laravel also provides a simple, convenient way to authenticate with OAuth providers using [Laravel Socialite](https://github.com/laravel/socialite). Socialite currently supports authentication via Facebook, Twitter, LinkedIn, Google, GitHub, GitLab, and Bitbucket.

> **Note**  
>  Adapters for other platforms are available via the community driven [Socialite Providers](https://socialiteproviders.com/) website.

## [Installation](#installation)
To get started with Socialite, use the Composer package manager to add the package to your project's dependencies:

```shell	
composer requirelaravel/socialite
```
## [Upgrading Socialite](#upgrading-socialite)
When upgrading to a new major version of Socialite, it's important that you carefully review [the upgrade guide](https://github.com/laravel/socialite/blob/master/UPGRADE.md).

## [Configuration](#configuration)
Before using Socialite, you will need to add credentials for the OAuth providers your application utilizes. Typically, these credentials may be retrieved by creating a "developer application" within the dashboard of the service you will be authenticating with.

These credentials should be placed in your application's `config/services.php` configuration file, and should use the key `facebook`, `twitter` (OAuth 1.0), `twitter-oauth-2` (OAuth 2.0), `linkedin`, `google`, `github`, `gitlab`, or `bitbucket`, depending on the providers your application requires:

```php	
'github'=> [
'client_id'=>env('GITHUB_CLIENT_ID'),
'client_secret'=>env('GITHUB_CLIENT_SECRET'),
'redirect'=>'http://example.com/callback-url',
],
```
> **Note**  
>  If the `redirect` option contains a relative path, it will automatically be resolved to a fully qualified URL.

## [Authentication](#authentication)
### [Routing](#routing)
To authenticate users using an OAuth provider, you will need two routes: one for redirecting the user to the OAuth provider, and another for receiving the callback from the provider after authentication. The example routes below demonstrate the implementation of both routes:

```php	
use Laravel\Socialite\Facades\Socialite;
 
Route::get('/auth/redirect', function() {
returnSocialite::driver('github')->redirect();
});
 
Route::get('/auth/callback', function() {
$user=Socialite::driver('github')->user();
 
// $user->token
});
```
The `redirect` method provided by the `Socialite` facade takes care of redirecting the user to the OAuth provider, while the `user` method will examine the incoming request and retrieve the user's information from the provider after they have approved the authentication request.

### [Authentication & Storage](#authentication-and-storage)
Once the user has been retrieved from the OAuth provider, you may determine if the user exists in your application's database and [authenticate the user](/docs/master/authentication#authenticate-a-user-instance). If the user does not exist in your application's database, you will typically create a new record in your database to represent the user:

```php	
use App\Models\User;
use Illuminate\Support\Facades\Auth;
use Laravel\Socialite\Facades\Socialite;
 
Route::get('/auth/callback', function() {
$githubUser=Socialite::driver('github')->user();
 
$user=User::updateOrCreate([
'github_id'=>$githubUser->id,
 ], [
'name'=>$githubUser->name,
'email'=>$githubUser->email,
'github_token'=>$githubUser->token,
'github_refresh_token'=>$githubUser->refreshToken,
 ]);
 
Auth::login($user);
 
returnredirect('/dashboard');
});
```
> **Note**  
>  For more information regarding what user information is available from specific OAuth providers, please consult the documentation on [retrieving user details](#retrieving-user-details).

### [Access Scopes](#access-scopes)
Before redirecting the user, you may use the `scopes` method to specify the "scopes" that should be included in the authentication request. This method will merge all previously specified scopes with the scopes that you specify:

```php	
use Laravel\Socialite\Facades\Socialite;
 
returnSocialite::driver('github')
->scopes(['read:user', 'public_repo'])
->redirect();
```
You can overwrite all existing scopes on the authentication request using the `setScopes` method:

```php	
returnSocialite::driver('github')
->setScopes(['read:user', 'public_repo'])
->redirect();
```
### [Optional Parameters](#optional-parameters)
A number of OAuth providers support other optional parameters on the redirect request. To include any optional parameters in the request, call the `with` method with an associative array:

```php	
use Laravel\Socialite\Facades\Socialite;
 
returnSocialite::driver('google')
->with(['hd'=>'example.com'])
->redirect();
```
> **Warning**  
>  When using the `with` method, be careful not to pass any reserved keywords such as `state` or `response_type`.

## [Retrieving User Details](#retrieving-user-details)
After the user is redirected back to your application's authentication callback route, you may retrieve the user's details using Socialite's `user` method. The user object returned by the `user` method provides a variety of properties and methods you may use to store information about the user in your own database.

Differing properties and methods may be available on this object depending on whether the OAuth provider you are authenticating with supports OAuth 1.0 or OAuth 2.0:

```php	
use Laravel\Socialite\Facades\Socialite;
 
Route::get('/auth/callback', function() {
$user=Socialite::driver('github')->user();
 
// OAuth 2.0 providers...
$token=$user->token;
$refreshToken=$user->refreshToken;
$expiresIn=$user->expiresIn;
 
// OAuth 1.0 providers...
$token=$user->token;
$tokenSecret=$user->tokenSecret;
 
// All providers...
$user->getId();
$user->getNickname();
$user->getName();
$user->getEmail();
$user->getAvatar();
});
```
#### [Retrieving User Details From A Token (OAuth2)](#retrieving-user-details-from-a-token-oauth2)
If you already have a valid access token for a user, you can retrieve their user details using Socialite's `userFromToken` method:

```php	
use Laravel\Socialite\Facades\Socialite;
 
$user=Socialite::driver('github')->userFromToken($token);
```
#### [Retrieving User Details From A Token And Secret (OAuth1)](#retrieving-user-details-from-a-token-and-secret-oauth1)
If you already have a valid token and secret for a user, you can retrieve their user details using Socialite's `userFromTokenAndSecret` method:

```php	
use Laravel\Socialite\Facades\Socialite;
 
$user=Socialite::driver('twitter')->userFromTokenAndSecret($token, $secret);
```
#### [Stateless Authentication](#stateless-authentication)
The `stateless` method may be used to disable session state verification. This is useful when adding social authentication to a stateless API that does not utilize cookie based sessions:

```php	
use Laravel\Socialite\Facades\Socialite;
 
returnSocialite::driver('google')->stateless()->user();
```
> **Warning**  
>  Stateless authentication is not available for the Twitter OAuth 1.0 driver.

