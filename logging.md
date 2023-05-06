# Logging
- [Introduction](#introduction)
- [Configuration](#configuration)
    - [Available Channel Drivers](#available-channel-drivers)

    - [Channel Prerequisites](#channel-prerequisites)

    - [Logging Deprecation Warnings](#logging-deprecation-warnings)


- [Building Log Stacks](#building-log-stacks)
- [Writing Log Messages](#writing-log-messages)
    - [Contextual Information](#contextual-information)

    - [Writing To Specific Channels](#writing-to-specific-channels)


- [Monolog Channel Customization](#monolog-channel-customization)
    - [Customizing Monolog For Channels](#customizing-monolog-for-channels)

    - [Creating Monolog Handler Channels](#creating-monolog-handler-channels)

    - [Creating Custom Channels Via Factories](#creating-custom-channels-via-factories)



## [Introduction](#introduction)
To help you learn more about what's happening within your application, Laravel provides robust logging services that allow you to log messages to files, the system error log, and even to Slack to notify your entire team.

Laravel logging is based on "channels". Each channel represents a specific way of writing log information. For example, the `single` channel writes log files to a single log file, while the `slack` channel sends log messages to Slack. Log messages may be written to multiple channels based on their severity.

Under the hood, Laravel utilizes the [Monolog](https://github.com/Seldaek/monolog) library, which provides support for a variety of powerful log handlers. Laravel makes it a cinch to configure these handlers, allowing you to mix and match them to customize your application's log handling.

## [Configuration](#configuration)
All of the configuration options for your application's logging behavior is housed in the `config/logging.php` configuration file. This file allows you to configure your application's log channels, so be sure to review each of the available channels and their options. We'll review a few common options below.

By default, Laravel will use the `stack` channel when logging messages. The `stack` channel is used to aggregate multiple log channels into a single channel. For more information on building stacks, check out the [documentation below](#building-log-stacks).

#### [Configuring The Channel Name](#configuring-the-channel-name)
By default, Monolog is instantiated with a "channel name" that matches the current environment, such as `production` or `local`. To change this value, add a `name` option to your channel's configuration:

```php	
'stack'=> [
'driver'=>'stack',
'name'=>'channel-name',
'channels'=> ['single', 'slack'],
],
```
### [Available Channel Drivers](#available-channel-drivers)
Each log channel is powered by a "driver". The driver determines how and where the log message is actually recorded. The following log channel drivers are available in every Laravel application. An entry for most of these drivers is already present in your application's `config/logging.php` configuration file, so be sure to review this file to become familiar with its contents:

  
| Name | Description |   
| ---- | ---- |   
| `custom` | A driver that calls a specified factory to create a channel |   
| `daily` | A `RotatingFileHandler` based Monolog driver which rotates daily |   
| `errorlog` | An `ErrorLogHandler` based Monolog driver |   
| `monolog` | A Monolog factory driver that may use any supported Monolog handler |   
| `null` | A driver that discards all log messages |   
| `papertrail` | A `SyslogUdpHandler` based Monolog driver |   
| `single` | A single file or path based logger channel (`StreamHandler`) |   
| `slack` | A `SlackWebhookHandler` based Monolog driver |   
| `stack` | A wrapper to facilitate creating "multi-channel" channels |   
| `syslog` | A `SyslogHandler` based Monolog driver | > **Note**  
>  Check out the documentation on [advanced channel customization](#monolog-channel-customization) to learn more about the `monolog` and `custom` drivers.

### [Channel Prerequisites](#channel-prerequisites)
#### [Configuring The Single and Daily Channels](#configuring-the-single-and-daily-channels)
The `single` and `daily` channels have three optional configuration options: `bubble`, `permission`, and `locking`.

NameDescriptionDefault  
`bubble` Indicates if messages should bubble up to other channels after being handled `true`  
`locking` Attempt to lock the log file before writing to it `false`  
`permission` The log file's permissions `0644`  
Additionally, the retention policy for the `daily` channel can be configured via the `days` option:

NameDescriptionDefault  
`days` The number of days that daily log files should be retained `7`  
#### [Configuring The Papertrail Channel](#configuring-the-papertrail-channel)
The `papertrail` channel requires the `host` and `port` configuration options. You can obtain these values from [Papertrail](https://help.papertrailapp.com/kb/configuration/configuring-centralized-logging-from-php-apps/#send-events-from-php-app).

#### [Configuring The Slack Channel](#configuring-the-slack-channel)
The `slack` channel requires a `url` configuration option. This URL should match a URL for an [incoming webhook](https://slack.com/apps/A0F7XDUAZ-incoming-webhooks) that you have configured for your Slack team.

By default, Slack will only receive logs at the `critical` level and above; however, you can adjust this in your `config/logging.php` configuration file by modifying the `level` configuration option within your Slack log channel's configuration array.

### [Logging Deprecation Warnings](#logging-deprecation-warnings)
PHP, Laravel, and other libraries often notify their users that some of their features have been deprecated and will be removed in a future version. If you would like to log these deprecation warnings, you may specify your preferred `deprecations` log channel in your application's `config/logging.php` configuration file:

```php	
'deprecations'=>env('LOG_DEPRECATIONS_CHANNEL','null'),
 
'channels'=> [
...
]
```
Or, you may define a log channel named `deprecations`. If a log channel with this name exists, it will always be used to log deprecations:

```php	
'channels'=> [
'deprecations'=> [
'driver'=>'single',
'path'=>storage_path('logs/php-deprecation-warnings.log'),
 ],
],
```
## [Building Log Stacks](#building-log-stacks)
As mentioned previously, the `stack` driver allows you to combine multiple channels into a single log channel for convenience. To illustrate how to use log stacks, let's take a look at an example configuration that you might see in a production application:

```php	
'channels'=> [
'stack'=> [
'driver'=>'stack',
'channels'=> ['syslog', 'slack'],
 ],
 
'syslog'=> [
'driver'=>'syslog',
'level'=>'debug',
 ],
 
'slack'=> [
'driver'=>'slack',
'url'=>env('LOG_SLACK_WEBHOOK_URL'),
'username'=>'Laravel Log',
'emoji'=>':boom:',
'level'=>'critical',
 ],
],
```
Let's dissect this configuration. First, notice our `stack` channel aggregates two other channels via its `channels` option: `syslog` and `slack`. So, when logging messages, both of these channels will have the opportunity to log the message. However, as we will see below, whether these channels actually log the message may be determined by the message's severity / "level".

#### [Log Levels](#log-levels)
Take note of the `level` configuration option present on the `syslog` and `slack` channel configurations in the example above. This option determines the minimum "level" a message must be in order to be logged by the channel. Monolog, which powers Laravel's logging services, offers all of the log levels defined in the [RFC 5424 specification](https://tools.ietf.org/html/rfc5424). In descending order of severity, these log levels are: **emergency**, **alert**, **critical**, **error**, **warning**, **notice**, **info**, and **debug**.

So, imagine we log a message using the `debug` method:

```php	
Log::debug('An informational message.');
```
Given our configuration, the `syslog` channel will write the message to the system log; however, since the error message is not `critical` or above, it will not be sent to Slack. However, if we log an `emergency` message, it will be sent to both the system log and Slack since the `emergency` level is above our minimum level threshold for both channels:

```php	
Log::emergency('The system is down!');
```
## [Writing Log Messages](#writing-log-messages)
You may write information to the logs using the `Log`[facade](/docs/master/facades). As previously mentioned, the logger provides the eight logging levels defined in the [RFC 5424 specification](https://tools.ietf.org/html/rfc5424): **emergency**, **alert**, **critical**, **error**, **warning**, **notice**, **info** and **debug**:

```php	
use Illuminate\Support\Facades\Log;
 
Log::emergency($message);
Log::alert($message);
Log::critical($message);
Log::error($message);
Log::warning($message);
Log::notice($message);
Log::info($message);
Log::debug($message);
```
You may call any of these methods to log a message for the corresponding level. By default, the message will be written to the default log channel as configured by your `logging` configuration file:

```php	
<?php
 
namespace App\Http\Controllers;
 
use App\Http\Controllers\Controller;
use App\Models\User;
use Illuminate\Support\Facades\Log;
use Illuminate\View\View;
 
classUserControllerextendsController
{
/**
 * Show the profile for the given user.
*/
publicfunctionshow(string$id):View
 {
Log::info('Showing the user profile for user: '.$id);
 
returnview('user.profile',[
'user'=>User::findOrFail($id)
]);
 }
}
```
### [Contextual Information](#contextual-information)
An array of contextual data may be passed to the log methods. This contextual data will be formatted and displayed with the log message:

```php	
use Illuminate\Support\Facades\Log;
 
Log::info('User failed to login.', ['id'=>$user->id]);
```
Occasionally, you may wish to specify some contextual information that should be included with all subsequent log entries in a particular channel. For example, you may wish to log a request ID that is associated with each incoming request to your application. To accomplish this, you may call the `Log` facade's `withContext` method:

```php	
<?php
 
namespace App\Http\Middleware;
 
useClosure;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Log;
use Illuminate\Support\Str;
use Symfony\Component\HttpFoundation\Response;
 
classAssignRequestId
{
/**
 * Handle an incoming request.
 *
 * @param\Closure(\Illuminate\Http\Request): (\Symfony\Component\HttpFoundation\Response) $next
*/
publicfunctionhandle(Request$request, Closure$next):Response
 {
$requestId= (string) Str::uuid();
 
Log::withContext([
'request-id'=>$requestId
 ]);
 
return$next($request)->header('Request-Id', $requestId);
 }
}
```
If you would like to share contextual information across *all* logging channels, you may call the `Log::shareContext()` method. This method will provide the contextual information to all created channels and any channels that are created subsequently. Typically, the `shareContext` method should be called from the `boot` method of an application service provider:

```php	
use Illuminate\Support\Facades\Log;
use Illuminate\Support\Str;
 
classAppServiceProvider
{
/**
 * Bootstrap any application services.
*/
publicfunctionboot():void
 {
Log::shareContext([
'invocation-id'=> (string) Str::uuid(),
 ]);
 }
}
```
### [Writing To Specific Channels](#writing-to-specific-channels)
Sometimes you may wish to log a message to a channel other than your application's default channel. You may use the `channel` method on the `Log` facade to retrieve and log to any channel defined in your configuration file:

```php	
use Illuminate\Support\Facades\Log;
 
Log::channel('slack')->info('Something happened!');
```
If you would like to create an on-demand logging stack consisting of multiple channels, you may use the `stack` method:

```php	
Log::stack(['single', 'slack'])->info('Something happened!');
```
#### [On-Demand Channels](#on-demand-channels)
It is also possible to create an on-demand channel by providing the configuration at runtime without that configuration being present in your application's `logging` configuration file. To accomplish this, you may pass a configuration array to the `Log` facade's `build` method:

```php	
use Illuminate\Support\Facades\Log;
 
Log::build([
'driver'=>'single',
'path'=>storage_path('logs/custom.log'),
])->info('Something happened!');
```
You may also wish to include an on-demand channel in an on-demand logging stack. This can be achieved by including your on-demand channel instance in the array passed to the `stack` method:

```php	
use Illuminate\Support\Facades\Log;
 
$channel=Log::build([
'driver'=>'single',
'path'=>storage_path('logs/custom.log'),
]);
 
Log::stack(['slack', $channel])->info('Something happened!');
```
## [Monolog Channel Customization](#monolog-channel-customization)
### [Customizing Monolog For Channels](#customizing-monolog-for-channels)
Sometimes you may need complete control over how Monolog is configured for an existing channel. For example, you may want to configure a custom Monolog `FormatterInterface` implementation for Laravel's built-in `single` channel.

To get started, define a `tap` array on the channel's configuration. The `tap` array should contain a list of classes that should have an opportunity to customize (or "tap" into) the Monolog instance after it is created. There is no conventional location where these classes should be placed, so you are free to create a directory within your application to contain these classes:

```php	
'single'=> [
'driver'=>'single',
'tap'=> [App\Logging\CustomizeFormatter::class],
'path'=>storage_path('logs/laravel.log'),
'level'=>'debug',
],
```
Once you have configured the `tap` option on your channel, you're ready to define the class that will customize your Monolog instance. This class only needs a single method: `__invoke`, which receives an `Illuminate\Log\Logger` instance. The `Illuminate\Log\Logger` instance proxies all method calls to the underlying Monolog instance:

```php	
<?php
 
namespace App\Logging;
 
use Illuminate\Log\Logger;
use Monolog\Formatter\LineFormatter;
 
classCustomizeFormatter
{
/**
 * Customize the given logger instance.
*/
publicfunction__invoke(Logger$logger):void
 {
foreach ($logger->getHandlers() as$handler) {
$handler->setFormatter(newLineFormatter(
'[%datetime%] %channel%.%level_name%: %message% %context% %extra%'
 ));
 }
 }
}
```
> **Note**  
>  All of your "tap" classes are resolved by the [service container](/docs/master/container), so any constructor dependencies they require will automatically be injected.

### [Creating Monolog Handler Channels](#creating-monolog-handler-channels)
Monolog has a variety of [available handlers](https://github.com/Seldaek/monolog/tree/main/src/Monolog/Handler) and Laravel does not include a built-in channel for each one. In some cases, you may wish to create a custom channel that is merely an instance of a specific Monolog handler that does not have a corresponding Laravel log driver. These channels can be easily created using the `monolog` driver.

When using the `monolog` driver, the `handler` configuration option is used to specify which handler will be instantiated. Optionally, any constructor parameters the handler needs may be specified using the `with` configuration option:

```php	
'logentries'=> [
'driver'=>'monolog',
'handler'=> Monolog\Handler\SyslogUdpHandler::class,
'with'=> [
'host'=>'my.logentries.internal.datahubhost.company.com',
'port'=>'10000',
 ],
],
```
#### [Monolog Formatters](#monolog-formatters)
When using the `monolog` driver, the Monolog `LineFormatter` will be used as the default formatter. However, you may customize the type of formatter passed to the handler using the `formatter` and `formatter_with` configuration options:

```php	
'browser'=> [
'driver'=>'monolog',
'handler'=> Monolog\Handler\BrowserConsoleHandler::class,
'formatter'=> Monolog\Formatter\HtmlFormatter::class,
'formatter_with'=> [
'dateFormat'=>'Y-m-d',
 ],
],
```
If you are using a Monolog handler that is capable of providing its own formatter, you may set the value of the `formatter` configuration option to `default`:

```php	
'newrelic'=> [
'driver'=>'monolog',
'handler'=> Monolog\Handler\NewRelicHandler::class,
'formatter'=>'default',
],
```
### [Creating Custom Channels Via Factories](#creating-custom-channels-via-factories)
If you would like to define an entirely custom channel in which you have full control over Monolog's instantiation and configuration, you may specify a `custom` driver type in your `config/logging.php` configuration file. Your configuration should include a `via` option that contains the name of the factory class which will be invoked to create the Monolog instance:

```php	
'channels'=> [
'example-custom-channel'=> [
'driver'=>'custom',
'via'=> App\Logging\CreateCustomLogger::class,
 ],
],
```
Once you have configured the `custom` driver channel, you're ready to define the class that will create your Monolog instance. This class only needs a single `__invoke` method which should return the Monolog logger instance. The method will receive the channels configuration array as its only argument:

```php	
<?php
 
namespace App\Logging;
 
use Monolog\Logger;
 
classCreateCustomLogger
{
/**
 * Create a custom Monolog instance.
*/
publicfunction__invoke(array$config):Logger
 {
returnnewLogger(/* ... */);
 }
}
```
