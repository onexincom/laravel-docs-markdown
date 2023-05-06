# Laravel Horizon
- [Introduction](#introduction)
- [Installation](#installation)
    - [Configuration](#configuration)

    - [Balancing Strategies](#balancing-strategies)

    - [Dashboard Authorization](#dashboard-authorization)

    - [Silenced Jobs](#silenced-jobs)


- [Upgrading Horizon](#upgrading-horizon)
- [Running Horizon](#running-horizon)
    - [Deploying Horizon](#deploying-horizon)


- [Tags](#tags)
- [Notifications](#notifications)
- [Metrics](#metrics)
- [Deleting Failed Jobs](#deleting-failed-jobs)
- [Clearing Jobs From Queues](#clearing-jobs-from-queues)

## [Introduction](#introduction)
> **Note**  
>  Before digging into Laravel Horizon, you should familiarize yourself with Laravel's base [queue services](/docs/master/queues). Horizon augments Laravel's queue with additional features that may be confusing if you are not already familiar with the basic queue features offered by Laravel.

[Laravel Horizon](https://github.com/laravel/horizon) provides a beautiful dashboard and code-driven configuration for your Laravel powered [Redis queues](/docs/master/queues). Horizon allows you to easily monitor key metrics of your queue system such as job throughput, runtime, and job failures.

When using Horizon, all of your queue worker configuration is stored in a single, simple configuration file. By defining your application's worker configuration in a version controlled file, you may easily scale or modify your application's queue workers when deploying your application.

![](https://laravel.com/img/docs/horizon-example.png)## [Installation](#installation)
> **Warning**  
>  Laravel Horizon requires that you use [Redis](https://redis.io) to power your queue. Therefore, you should ensure that your queue connection is set to `redis` in your application's `config/queue.php` configuration file.

You may install Horizon into your project using the Composer package manager:

```shell	
composer requirelaravel/horizon
```
After installing Horizon, publish its assets using the `horizon:install` Artisan command:

```shell	
php artisanhorizon:install
```
### [Configuration](#configuration)
After publishing Horizon's assets, its primary configuration file will be located at `config/horizon.php`. This configuration file allows you to configure the queue worker options for your application. Each configuration option includes a description of its purpose, so be sure to thoroughly explore this file.

> **Warning**  
>  Horizon uses a Redis connection named `horizon` internally. This Redis connection name is reserved and should not be assigned to another Redis connection in the `database.php` configuration file or as the value of the `use` option in the `horizon.php` configuration file.

#### [Environments](#environments)
After installation, the primary Horizon configuration option that you should familiarize yourself with is the `environments` configuration option. This configuration option is an array of environments that your application runs on and defines the worker process options for each environment. By default, this entry contains a `production` and `local` environment. However, you are free to add more environments as needed:

```php	
'environments'=> [
'production'=> [
'supervisor-1'=> [
'maxProcesses'=>10,
'balanceMaxShift'=>1,
'balanceCooldown'=>3,
 ],
 ],
 
'local'=> [
'supervisor-1'=> [
'maxProcesses'=>3,
 ],
 ],
],
```
When you start Horizon, it will use the worker process configuration options for the environment that your application is running on. Typically, the environment is determined by the value of the `APP_ENV`[environment variable](/docs/master/configuration#determining-the-current-environment). For example, the default `local` Horizon environment is configured to start three worker processes and automatically balance the number of worker processes assigned to each queue. The default `production` environment is configured to start a maximum of 10 worker processes and automatically balance the number of worker processes assigned to each queue.

> **Warning**  
>  You should ensure that the `environments` portion of your `horizon` configuration file contains an entry for each [environment](/docs/master/configuration#environment-configuration) on which you plan to run Horizon.

#### [Supervisors](#supervisors)
As you can see in Horizon's default configuration file, each environment can contain one or more "supervisors". By default, the configuration file defines this supervisor as `supervisor-1`; however, you are free to name your supervisors whatever you want. Each supervisor is essentially responsible for "supervising" a group of worker processes and takes care of balancing worker processes across queues.

You may add additional supervisors to a given environment if you would like to define a new group of worker processes that should run in that environment. You may choose to do this if you would like to define a different balancing strategy or worker process count for a given queue used by your application.

#### [Default Values](#default-values)
Within Horizon's default configuration file, you will notice a `defaults` configuration option. This configuration option specifies the default values for your application's [supervisors](#supervisors). The supervisor's default configuration values will be merged into the supervisor's configuration for each environment, allowing you to avoid unnecessary repetition when defining your supervisors.

### [Balancing Strategies](#balancing-strategies)
Unlike Laravel's default queue system, Horizon allows you to choose from three worker balancing strategies: `simple`, `auto`, and `false`. The `simple` strategy, which is the configuration file's default, splits incoming jobs evenly between worker processes:

```php	
'balance'=>'simple',
```
The `auto` strategy adjusts the number of worker processes per queue based on the current workload of the queue. For example, if your `notifications` queue has 1,000 pending jobs while your `render` queue is empty, Horizon will allocate more workers to your `notifications` queue until the queue is empty.

When using the `auto` strategy, you may define the `minProcesses` and `maxProcesses` configuration options to control the minimum and the maximum number of worker processes Horizon should scale up and down to:

```php	
'environments'=> [
'production'=> [
'supervisor-1'=> [
'connection'=>'redis',
'queue'=> ['default'],
'balance'=>'auto',
'minProcesses'=>1,
'maxProcesses'=>10,
'balanceMaxShift'=>1,
'balanceCooldown'=>3,
'tries'=>3,
 ],
 ],
],
```
The `balanceMaxShift` and `balanceCooldown` configuration values determine how quickly Horizon will scale to meet worker demand. In the example above, a maximum of one new process will be created or destroyed every three seconds. You are free to tweak these values as necessary based on your application's needs.

When the `balance` option is set to `false`, the default Laravel behavior will be used, wherein queues are processed in the order they are listed in your configuration.

### [Dashboard Authorization](#dashboard-authorization)
Horizon exposes a dashboard at the `/horizon` URI. By default, you will only be able to access this dashboard in the `local` environment. However, within your `app/Providers/HorizonServiceProvider.php` file, there is an [authorization gate](/docs/master/authorization#gates) definition. This authorization gate controls access to Horizon in **non-local** environments. You are free to modify this gate as needed to restrict access to your Horizon installation:

```php	
/**
 * Register the Horizon gate.
 *
 * This gate determines who can access Horizon in non-local environments.
*/
protectedfunctiongate():void
{
Gate::define('viewHorizon', function(User$user) {
returnin_array($user->email,[
'[[email protected]](/cdn-cgi/l/email-protection)',
]);
 });
}
```
#### [Alternative Authentication Strategies](#alternative-authentication-strategies)
Remember that Laravel automatically injects the authenticated user into the gate closure. If your application is providing Horizon security via another method, such as IP restrictions, then your Horizon users may not need to "login". Therefore, you will need to change `function (User $user)` closure signature above to `function (User $user = null)` in order to force Laravel to not require authentication.

### [Silenced Jobs](#silenced-jobs)
Sometimes, you may not be interested in viewing certain jobs dispatched by your application or third-party packages. Instead of these jobs taking up space in your "Completed Jobs" list, you can silence them. To get started, add the job's class name to the `silenced` configuration option in your application's `horizon` configuration file:

```php	
'silenced'=> [
 App\Jobs\ProcessPodcast::class,
],
```
Alternatively, the job you wish to silence can implement the `Laravel\Horizon\Contracts\Silenced` interface. If a job implements this interface, it will automatically be silenced, even if it is not present in the `silenced` configuration array:

```php	
use Laravel\Horizon\Contracts\Silenced;
 
classProcessPodcastimplementsShouldQueue, Silenced
{
useDispatchable, InteractsWithQueue, Queueable, SerializesModels;
 
// ...
}
```
## [Upgrading Horizon](#upgrading-horizon)
When upgrading to a new major version of Horizon, it's important that you carefully review [the upgrade guide](https://github.com/laravel/horizon/blob/master/UPGRADE.md). In addition, when upgrading to any new Horizon version, you should re-publish Horizon's assets:

```shell	
php artisanhorizon:publish
```
To keep the assets up-to-date and avoid issues in future updates, you may add the `vendor:publish --tag=laravel-assets` command to the `post-update-cmd` scripts in your application's `composer.json` file:

```json	
{
"scripts": {
"post-update-cmd": [
"@php artisan vendor:publish --tag=laravel-assets --ansi --force"
 ]
 }
}
```
## [Running Horizon](#running-horizon)
Once you have configured your supervisors and workers in your application's `config/horizon.php` configuration file, you may start Horizon using the `horizon` Artisan command. This single command will start all of the configured worker processes for the current environment:

```shell	
php artisanhorizon
```
You may pause the Horizon process and instruct it to continue processing jobs using the `horizon:pause` and `horizon:continue` Artisan commands:

```shell	
php artisanhorizon:pause
 
php artisanhorizon:continue
```
You may also pause and continue specific Horizon [supervisors](#supervisors) using the `horizon:pause-supervisor` and `horizon:continue-supervisor` Artisan commands:

```shell	
php artisanhorizon:pause-supervisorsupervisor-1
 
php artisanhorizon:continue-supervisorsupervisor-1
```
You may check the current status of the Horizon process using the `horizon:status` Artisan command:

```shell	
php artisanhorizon:status
```
You may gracefully terminate the Horizon process using the `horizon:terminate` Artisan command. Any jobs that are currently being processed by will be completed and then Horizon will stop executing:

```shell	
php artisanhorizon:terminate
```
### [Deploying Horizon](#deploying-horizon)
When you're ready to deploy Horizon to your application's actual server, you should configure a process monitor to monitor the `php artisan horizon` command and restart it if it exits unexpectedly. Don't worry, we'll discuss how to install a process monitor below.

During your application's deployment process, you should instruct the Horizon process to terminate so that it will be restarted by your process monitor and receive your code changes:

```shell	
php artisanhorizon:terminate
```
#### [Installing Supervisor](#installing-supervisor)
Supervisor is a process monitor for the Linux operating system and will automatically restart your `horizon` process if it stops executing. To install Supervisor on Ubuntu, you may use the following command. If you are not using Ubuntu, you can likely install Supervisor using your operating system's package manager:

```shell	
sudo apt-getinstallsupervisor
```
> **Note**  
>  If configuring Supervisor yourself sounds overwhelming, consider using [Laravel Forge](https://forge.laravel.com), which will automatically install and configure Supervisor for your Laravel projects.

#### [Supervisor Configuration](#supervisor-configuration)
Supervisor configuration files are typically stored within your server's `/etc/supervisor/conf.d` directory. Within this directory, you may create any number of configuration files that instruct supervisor how your processes should be monitored. For example, let's create a `horizon.conf` file that starts and monitors a `horizon` process:

```ini	
[program:horizon]
process_name=%(program_name)s
command=php /home/forge/example.com/artisan horizon
autostart=true
autorestart=true
user=forge
redirect_stderr=true
stdout_logfile=/home/forge/example.com/horizon.log
stopwaitsecs=3600
```
When defining your Supervisor configuration, you should ensure that the value of `stopwaitsecs` is greater than the number of seconds consumed by your longest running job. Otherwise, Supervisor may kill the job before it is finished processing.

> **Warning**  
>  While the examples above are valid for Ubuntu based servers, the location and file extension expected of Supervisor configuration files may vary between other server operating systems. Please consult your server's documentation for more information.

#### [Starting Supervisor](#starting-supervisor)
Once the configuration file has been created, you may update the Supervisor configuration and start the monitored processes using the following commands:

```shell	
sudo supervisorctlreread
 
sudo supervisorctlupdate
 
sudo supervisorctlstarthorizon
```
> **Note**  
>  For more information on running Supervisor, consult the [Supervisor documentation](http://supervisord.org/index.html).

## [Tags](#tags)
Horizon allows you to assign “tags” to jobs, including mailables, broadcast events, notifications, and queued event listeners. In fact, Horizon will intelligently and automatically tag most jobs depending on the Eloquent models that are attached to the job. For example, take a look at the following job:

```php	
<?php
 
namespace App\Jobs;
 
use App\Models\Video;
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Foundation\Bus\Dispatchable;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;
 
classRenderVideoimplementsShouldQueue
{
useDispatchable, InteractsWithQueue, Queueable, SerializesModels;
 
/**
 * Create a new job instance.
*/
publicfunction__construct(
publicVideo$video,
) {}
 
/**
 * Execute the job.
*/
publicfunctionhandle():void
 {
// ...
 }
}
```
If this job is queued with an `App\Models\Video` instance that has an `id` attribute of `1`, it will automatically receive the tag `App\Models\Video:1`. This is because Horizon will search the job's properties for any Eloquent models. If Eloquent models are found, Horizon will intelligently tag the job using the model's class name and primary key:

```php	
use App\Jobs\RenderVideo;
use App\Models\Video;
 
$video=Video::find(1);
 
RenderVideo::dispatch($video);
```
#### [Manually Tagging Jobs](#manually-tagging-jobs)
If you would like to manually define the tags for one of your queueable objects, you may define a `tags` method on the class:

```php	
classRenderVideoimplementsShouldQueue
{
/**
 * Get the tags that should be assigned to the job.
 *
 * @returnarray<int, string>
*/
publicfunctiontags():array
 {
return ['render', 'video:'.$this->video->id];
 }
}
```
## [Notifications](#notifications)
> **Warning**  
>  When configuring Horizon to send Slack or SMS notifications, you should review the [prerequisites for the relevant notification channel](/docs/master/notifications).

If you would like to be notified when one of your queues has a long wait time, you may use the `Horizon::routeMailNotificationsTo`, `Horizon::routeSlackNotificationsTo`, and `Horizon::routeSmsNotificationsTo` methods. You may call these methods from the `boot` method of your application's `App\Providers\HorizonServiceProvider`:

```php	
/**
 * Bootstrap any application services.
*/
publicfunctionboot():void
{
parent::boot();
 
Horizon::routeSmsNotificationsTo('15556667777');
Horizon::routeMailNotificationsTo('[[email protected]](/cdn-cgi/l/email-protection)');
Horizon::routeSlackNotificationsTo('slack-webhook-url', '#channel');
}
```
#### [Configuring Notification Wait Time Thresholds](#configuring-notification-wait-time-thresholds)
You may configure how many seconds are considered a "long wait" within your application's `config/horizon.php` configuration file. The `waits` configuration option within this file allows you to control the long wait threshold for each connection / queue combination:

```php	
'waits'=> [
'redis:default'=>60,
'redis:critical,high'=>90,
],
```
## [Metrics](#metrics)
Horizon includes a metrics dashboard which provides information regarding your job and queue wait times and throughput. In order to populate this dashboard, you should configure Horizon's `snapshot` Artisan command to run every five minutes via your application's [scheduler](/docs/master/scheduling):

```php	
/**
 * Define the application's command schedule.
*/
protectedfunctionschedule(Schedule$schedule):void
{
$schedule->command('horizon:snapshot')->everyFiveMinutes();
}
```
## [Deleting Failed Jobs](#deleting-failed-jobs)
If you would like to delete a failed job, you may use the `horizon:forget` command. The `horizon:forget` command accepts the ID or UUID of the failed job as its only argument:

```shell	
php artisanhorizon:forget5
```
## [Clearing Jobs From Queues](#clearing-jobs-from-queues)
If you would like to delete all jobs from your application's default queue, you may do so using the `horizon:clear` Artisan command:

```shell	
php artisanhorizon:clear
```
You may provide the `queue` option to delete jobs from a specific queue:

```shell	
php artisanhorizon:clear--queue=emails
```
