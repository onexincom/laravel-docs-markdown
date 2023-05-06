# Laravel Valet
- [Introduction](#introduction)
- [Installation](#installation)
    - [Upgrading Valet](#upgrading-valet)


- [Serving Sites](#serving-sites)
    - [The "Park" Command](#the-park-command)

    - [The "Link" Command](#the-link-command)

    - [Securing Sites With TLS](#securing-sites)

    - [Serving a Default Site](#serving-a-default-site)

    - [Per-Site PHP Versions](#per-site-php-versions)


- [Sharing Sites](#sharing-sites)
    - [Sharing Sites Via Ngrok](#sharing-sites-via-ngrok)

    - [Sharing Sites Via Expose](#sharing-sites-via-expose)

    - [Sharing Sites On Your Local Network](#sharing-sites-on-your-local-network)


- [Site Specific Environment Variables](#site-specific-environment-variables)
- [Proxying Services](#proxying-services)
- [Custom Valet Drivers](#custom-valet-drivers)
    - [Local Drivers](#local-drivers)


- [Other Valet Commands](#other-valet-commands)
- [Valet Directories & Files](#valet-directories-and-files)
    - [Disk Access](#disk-access)



## [Introduction](#introduction)
[Laravel Valet](https://github.com/laravel/valet) is a development environment for macOS minimalists. Laravel Valet configures your Mac to always run [Nginx](https://www.nginx.com/) in the background when your machine starts. Then, using [DnsMasq](https://en.wikipedia.org/wiki/Dnsmasq), Valet proxies all requests on the `*.test` domain to point to sites installed on your local machine.

In other words, Valet is a blazing fast Laravel development environment that uses roughly 7 MB of RAM. Valet isn't a complete replacement for [Sail](/docs/master/sail) or [Homestead](/docs/master/homestead), but provides a great alternative if you want flexible basics, prefer extreme speed, or are working on a machine with a limited amount of RAM.

Out of the box, Valet support includes, but is not limited to:

- [Laravel](https://laravel.com)
- [Bedrock](https://roots.io/bedrock/)
- [CakePHP 3](https://cakephp.org)
- [ConcreteCMS](https://www.concretecms.com/)
- [Contao](https://contao.org/en/)
- [Craft](https://craftcms.com)
- [Drupal](https://www.drupal.org/)
- [ExpressionEngine](https://www.expressionengine.com/)
- [Jigsaw](https://jigsaw.tighten.co)
- [Joomla](https://www.joomla.org/)
- [Katana](https://github.com/themsaid/katana)
- [Kirby](https://getkirby.com/)
- [Magento](https://magento.com/)
- [OctoberCMS](https://octobercms.com/)
- [Sculpin](https://sculpin.io/)
- [Slim](https://www.slimframework.com)
- [Statamic](https://statamic.com)
- Static HTML
- [Symfony](https://symfony.com)
- [WordPress](https://wordpress.org)
- [Zend](https://framework.zend.com)

However, you may extend Valet with your own [custom drivers](#custom-valet-drivers).

## [Installation](#installation)
> **Warning**  
>  Valet requires macOS and [Homebrew](https://brew.sh/). Before installation, you should make sure that no other programs such as Apache or Nginx are binding to your local machine's port 80.

To get started, you first need to ensure that Homebrew is up to date using the `update` command:

```shell	
brew update
```
Next, you should use Homebrew to install PHP:

```shell	
brew installphp
```
After installing PHP, you are ready to install the [Composer package manager](https://getcomposer.org). In addition, you should make sure the `~/.composer/vendor/bin` directory is in your system's "PATH". After Composer has been installed, you may install Laravel Valet as a global Composer package:

```shell	
composer globalrequirelaravel/valet
```
Finally, you may execute Valet's `install` command. This will configure and install Valet and DnsMasq. In addition, the daemons Valet depends on will be configured to launch when your system starts:

```shell	
valet install
```
Once Valet is installed, try pinging any `*.test` domain on your terminal using a command such as `ping foobar.test`. If Valet is installed correctly you should see this domain responding on `127.0.0.1`.

Valet will automatically start its required services each time your machine boots.

#### [PHP Versions](#php-versions)
Valet allows you to switch PHP versions using the `valet use [[email protected]](/cdn-cgi/l/email-protection)` command. Valet will install the specified PHP version via Homebrew if it is not already installed:

```shell	
valet use[[email protected]](/cdn-cgi/l/email-protection)
 
valet usephp
```
You may also create a `.valetphprc` file in the root of your project. The `.valetphprc` file should contain the PHP version the site should use:

```shell	
[[email protected]](/cdn-cgi/l/email-protection)
```
Once this file has been created, you may simply execute the `valet use` command and the command will determine the site's preferred PHP version by reading the file.

> **Warning**  
>  Valet only serves one PHP version at a time, even if you have multiple PHP versions installed.

#### [Database](#database)
If your application needs a database, check out [DBngin](https://dbngin.com), which provides a free, all-in-one database management tool that includes MySQL, PostgreSQL, and Redis. After DBngin has been installed, you can connect to your database at `127.0.0.1` using the `root` username and an empty string for the password.

#### [Resetting Your Installation](#resetting-your-installation)
If you are having trouble getting your Valet installation to run properly, executing the `composer global require laravel/valet` command followed by `valet install` will reset your installation and can solve a variety of problems. In rare cases, it may be necessary to "hard reset" Valet by executing `valet uninstall --force` followed by `valet install`.

### [Upgrading Valet](#upgrading-valet)
You may update your Valet installation by executing the `composer global require laravel/valet` command in your terminal. After upgrading, it is good practice to run the `valet install` command so Valet can make additional upgrades to your configuration files if necessary.

## [Serving Sites](#serving-sites)
Once Valet is installed, you're ready to start serving your Laravel applications. Valet provides two commands to help you serve your applications: `park` and `link`.

### [The `park` Command](#the-park-command)
The `park` command registers a directory on your machine that contains your applications. Once the directory has been "parked" with Valet, all of the directories within that directory will be accessible in your web browser at `http://<directory-name>.test`:

```shell	
cd~/Sites
 
valet park
```
That's all there is to it. Now, any application you create within your "parked" directory will automatically be served using the `http://<directory-name>.test` convention. So, if your parked directory contains a directory named "laravel", the application within that directory will be accessible at `http://laravel.test`. In addition, Valet automatically allows you to access the site using wildcard subdomains (`http://foo.laravel.test`).

### [The `link` Command](#the-link-command)
The `link` command can also be used to serve your Laravel applications. This command is useful if you want to serve a single site in a directory and not the entire directory:

```shell	
cd~/Sites/laravel
 
valet link
```
Once an application has been linked to Valet using the `link` command, you may access the application using its directory name. So, the site that was linked in the example above may be accessed at `http://laravel.test`. In addition, Valet automatically allows you to access the site using wildcard sub-domains (`http://foo.laravel.test`).

If you would like to serve the application at a different hostname, you may pass the hostname to the `link` command. For example, you may run the following command to make an application available at `http://application.test`:

```shell	
cd~/Sites/laravel
 
valet linkapplication
```
Of course, you may also serve applications on subdomains using the `link` command:

```shell	
valet linkapi.application
```
You may execute the `links` command to display a list of all of your linked directories:

```shell	
valet links
```
The `unlink` command may be used to destroy the symbolic link for a site:

```shell	
cd~/Sites/laravel
 
valet unlink
```
### [Securing Sites With TLS](#securing-sites)
By default, Valet serves sites over HTTP. However, if you would like to serve a site over encrypted TLS using HTTP/2, you may use the `secure` command. For example, if your site is being served by Valet on the `laravel.test` domain, you should run the following command to secure it:

```shell	
valet securelaravel
```
To "unsecure" a site and revert back to serving its traffic over plain HTTP, use the `unsecure` command. Like the `secure` command, this command accepts the hostname that you wish to unsecure:

```shell	
valet unsecurelaravel
```
### [Serving A Default Site](#serving-a-default-site)
Sometimes, you may wish to configure Valet to serve a "default" site instead of a `404` when visiting an unknown `test` domain. To accomplish this, you may add a `default` option to your `~/.config/valet/config.json` configuration file containing the path to the site that should serve as your default site:

```php	
"default": "/Users/Sally/Sites/example-site",
```
### [Per-Site PHP Versions](#per-site-php-versions)
By default, Valet uses your global PHP installation to serve your sites. However, if you need to support multiple PHP versions across various sites, you may use the `isolate` command to specify which PHP version a particular site should use. The `isolate` command configures Valet to use the specified PHP version for the site located in your current working directory:

```shell	
cd~/Sites/example-site
 
valet isolate[[email protected]](/cdn-cgi/l/email-protection)
```
If your site name does not match the name of the directory that contains it, you may specify the site name using the `--site` option:

```shell	
valet isolate[[email protected]](/cdn-cgi/l/email-protection)--site="site-name"
```
For convenience, you may use the `valet php`, `composer`, and `which-php` commands to proxy calls to the appropriate PHP CLI or tool based on the site's configured PHP version:

```shell	
valet php
valet composer
valet which-php
```
You may execute the `isolated` command to display a list of all of your isolated sites and their PHP versions:

```shell	
valet isolated
```
To revert a site back to Valet's globally installed PHP version, you may invoke the `unisolate` command from the site's root directory:

```shell	
valet unisolate
```
## [Sharing Sites](#sharing-sites)
Valet even includes a command to share your local sites with the world, providing an easy way to test your site on mobile devices or share it with team members and clients.

### [Sharing Sites Via Ngrok](#sharing-sites-via-ngrok)
To share a site, navigate to the site's directory in your terminal and run Valet's `share` command. A publicly accessible URL will be inserted into your clipboard and is ready to paste directly into your browser or share with your team:

```shell	
cd~/Sites/laravel
 
valet share
```
To stop sharing your site, you may press `Control + C`. Sharing your site using Ngrok requires you to [create an Ngrok account](https://dashboard.ngrok.com/signup) and [setup an authentication token](https://dashboard.ngrok.com/get-started/your-authtoken).

> **Note**  
>  You may pass additional Ngrok parameters to the share command, such as `valet share --region=eu`. For more information, consult the [ngrok documentation](https://ngrok.com/docs).

### [Sharing Sites Via Expose](#sharing-sites-via-expose)
If you have [Expose](https://expose.dev) installed, you can share your site by navigating to the site's directory in your terminal and running the `expose` command. Consult the [Expose documentation](https://expose.dev/docs) for information regarding the additional command-line parameters it supports. After sharing the site, Expose will display the sharable URL that you may use on your other devices or amongst team members:

```shell	
cd~/Sites/laravel
 
expose
```
To stop sharing your site, you may press `Control + C`.

### [Sharing Sites On Your Local Network](#sharing-sites-on-your-local-network)
Valet restricts incoming traffic to the internal `127.0.0.1` interface by default so that your development machine isn't exposed to security risks from the Internet.

If you wish to allow other devices on your local network to access the Valet sites on your machine via your machine's IP address (eg: `192.168.1.10/application.test`), you will need to manually edit the appropriate Nginx configuration file for that site to remove the restriction on the `listen` directive. You should remove the `127.0.0.1:` prefix on the `listen` directive for ports 80 and 443.

If you have not run `valet secure` on the project, you can open up network access for all non-HTTPS sites by editing the `/usr/local/etc/nginx/valet/valet.conf` file. However, if you're serving the project site over HTTPS (you have run `valet secure` for the site) then you should edit the `~/.config/valet/Nginx/app-name.test` file.

Once you have updated your Nginx configuration, run the `valet restart` command to apply the configuration changes.

## [Site Specific Environment Variables](#site-specific-environment-variables)
Some applications using other frameworks may depend on server environment variables but do not provide a way for those variables to be configured within your project. Valet allows you to configure site specific environment variables by adding a `.valet-env.php` file within the root of your project. This file should return an array of site / environment variable pairs which will be added to the global `$_SERVER` array for each site specified in the array:

```php	
<?php
 
return [
// Set $_SERVER['key'] to "value" for the laravel.test site...
'laravel'=> [
'key'=>'value',
 ],
 
// Set $_SERVER['key'] to "value" for all sites...
'*'=> [
'key'=>'value',
 ],
];
```
## [Proxying Services](#proxying-services)
Sometimes you may wish to proxy a Valet domain to another service on your local machine. For example, you may occasionally need to run Valet while also running a separate site in Docker; however, Valet and Docker can't both bind to port 80 at the same time.

To solve this, you may use the `proxy` command to generate a proxy. For example, you may proxy all traffic from `http://elasticsearch.test` to `http://127.0.0.1:9200`:

```shell	
# Proxy over HTTP...
valet proxyelasticsearchhttp://127.0.0.1:9200
 
# Proxy over TLS + HTTP/2...
valet proxyelasticsearchhttp://127.0.0.1:9200--secure
```
You may remove a proxy using the `unproxy` command:

```shell	
valet unproxyelasticsearch
```
You may use the `proxies` command to list all site configurations that are proxied:

```shell	
valet proxies
```
## [Custom Valet Drivers](#custom-valet-drivers)
You can write your own Valet "driver" to serve PHP applications running on a framework or CMS that is not natively supported by Valet. When you install Valet, a `~/.config/valet/Drivers` directory is created which contains a `SampleValetDriver.php` file. This file contains a sample driver implementation to demonstrate how to write a custom driver. Writing a driver only requires you to implement three methods: `serves`, `isStaticFile`, and `frontControllerPath`.

All three methods receive the `$sitePath`, `$siteName`, and `$uri` values as their arguments. The `$sitePath` is the fully qualified path to the site being served on your machine, such as `/Users/Lisa/Sites/my-project`. The `$siteName` is the "host" / "site name" portion of the domain (`my-project`). The `$uri` is the incoming request URI (`/foo/bar`).

Once you have completed your custom Valet driver, place it in the `~/.config/valet/Drivers` directory using the `FrameworkValetDriver.php` naming convention. For example, if you are writing a custom valet driver for WordPress, your filename should be `WordPressValetDriver.php`.

Let's take a look at a sample implementation of each method your custom Valet driver should implement.

#### [The `serves` Method](#the-serves-method)
The `serves` method should return `true` if your driver should handle the incoming request. Otherwise, the method should return `false`. So, within this method, you should attempt to determine if the given `$sitePath` contains a project of the type you are trying to serve.

For example, let's imagine we are writing a `WordPressValetDriver`. Our `serves` method might look something like this:

```php	
/**
 * Determine if the driver serves the request.
 *
 * @paramstring $sitePath
 * @paramstring $siteName
 * @paramstring $uri
 * @returnbool
*/
publicfunctionserves($sitePath, $siteName, $uri)
{
returnis_dir($sitePath.'/wp-admin');
}
```
#### [The `isStaticFile` Method](#the-isstaticfile-method)
The `isStaticFile` should determine if the incoming request is for a file that is "static", such as an image or a stylesheet. If the file is static, the method should return the fully qualified path to the static file on disk. If the incoming request is not for a static file, the method should return `false`:

```php	
/**
 * Determine if the incoming request is for a static file.
 *
 * @paramstring $sitePath
 * @paramstring $siteName
 * @paramstring $uri
 * @returnstring|false
*/
publicfunctionisStaticFile($sitePath, $siteName, $uri)
{
if (file_exists($staticFilePath=$sitePath.'/public/'.$uri)) {
return$staticFilePath;
 }
 
returnfalse;
}
```
> **Warning**  
>  The `isStaticFile` method will only be called if the `serves` method returns `true` for the incoming request and the request URI is not `/`.

#### [The `frontControllerPath` Method](#the-frontcontrollerpath-method)
The `frontControllerPath` method should return the fully qualified path to your application's "front controller", which is typically an "index.php" file or equivalent:

```php	
/**
 * Get the fully resolved path to the application's front controller.
 *
 * @paramstring $sitePath
 * @paramstring $siteName
 * @paramstring $uri
 * @returnstring
*/
publicfunctionfrontControllerPath($sitePath, $siteName, $uri)
{
return$sitePath.'/public/index.php';
}
```
### [Local Drivers](#local-drivers)
If you would like to define a custom Valet driver for a single application, create a `LocalValetDriver.php` file in the application's root directory. Your custom driver may extend the base `ValetDriver` class or extend an existing application specific driver such as the `LaravelValetDriver`:

```php	
use Valet\Drivers\LaravelValetDriver;
 
classLocalValetDriverextendsLaravelValetDriver
{
/**
 * Determine if the driver serves the request.
 *
 * @paramstring $sitePath
 * @paramstring $siteName
 * @paramstring $uri
 * @returnbool
*/
publicfunctionserves($sitePath, $siteName, $uri)
 {
returntrue;
 }
 
/**
 * Get the fully resolved path to the application's front controller.
 *
 * @paramstring $sitePath
 * @paramstring $siteName
 * @paramstring $uri
 * @returnstring
*/
publicfunctionfrontControllerPath($sitePath, $siteName, $uri)
 {
return$sitePath.'/public_html/index.php';
 }
}
```
## [Other Valet Commands](#other-valet-commands)
  
| Command | Description |   
| ---- | ---- |   
| `valet list` | Display a list of all Valet commands. |   
| `valet forget` | Run this command from a "parked" directory to remove it from the parked directory list. |   
| `valet log` | View a list of logs which are written by Valet's services. |   
| `valet paths` | View all of your "parked" paths. |   
| `valet restart` | Restart the Valet daemons. |   
| `valet start` | Start the Valet daemons. |   
| `valet stop` | Stop the Valet daemons. |   
| `valet trust` | Add sudoers files for Brew and Valet to allow Valet commands to be run without prompting for your password. |   
| `valet uninstall` | Uninstall Valet: shows instructions for manual uninstall. Pass the `--force` option to aggressively delete all of Valet's resources. | ## [Valet Directories & Files](#valet-directories-and-files)
You may find the following directory and file information helpful while troubleshooting issues with your Valet environment:

#### `~/.config/valet`
Contains all of Valet's configuration. You may wish to maintain a backup of this directory.

#### `~/.config/valet/dnsmasq.d/`
This directory contains DNSMasq's configuration.

#### `~/.config/valet/Drivers/`
This directory contains Valet's drivers. Drivers determine how a particular framework / CMS is served.

#### `~/.config/valet/Extensions/`
This directory contains custom Valet extensions / commands.

#### `~/.config/valet/Nginx/`
This directory contains all of Valet's Nginx site configurations. These files are rebuilt when running the `install` and `secure` commands.

#### `~/.config/valet/Sites/`
This directory contains all of the symbolic links for your [linked projects](#the-link-command).

#### `~/.config/valet/config.json`
This file is Valet's master configuration file.

#### `~/.config/valet/valet.sock`
This file is the PHP-FPM socket used by Valet's Nginx installation. This will only exist if PHP is running properly.

#### `~/.config/valet/Log/fpm-php.www.log`
This file is the user log for PHP errors.

#### `~/.config/valet/Log/nginx-error.log`
This file is the user log for Nginx errors.

#### `/usr/local/var/log/php-fpm.log`
This file is the system log for PHP-FPM errors.

#### `/usr/local/var/log/nginx`
This directory contains the Nginx access and error logs.

#### `/usr/local/etc/php/X.X/conf.d`
This directory contains the `*.ini` files for various PHP configuration settings.

#### `/usr/local/etc/php/X.X/php-fpm.d/valet-fpm.conf`
This file is the PHP-FPM pool configuration file.

#### `~/.composer/vendor/laravel/valet/cli/stubs/secure.valet.conf`
This file is the default Nginx configuration used for building SSL certificates for your sites.

### [Disk Access](#disk-access)
Since macOS 10.14, [access to some files and directories is restricted by default](https://manuals.info.apple.com/MANUALS/1000/MA1902/en_US/apple-platform-security-guide.pdf). These restrictions include the Desktop, Documents, and Downloads directories. In addition, network volume and removable volume access is restricted. Therefore, Valet recommends your site folders are located outside of these protected locations.

However, if you wish to serve sites from within one of those locations, you will need to give Nginx "Full Disk Access". Otherwise, you may encounter server errors or other unpredictable behavior from Nginx, especially when serving static assets. Typically, macOS will automatically prompt you to grant Nginx full access to these locations. Or, you may do so manually via `System Preferences` > `Security & Privacy` > `Privacy` and selecting `Full Disk Access`. Next, enable any `nginx` entries in the main window pane.

