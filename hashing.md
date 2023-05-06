# Hashing
- [Introduction](#introduction)
- [Configuration](#configuration)
- [Basic Usage](#basic-usage)
    - [Hashing Passwords](#hashing-passwords)

    - [Verifying That A Password Matches A Hash](#verifying-that-a-password-matches-a-hash)

    - [Determining If A Password Needs To Be Rehashed](#determining-if-a-password-needs-to-be-rehashed)



## [Introduction](#introduction)
The Laravel `Hash`[facade](/docs/master/facades) provides secure Bcrypt and Argon2 hashing for storing user passwords. If you are using one of the [Laravel application starter kits](/docs/master/starter-kits), Bcrypt will be used for registration and authentication by default.

Bcrypt is a great choice for hashing passwords because its "work factor" is adjustable, which means that the time it takes to generate a hash can be increased as hardware power increases. When hashing passwords, slow is good. The longer an algorithm takes to hash a password, the longer it takes malicious users to generate "rainbow tables" of all possible string hash values that may be used in brute force attacks against applications.

## [Configuration](#configuration)
The default hashing driver for your application is configured in your application's `config/hashing.php` configuration file. There are currently several supported drivers: [Bcrypt](https://en.wikipedia.org/wiki/Bcrypt) and [Argon2](https://en.wikipedia.org/wiki/Argon2) (Argon2i and Argon2id variants).

## [Basic Usage](#basic-usage)
### [Hashing Passwords](#hashing-passwords)
You may hash a password by calling the `make` method on the `Hash` facade:

```php	
<?php
 
namespace App\Http\Controllers;
 
use App\Http\Controllers\Controller;
use Illuminate\Http\Request;
use Illuminate\Http\Response;
use Illuminate\Support\Facades\Hash;
 
classPasswordControllerextendsController
{
/**
 * Update the password for the user.
*/
publicfunctionupdate(Request$request):Response
 {
// Validate the new password length...
 
$request->user()->fill([
'password'=>Hash::make($request->newPassword)
 ])->save();
 
returnresponse()->noContent();
 }
}
```
#### [Adjusting The Bcrypt Work Factor](#adjusting-the-bcrypt-work-factor)
If you are using the Bcrypt algorithm, the `make` method allows you to manage the work factor of the algorithm using the `rounds` option; however, the default work factor managed by Laravel is acceptable for most applications:

```php	
$hashed=Hash::make('password', [
'rounds'=>12,
]);
```
#### [Adjusting The Argon2 Work Factor](#adjusting-the-argon2-work-factor)
If you are using the Argon2 algorithm, the `make` method allows you to manage the work factor of the algorithm using the `memory`, `time`, and `threads` options; however, the default values managed by Laravel are acceptable for most applications:

```php	
$hashed=Hash::make('password', [
'memory'=>1024,
'time'=>2,
'threads'=>2,
]);
```
> **Note**  
>  For more information on these options, please refer to the [official PHP documentation regarding Argon hashing](https://secure.php.net/manual/en/function.password-hash.php).

### [Verifying That A Password Matches A Hash](#verifying-that-a-password-matches-a-hash)
The `check` method provided by the `Hash` facade allows you to verify that a given plain-text string corresponds to a given hash:

```php	
if (Hash::check('plain-text', $hashedPassword)) {
// The passwords match...
}
```
### [Determining If A Password Needs To Be Rehashed](#determining-if-a-password-needs-to-be-rehashed)
The `needsRehash` method provided by the `Hash` facade allows you to determine if the work factor used by the hasher has changed since the password was hashed. Some applications choose to perform this check during the application's authentication process:

```php	
if (Hash::needsRehash($hashed)) {
$hashed=Hash::make('plain-text');
}
```
