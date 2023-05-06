# Notifications
- [Introduction](#introduction)
- [Generating Notifications](#generating-notifications)
- [Sending Notifications](#sending-notifications)
    - [Using The Notifiable Trait](#using-the-notifiable-trait)

    - [Using The Notification Facade](#using-the-notification-facade)

    - [Specifying Delivery Channels](#specifying-delivery-channels)

    - [Queueing Notifications](#queueing-notifications)

    - [On-Demand Notifications](#on-demand-notifications)


- [Mail Notifications](#mail-notifications)
    - [Formatting Mail Messages](#formatting-mail-messages)

    - [Customizing The Sender](#customizing-the-sender)

    - [Customizing The Recipient](#customizing-the-recipient)

    - [Customizing The Subject](#customizing-the-subject)

    - [Customizing The Mailer](#customizing-the-mailer)

    - [Customizing The Templates](#customizing-the-templates)

    - [Attachments](#mail-attachments)

    - [Adding Tags & Metadata](#adding-tags-metadata)

    - [Customizing The Symfony Message](#customizing-the-symfony-message)

    - [Using Mailables](#using-mailables)

    - [Previewing Mail Notifications](#previewing-mail-notifications)


- [Markdown Mail Notifications](#markdown-mail-notifications)
    - [Generating The Message](#generating-the-message)

    - [Writing The Message](#writing-the-message)

    - [Customizing The Components](#customizing-the-components)


- [Database Notifications](#database-notifications)
    - [Prerequisites](#database-prerequisites)

    - [Formatting Database Notifications](#formatting-database-notifications)

    - [Accessing The Notifications](#accessing-the-notifications)

    - [Marking Notifications As Read](#marking-notifications-as-read)


- [Broadcast Notifications](#broadcast-notifications)
    - [Prerequisites](#broadcast-prerequisites)

    - [Formatting Broadcast Notifications](#formatting-broadcast-notifications)

    - [Listening For Notifications](#listening-for-notifications)


- [SMS Notifications](#sms-notifications)
    - [Prerequisites](#sms-prerequisites)

    - [Formatting SMS Notifications](#formatting-sms-notifications)

    - [Formatting Shortcode Notifications](#formatting-shortcode-notifications)

    - [Customizing The "From" Number](#customizing-the-from-number)

    - [Adding A Client Reference](#adding-a-client-reference)

    - [Routing SMS Notifications](#routing-sms-notifications)


- [Slack Notifications](#slack-notifications)
    - [Prerequisites](#slack-prerequisites)

    - [Formatting Slack Notifications](#formatting-slack-notifications)

    - [Slack Attachments](#slack-attachments)

    - [Routing Slack Notifications](#routing-slack-notifications)


- [Localizing Notifications](#localizing-notifications)
- [Testing](#testing)
- [Notification Events](#notification-events)
- [Custom Channels](#custom-channels)

## [Introduction](#introduction)
In addition to support for [sending email](/docs/master/mail), Laravel provides support for sending notifications across a variety of delivery channels, including email, SMS (via [Vonage](https://www.vonage.com/communications-apis/), formerly known as Nexmo), and [Slack](https://slack.com). In addition, a variety of [community built notification channels](https://laravel-notification-channels.com/about/#suggesting-a-new-channel) have been created to send notifications over dozens of different channels! Notifications may also be stored in a database so they may be displayed in your web interface.

Typically, notifications should be short, informational messages that notify users of something that occurred in your application. For example, if you are writing a billing application, you might send an "Invoice Paid" notification to your users via the email and SMS channels.

## [Generating Notifications](#generating-notifications)
In Laravel, each notification is represented by a single class that is typically stored in the `app/Notifications` directory. Don't worry if you don't see this directory in your application - it will be created for you when you run the `make:notification` Artisan command:

```shell	
php artisanmake:notificationInvoicePaid
```
This command will place a fresh notification class in your `app/Notifications` directory. Each notification class contains a `via` method and a variable number of message building methods, such as `toMail` or `toDatabase`, that convert the notification to a message tailored for that particular channel.

## [Sending Notifications](#sending-notifications)
### [Using The Notifiable Trait](#using-the-notifiable-trait)
Notifications may be sent in two ways: using the `notify` method of the `Notifiable` trait or using the `Notification`[facade](/docs/master/facades). The `Notifiable` trait is included on your application's `App\Models\User` model by default:

```php	
<?php
 
namespace App\Models;
 
use Illuminate\Foundation\Auth\Useras Authenticatable;
use Illuminate\Notifications\Notifiable;
 
classUserextendsAuthenticatable
{
useNotifiable;
}
```
The `notify` method that is provided by this trait expects to receive a notification instance:

```php	
use App\Notifications\InvoicePaid;
 
$user->notify(newInvoicePaid($invoice));
```
> **Note**  
>  Remember, you may use the `Notifiable` trait on any of your models. You are not limited to only including it on your `User` model.

### [Using The Notification Facade](#using-the-notification-facade)
Alternatively, you may send notifications via the `Notification`[facade](/docs/master/facades). This approach is useful when you need to send a notification to multiple notifiable entities such as a collection of users. To send notifications using the facade, pass all of the notifiable entities and the notification instance to the `send` method:

```php	
use Illuminate\Support\Facades\Notification;
 
Notification::send($users, newInvoicePaid($invoice));
```
You can also send notifications immediately using the `sendNow` method. This method will send the notification immediately even if the notification implements the `ShouldQueue` interface:

```php	
Notification::sendNow($developers, newDeploymentCompleted($deployment));
```
### [Specifying Delivery Channels](#specifying-delivery-channels)
Every notification class has a `via` method that determines on which channels the notification will be delivered. Notifications may be sent on the `mail`, `database`, `broadcast`, `vonage`, and `slack` channels.

> **Note**  
>  If you would like to use other delivery channels such as Telegram or Pusher, check out the community driven [Laravel Notification Channels website](http://laravel-notification-channels.com).

The `via` method receives a `$notifiable` instance, which will be an instance of the class to which the notification is being sent. You may use `$notifiable` to determine which channels the notification should be delivered on:

```php	
/**
 * Get the notification's delivery channels.
 *
 * @returnarray<int, string>
*/
publicfunctionvia(object$notifiable):array
{
return$notifiable->prefers_sms? ['vonage'] : ['mail', 'database'];
}
```
### [Queueing Notifications](#queueing-notifications)
> **Warning**  
>  Before queueing notifications you should configure your queue and [start a worker](/docs/master/queues).

Sending notifications can take time, especially if the channel needs to make an external API call to deliver the notification. To speed up your application's response time, let your notification be queued by adding the `ShouldQueue` interface and `Queueable` trait to your class. The interface and trait are already imported for all notifications generated using the `make:notification` command, so you may immediately add them to your notification class:

```php	
<?php
 
namespace App\Notifications;
 
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Notifications\Notification;
 
classInvoicePaidextendsNotificationimplementsShouldQueue
{
useQueueable;
 
// ...
}
```
Once the `ShouldQueue` interface has been added to your notification, you may send the notification like normal. Laravel will detect the `ShouldQueue` interface on the class and automatically queue the delivery of the notification:

```php	
$user->notify(newInvoicePaid($invoice));
```
When queueing notifications, a queued job will be created for each recipient and channel combination. For example, six jobs will be dispatched to the queue if your notification has three recipients and two channels.

#### [Delaying Notifications](#delaying-notifications)
If you would like to delay the delivery of the notification, you may chain the `delay` method onto your notification instantiation:

```php	
$delay=now()->addMinutes(10);
 
$user->notify((newInvoicePaid($invoice))->delay($delay));
```
#### [Delaying Notifications Per Channel](#delaying-notifications-per-channel)
You may pass an array to the `delay` method to specify the delay amount for specific channels:

```php	
$user->notify((newInvoicePaid($invoice))->delay([
'mail'=>now()->addMinutes(5),
'sms'=>now()->addMinutes(10),
]));
```
Alternatively, you may define a `withDelay` method on the notification class itself. The `withDelay` method should return an array of channel names and delay values:

```php	
/**
 * Determine the notification's delivery delay.
 *
 * @returnarray<string, \Illuminate\Support\Carbon>
*/
publicfunctionwithDelay(object$notifiable):array
{
return [
'mail'=>now()->addMinutes(5),
'sms'=>now()->addMinutes(10),
 ];
}
```
#### [Customizing The Notification Queue Connection](#customizing-the-notification-queue-connection)
By default, queued notifications will be queued using your application's default queue connection. If you would like to specify a different connection that should be used for a particular notification, you may define a `$connection` property on the notification class:

```php	
/**
 * The name of the queue connection to use when queueing the notification.
 *
 * @varstring
*/
public$connection='redis';
```
Or, if you would like to specify a specific queue connection that should be used for each notification channel supported by the notification, you may define a `viaConnections` method on your notification. This method should return an array of channel name / queue connection name pairs:

```php	
/**
 * Determine which connections should be used for each notification channel.
 *
 * @returnarray<string, string>
*/
publicfunctionviaConnections():array
{
return [
'mail'=>'redis',
'database'=>'sync',
 ];
}
```
#### [Customizing Notification Channel Queues](#customizing-notification-channel-queues)
If you would like to specify a specific queue that should be used for each notification channel supported by the notification, you may define a `viaQueues` method on your notification. This method should return an array of channel name / queue name pairs:

```php	
/**
 * Determine which queues should be used for each notification channel.
 *
 * @returnarray<string, string>
*/
publicfunctionviaQueues():array
{
return [
'mail'=>'mail-queue',
'slack'=>'slack-queue',
 ];
}
```
#### [Queued Notifications & Database Transactions](#queued-notifications-and-database-transactions)
When queued notifications are dispatched within database transactions, they may be processed by the queue before the database transaction has committed. When this happens, any updates you have made to models or database records during the database transaction may not yet be reflected in the database. In addition, any models or database records created within the transaction may not exist in the database. If your notification depends on these models, unexpected errors can occur when the job that sends the queued notification is processed.

If your queue connection's `after_commit` configuration option is set to `false`, you may still indicate that a particular queued notification should be dispatched after all open database transactions have been committed by calling the `afterCommit` method when sending the notification:

```php	
use App\Notifications\InvoicePaid;
 
$user->notify((newInvoicePaid($invoice))->afterCommit());
```
Alternatively, you may call the `afterCommit` method from your notification's constructor:

```php	
<?php
 
namespace App\Notifications;
 
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Notifications\Notification;
 
classInvoicePaidextendsNotificationimplementsShouldQueue
{
useQueueable;
 
/**
 * Create a new notification instance.
*/
publicfunction__construct()
 {
$this->afterCommit();
 }
}
```
> **Note**  
>  To learn more about working around these issues, please review the documentation regarding [queued jobs and database transactions](/docs/master/queues#jobs-and-database-transactions).

#### [Determining If A Queued Notification Should Be Sent](#determining-if-the-queued-notification-should-be-sent)
After a queued notification has been dispatched for the queue for background processing, it will typically be accepted by a queue worker and sent to its intended recipient.

However, if you would like to make the final determination on whether the queued notification should be sent after it is being processed by a queue worker, you may define a `shouldSend` method on the notification class. If this method returns `false`, the notification will not be sent:

```php	
/**
 * Determine if the notification should be sent.
*/
publicfunctionshouldSend(object$notifiable, string$channel):bool
{
return$this->invoice->isPaid();
}
```
### [On-Demand Notifications](#on-demand-notifications)
Sometimes you may need to send a notification to someone who is not stored as a "user" of your application. Using the `Notification` facade's `route` method, you may specify ad-hoc notification routing information before sending the notification:

```php	
use Illuminate\Broadcasting\Channel;
 
Notification::route('mail', '[[email protected]](/cdn-cgi/l/email-protection)')
->route('vonage', '5555555555')
->route('slack', 'https://hooks.slack.com/services/...')
->route('broadcast', [newChannel('channel-name')])
->notify(newInvoicePaid($invoice));
```
If you would like to provide the recipient's name when sending an on-demand notification to the `mail` route, you may provide an array that contains the email address as the key and the name as the value of the first element in the array:

```php	
Notification::route('mail', [
'[[email protected]](/cdn-cgi/l/email-protection)'=>'Barrett Blair',
])->notify(newInvoicePaid($invoice));
```
## [Mail Notifications](#mail-notifications)
### [Formatting Mail Messages](#formatting-mail-messages)
If a notification supports being sent as an email, you should define a `toMail` method on the notification class. This method will receive a `$notifiable` entity and should return an `Illuminate\Notifications\Messages\MailMessage` instance.

The `MailMessage` class contains a few simple methods to help you build transactional email messages. Mail messages may contain lines of text as well as a "call to action". Let's take a look at an example `toMail` method:

```php	
/**
 * Get the mail representation of the notification.
*/
publicfunctiontoMail(object$notifiable):MailMessage
{
$url=url('/invoice/'.$this->invoice->id);
 
return (newMailMessage)
->greeting('Hello!')
->line('One of your invoices has been paid!')
->lineIf($this->amount>0, "Amount paid: {$this->amount}")
->action('View Invoice', $url)
->line('Thank you for using our application!');
}
```
> **Note**  
>  Note we are using `$this->invoice->id` in our `toMail` method. You may pass any data your notification needs to generate its message into the notification's constructor.

In this example, we register a greeting, a line of text, a call to action, and then another line of text. These methods provided by the `MailMessage` object make it simple and fast to format small transactional emails. The mail channel will then translate the message components into a beautiful, responsive HTML email template with a plain-text counterpart. Here is an example of an email generated by the `mail` channel:

![](https://laravel.com/img/docs/notification-example-2.png)> **Note**  
>  When sending mail notifications, be sure to set the `name` configuration option in your `config/app.php` configuration file. This value will be used in the header and footer of your mail notification messages.

#### [Error Messages](#error-messages)
Some notifications inform users of errors, such as a failed invoice payment. You may indicate that a mail message is regarding an error by calling the `error` method when building your message. When using the `error` method on a mail message, the call to action button will be red instead of black:

```php	
/**
 * Get the mail representation of the notification.
*/
publicfunctiontoMail(object$notifiable):MailMessage
{
return (newMailMessage)
->error()
->subject('Invoice Payment Failed')
->line('...');
}
```
#### [Other Mail Notification Formatting Options](#other-mail-notification-formatting-options)
Instead of defining the "lines" of text in the notification class, you may use the `view` method to specify a custom template that should be used to render the notification email:

```php	
/**
 * Get the mail representation of the notification.
*/
publicfunctiontoMail(object$notifiable):MailMessage
{
return (newMailMessage)->view(
'emails.name', ['invoice'=>$this->invoice]
 );
}
```
You may specify a plain-text view for the mail message by passing the view name as the second element of an array that is given to the `view` method:

```php	
/**
 * Get the mail representation of the notification.
*/
publicfunctiontoMail(object$notifiable):MailMessage
{
return (newMailMessage)->view(
 ['emails.name.html', 'emails.name.plain'],
 ['invoice'=>$this->invoice]
 );
}
```
### [Customizing The Sender](#customizing-the-sender)
By default, the email's sender / from address is defined in the `config/mail.php` configuration file. However, you may specify the from address for a specific notification using the `from` method:

```php	
/**
 * Get the mail representation of the notification.
*/
publicfunctiontoMail(object$notifiable):MailMessage
{
return (newMailMessage)
->from('[[email protected]](/cdn-cgi/l/email-protection)', 'Barrett Blair')
->line('...');
}
```
### [Customizing The Recipient](#customizing-the-recipient)
When sending notifications via the `mail` channel, the notification system will automatically look for an `email` property on your notifiable entity. You may customize which email address is used to deliver the notification by defining a `routeNotificationForMail` method on the notifiable entity:

```php	
<?php
 
namespace App\Models;
 
use Illuminate\Foundation\Auth\Useras Authenticatable;
use Illuminate\Notifications\Notifiable;
use Illuminate\Notifications\Notification;
 
classUserextendsAuthenticatable
{
useNotifiable;
 
/**
 * Route notifications for the mail channel.
 *
 * @returnarray<string, string>|string
*/
publicfunctionrouteNotificationForMail(Notification$notification):array|string
 {
// Return email address only...
return$this->email_address;
 
// Return email address and name...
return [$this->email_address=>$this->name];
 }
}
```
### [Customizing The Subject](#customizing-the-subject)
By default, the email's subject is the class name of the notification formatted to "Title Case". So, if your notification class is named `InvoicePaid`, the email's subject will be `Invoice Paid`. If you would like to specify a different subject for the message, you may call the `subject` method when building your message:

```php	
/**
 * Get the mail representation of the notification.
*/
publicfunctiontoMail(object$notifiable):MailMessage
{
return (newMailMessage)
->subject('Notification Subject')
->line('...');
}
```
### [Customizing The Mailer](#customizing-the-mailer)
By default, the email notification will be sent using the default mailer defined in the `config/mail.php` configuration file. However, you may specify a different mailer at runtime by calling the `mailer` method when building your message:

```php	
/**
 * Get the mail representation of the notification.
*/
publicfunctiontoMail(object$notifiable):MailMessage
{
return (newMailMessage)
->mailer('postmark')
->line('...');
}
```
### [Customizing The Templates](#customizing-the-templates)
You can modify the HTML and plain-text template used by mail notifications by publishing the notification package's resources. After running this command, the mail notification templates will be located in the `resources/views/vendor/notifications` directory:

```shell	
php artisanvendor:publish--tag=laravel-notifications
```
### [Attachments](#mail-attachments)
To add attachments to an email notification, use the `attach` method while building your message. The `attach` method accepts the absolute path to the file as its first argument:

```php	
/**
 * Get the mail representation of the notification.
*/
publicfunctiontoMail(object$notifiable):MailMessage
{
return (newMailMessage)
->greeting('Hello!')
->attach('/path/to/file');
}
```
> **Note**  
>  The `attach` method offered by notification mail messages also accepts [attachable objects](/docs/master/mail#attachable-objects). Please consult the comprehensive [attachable object documentation](/docs/master/mail#attachable-objects) to learn more.

When attaching files to a message, you may also specify the display name and / or MIME type by passing an `array` as the second argument to the `attach` method:

```php	
/**
 * Get the mail representation of the notification.
*/
publicfunctiontoMail(object$notifiable):MailMessage
{
return (newMailMessage)
->greeting('Hello!')
->attach('/path/to/file', [
'as'=>'name.pdf',
'mime'=>'application/pdf',
 ]);
}
```
Unlike attaching files in mailable objects, you may not attach a file directly from a storage disk using `attachFromStorage`. You should rather use the `attach` method with an absolute path to the file on the storage disk. Alternatively, you could return a [mailable](/docs/master/mail#generating-mailables) from the `toMail` method:

```php	
use App\Mail\InvoicePaidas InvoicePaidMailable;
 
/**
 * Get the mail representation of the notification.
*/
publicfunctiontoMail(object$notifiable):Mailable
{
return (newInvoicePaidMailable($this->invoice))
->to($notifiable->email)
->attachFromStorage('/path/to/file');
}
```
When necessary, multiple files may be attached to a message using the `attachMany` method:

```php	
/**
 * Get the mail representation of the notification.
*/
publicfunctiontoMail(object$notifiable):MailMessage
{
return (newMailMessage)
->greeting('Hello!')
->attachMany([
'/path/to/forge.svg',
'/path/to/vapor.svg'=> [
'as'=>'Logo.svg',
'mime'=>'image/svg+xml',
 ],
 ]);
}
```
#### [Raw Data Attachments](#raw-data-attachments)
The `attachData` method may be used to attach a raw string of bytes as an attachment. When calling the `attachData` method, you should provide the filename that should be assigned to the attachment:

```php	
/**
 * Get the mail representation of the notification.
*/
publicfunctiontoMail(object$notifiable):MailMessage
{
return (newMailMessage)
->greeting('Hello!')
->attachData($this->pdf, 'name.pdf', [
'mime'=>'application/pdf',
 ]);
}
```
### [Adding Tags & Metadata](#adding-tags-metadata)
Some third-party email providers such as Mailgun and Postmark support message "tags" and "metadata", which may be used to group and track emails sent by your application. You may add tags and metadata to an email message via the `tag` and `metadata` methods:

```php	
/**
 * Get the mail representation of the notification.
*/
publicfunctiontoMail(object$notifiable):MailMessage
{
return (newMailMessage)
->greeting('Comment Upvoted!')
->tag('upvote')
->metadata('comment_id', $this->comment->id);
}
```
If your application is using the Mailgun driver, you may consult Mailgun's documentation for more information on [tags](https://documentation.mailgun.com/en/latest/user_manual.html#tagging-1) and [metadata](https://documentation.mailgun.com/en/latest/user_manual.html#attaching-data-to-messages). Likewise, the Postmark documentation may also be consulted for more information on their support for [tags](https://postmarkapp.com/blog/tags-support-for-smtp) and [metadata](https://postmarkapp.com/support/article/1125-custom-metadata-faq).

If your application is using Amazon SES to send emails, you should use the `metadata` method to attach [SES "tags"](https://docs.aws.amazon.com/ses/latest/APIReference/API_MessageTag.html) to the message.

### [Customizing The Symfony Message](#customizing-the-symfony-message)
The `withSymfonyMessage` method of the `MailMessage` class allows you to register a closure which will be invoked with the Symfony Message instance before sending the message. This gives you an opportunity to deeply customize the message before it is delivered:

```php	
use Symfony\Component\Mime\Email;
 
/**
 * Get the mail representation of the notification.
*/
publicfunctiontoMail(object$notifiable):MailMessage
{
return (newMailMessage)
->withSymfonyMessage(function(Email$message) {
$message->getHeaders()->addTextHeader(
'Custom-Header', 'Header Value'
 );
 });
}
```
### [Using Mailables](#using-mailables)
If needed, you may return a full [mailable object](/docs/master/mail) from your notification's `toMail` method. When returning a `Mailable` instead of a `MailMessage`, you will need to specify the message recipient using the mailable object's `to` method:

```php	
use App\Mail\InvoicePaidas InvoicePaidMailable;
use Illuminate\Mail\Mailable;
 
/**
 * Get the mail representation of the notification.
*/
publicfunctiontoMail(object$notifiable):Mailable
{
return (newInvoicePaidMailable($this->invoice))
->to($notifiable->email);
}
```
#### [Mailables & On-Demand Notifications](#mailables-and-on-demand-notifications)
If you are sending an [on-demand notification](#on-demand-notifications), the `$notifiable` instance given to the `toMail` method will be an instance of `Illuminate\Notifications\AnonymousNotifiable`, which offers a `routeNotificationFor` method that may be used to retrieve the email address the on-demand notification should be sent to:

```php	
use App\Mail\InvoicePaidas InvoicePaidMailable;
use Illuminate\Notifications\AnonymousNotifiable;
use Illuminate\Mail\Mailable;
 
/**
 * Get the mail representation of the notification.
*/
publicfunctiontoMail(object$notifiable):Mailable
{
$address=$notifiableinstanceofAnonymousNotifiable
?$notifiable->routeNotificationFor('mail')
:$notifiable->email;
 
return (newInvoicePaidMailable($this->invoice))
->to($address);
}
```
### [Previewing Mail Notifications](#previewing-mail-notifications)
When designing a mail notification template, it is convenient to quickly preview the rendered mail message in your browser like a typical Blade template. For this reason, Laravel allows you to return any mail message generated by a mail notification directly from a route closure or controller. When a `MailMessage` is returned, it will be rendered and displayed in the browser, allowing you to quickly preview its design without needing to send it to an actual email address:

```php	
use App\Models\Invoice;
use App\Notifications\InvoicePaid;
 
Route::get('/notification', function() {
$invoice=Invoice::find(1);
 
return (newInvoicePaid($invoice))
->toMail($invoice->user);
});
```
## [Markdown Mail Notifications](#markdown-mail-notifications)
Markdown mail notifications allow you to take advantage of the pre-built templates of mail notifications, while giving you more freedom to write longer, customized messages. Since the messages are written in Markdown, Laravel is able to render beautiful, responsive HTML templates for the messages while also automatically generating a plain-text counterpart.

### [Generating The Message](#generating-the-message)
To generate a notification with a corresponding Markdown template, you may use the `--markdown` option of the `make:notification` Artisan command:

```shell	
php artisanmake:notificationInvoicePaid--markdown=mail.invoice.paid
```
Like all other mail notifications, notifications that use Markdown templates should define a `toMail` method on their notification class. However, instead of using the `line` and `action` methods to construct the notification, use the `markdown` method to specify the name of the Markdown template that should be used. An array of data you wish to make available to the template may be passed as the method's second argument:

```php	
/**
 * Get the mail representation of the notification.
*/
publicfunctiontoMail(object$notifiable):MailMessage
{
$url=url('/invoice/'.$this->invoice->id);
 
return (newMailMessage)
->subject('Invoice Paid')
->markdown('mail.invoice.paid', ['url'=>$url]);
}
```
### [Writing The Message](#writing-the-message)
Markdown mail notifications use a combination of Blade components and Markdown syntax which allow you to easily construct notifications while leveraging Laravel's pre-crafted notification components:

```blade	
<x-mail::message>
# Invoice Paid
 
Your invoice has been paid!
 
<x-mail::button:url="$url">
View Invoice
</x-mail::button>
 
Thanks,<br>
{{config('app.name') }}
</x-mail::message>
```
#### [Button Component](#button-component)
The button component renders a centered button link. The component accepts two arguments, a `url` and an optional `color`. Supported colors are `primary`, `green`, and `red`. You may add as many button components to a notification as you wish:

```blade	
<x-mail::button:url="$url"color="green">
View Invoice
</x-mail::button>
```
#### [Panel Component](#panel-component)
The panel component renders the given block of text in a panel that has a slightly different background color than the rest of the notification. This allows you to draw attention to a given block of text:

```blade	
<x-mail::panel>
This is the panel content.
</x-mail::panel>
```
#### [Table Component](#table-component)
The table component allows you to transform a Markdown table into an HTML table. The component accepts the Markdown table as its content. Table column alignment is supported using the default Markdown table alignment syntax:

```blade	
<x-mail::table>
| Laravel | Table | Example |
| ------------- |:-------------:| --------:|
| Col 2 is | Centered | $10 |
| Col 3 is | Right-Aligned | $20 |
</x-mail::table>
```
### [Customizing The Components](#customizing-the-components)
You may export all of the Markdown notification components to your own application for customization. To export the components, use the `vendor:publish` Artisan command to publish the `laravel-mail` asset tag:

```shell	
php artisanvendor:publish--tag=laravel-mail
```
This command will publish the Markdown mail components to the `resources/views/vendor/mail` directory. The `mail` directory will contain an `html` and a `text` directory, each containing their respective representations of every available component. You are free to customize these components however you like.

#### [Customizing The CSS](#customizing-the-css)
After exporting the components, the `resources/views/vendor/mail/html/themes` directory will contain a `default.css` file. You may customize the CSS in this file and your styles will automatically be in-lined within the HTML representations of your Markdown notifications.

If you would like to build an entirely new theme for Laravel's Markdown components, you may place a CSS file within the `html/themes` directory. After naming and saving your CSS file, update the `theme` option of the `mail` configuration file to match the name of your new theme.

To customize the theme for an individual notification, you may call the `theme` method while building the notification's mail message. The `theme` method accepts the name of the theme that should be used when sending the notification:

```php	
/**
 * Get the mail representation of the notification.
*/
publicfunctiontoMail(object$notifiable):MailMessage
{
return (newMailMessage)
->theme('invoice')
->subject('Invoice Paid')
->markdown('mail.invoice.paid', ['url'=>$url]);
}
```
## [Database Notifications](#database-notifications)
### [Prerequisites](#database-prerequisites)
The `database` notification channel stores the notification information in a database table. This table will contain information such as the notification type as well as a JSON data structure that describes the notification.

You can query the table to display the notifications in your application's user interface. But, before you can do that, you will need to create a database table to hold your notifications. You may use the `notifications:table` command to generate a [migration](/docs/master/migrations) with the proper table schema:

```shell	
php artisannotifications:table
 
php artisanmigrate
```
### [Formatting Database Notifications](#formatting-database-notifications)
If a notification supports being stored in a database table, you should define a `toDatabase` or `toArray` method on the notification class. This method will receive a `$notifiable` entity and should return a plain PHP array. The returned array will be encoded as JSON and stored in the `data` column of your `notifications` table. Let's take a look at an example `toArray` method:

```php	
/**
 * Get the array representation of the notification.
 *
 * @returnarray<string, mixed>
*/
publicfunctiontoArray(object$notifiable):array
{
return [
'invoice_id'=>$this->invoice->id,
'amount'=>$this->invoice->amount,
 ];
}
```
#### [`toDatabase` Vs. `toArray`](#todatabase-vs-toarray)
The `toArray` method is also used by the `broadcast` channel to determine which data to broadcast to your JavaScript powered frontend. If you would like to have two different array representations for the `database` and `broadcast` channels, you should define a `toDatabase` method instead of a `toArray` method.

### [Accessing The Notifications](#accessing-the-notifications)
Once notifications are stored in the database, you need a convenient way to access them from your notifiable entities. The `Illuminate\Notifications\Notifiable` trait, which is included on Laravel's default `App\Models\User` model, includes a `notifications`[Eloquent relationship](/docs/master/eloquent-relationships) that returns the notifications for the entity. To fetch notifications, you may access this method like any other Eloquent relationship. By default, notifications will be sorted by the `created_at` timestamp with the most recent notifications at the beginning of the collection:

```php	
$user= App\Models\User::find(1);
 
foreach ($user->notificationsas$notification) {
echo$notification->type;
}
```
If you want to retrieve only the "unread" notifications, you may use the `unreadNotifications` relationship. Again, these notifications will be sorted by the `created_at` timestamp with the most recent notifications at the beginning of the collection:

```php	
$user= App\Models\User::find(1);
 
foreach ($user->unreadNotificationsas$notification) {
echo$notification->type;
}
```
> **Note**  
>  To access your notifications from your JavaScript client, you should define a notification controller for your application which returns the notifications for a notifiable entity, such as the current user. You may then make an HTTP request to that controller's URL from your JavaScript client.

### [Marking Notifications As Read](#marking-notifications-as-read)
Typically, you will want to mark a notification as "read" when a user views it. The `Illuminate\Notifications\Notifiable` trait provides a `markAsRead` method, which updates the `read_at` column on the notification's database record:

```php	
$user= App\Models\User::find(1);
 
foreach ($user->unreadNotificationsas$notification) {
$notification->markAsRead();
}
```
However, instead of looping through each notification, you may use the `markAsRead` method directly on a collection of notifications:

```php	
$user->unreadNotifications->markAsRead();
```
You may also use a mass-update query to mark all of the notifications as read without retrieving them from the database:

```php	
$user= App\Models\User::find(1);
 
$user->unreadNotifications()->update(['read_at'=>now()]);
```
You may `delete` the notifications to remove them from the table entirely:

```php	
$user->notifications()->delete();
```
## [Broadcast Notifications](#broadcast-notifications)
### [Prerequisites](#broadcast-prerequisites)
Before broadcasting notifications, you should configure and be familiar with Laravel's [event broadcasting](/docs/master/broadcasting) services. Event broadcasting provides a way to react to server-side Laravel events from your JavaScript powered frontend.

### [Formatting Broadcast Notifications](#formatting-broadcast-notifications)
The `broadcast` channel broadcasts notifications using Laravel's [event broadcasting](/docs/master/broadcasting) services, allowing your JavaScript powered frontend to catch notifications in realtime. If a notification supports broadcasting, you can define a `toBroadcast` method on the notification class. This method will receive a `$notifiable` entity and should return a `BroadcastMessage` instance. If the `toBroadcast` method does not exist, the `toArray` method will be used to gather the data that should be broadcast. The returned data will be encoded as JSON and broadcast to your JavaScript powered frontend. Let's take a look at an example `toBroadcast` method:

```php	
use Illuminate\Notifications\Messages\BroadcastMessage;
 
/**
 * Get the broadcastable representation of the notification.
*/
publicfunctiontoBroadcast(object$notifiable):BroadcastMessage
{
returnnewBroadcastMessage([
'invoice_id'=>$this->invoice->id,
'amount'=>$this->invoice->amount,
 ]);
}
```
#### [Broadcast Queue Configuration](#broadcast-queue-configuration)
All broadcast notifications are queued for broadcasting. If you would like to configure the queue connection or queue name that is used to queue the broadcast operation, you may use the `onConnection` and `onQueue` methods of the `BroadcastMessage`:

```php	
return (newBroadcastMessage($data))
->onConnection('sqs')
->onQueue('broadcasts');
```
#### [Customizing The Notification Type](#customizing-the-notification-type)
In addition to the data you specify, all broadcast notifications also have a `type` field containing the full class name of the notification. If you would like to customize the notification `type`, you may define a `broadcastType` method on the notification class:

```php	
/**
 * Get the type of the notification being broadcast.
*/
publicfunctionbroadcastType():string
{
return'broadcast.message';
}
```
### [Listening For Notifications](#listening-for-notifications)
Notifications will broadcast on a private channel formatted using a `{notifiable}.{id}` convention. So, if you are sending a notification to an `App\Models\User` instance with an ID of `1`, the notification will be broadcast on the `App.Models.User.1` private channel. When using [Laravel Echo](/docs/master/broadcasting#client-side-installation), you may easily listen for notifications on a channel using the `notification` method:

```php	
Echo.private('App.Models.User.'+userId)
.notification((notification)=>{
 console.log(notification.type);
});
```
#### [Customizing The Notification Channel](#customizing-the-notification-channel)
If you would like to customize which channel that an entity's broadcast notifications are broadcast on, you may define a `receivesBroadcastNotificationsOn` method on the notifiable entity:

```php	
<?php
 
namespace App\Models;
 
use Illuminate\Broadcasting\PrivateChannel;
use Illuminate\Foundation\Auth\Useras Authenticatable;
use Illuminate\Notifications\Notifiable;
 
classUserextendsAuthenticatable
{
useNotifiable;
 
/**
 * The channels the user receives notification broadcasts on.
*/
publicfunctionreceivesBroadcastNotificationsOn():string
 {
return'users.'.$this->id;
 }
}
```
## [SMS Notifications](#sms-notifications)
### [Prerequisites](#sms-prerequisites)
Sending SMS notifications in Laravel is powered by [Vonage](https://www.vonage.com/) (formerly known as Nexmo). Before you can send notifications via Vonage, you need to install the `laravel/vonage-notification-channel` and `guzzlehttp/guzzle` packages:

```php	
composerrequirelaravel/vonage-notification-channelguzzlehttp/guzzle
```
The package includes a [configuration file](https://github.com/laravel/vonage-notification-channel/blob/3.x/config/vonage.php). However, you are not required to export this configuration file to your own application. You can simply use the `VONAGE_KEY` and `VONAGE_SECRET` environment variables to define your Vonage public and secret keys.

After defining your keys, you should set a `VONAGE_SMS_FROM` environment variable that defines the phone number that your SMS messages should be sent from by default. You may generate this phone number within the Vonage control panel:

```php	
VONAGE_SMS_FROM=15556666666
```
### [Formatting SMS Notifications](#formatting-sms-notifications)
If a notification supports being sent as an SMS, you should define a `toVonage` method on the notification class. This method will receive a `$notifiable` entity and should return an `Illuminate\Notifications\Messages\VonageMessage` instance:

```php	
use Illuminate\Notifications\Messages\VonageMessage;
 
/**
 * Get the Vonage / SMS representation of the notification.
*/
publicfunctiontoVonage(object$notifiable):VonageMessage
{
return (newVonageMessage)
->content('Your SMS message content');
}
```
#### [Unicode Content](#unicode-content)
If your SMS message will contain unicode characters, you should call the `unicode` method when constructing the `VonageMessage` instance:

```php	
use Illuminate\Notifications\Messages\VonageMessage;
 
/**
 * Get the Vonage / SMS representation of the notification.
*/
publicfunctiontoVonage(object$notifiable):VonageMessage
{
return (newVonageMessage)
->content('Your unicode message')
->unicode();
}
```
### [Customizing The "From" Number](#customizing-the-from-number)
If you would like to send some notifications from a phone number that is different from the phone number specified by your `VONAGE_SMS_FROM` environment variable, you may call the `from` method on a `VonageMessage` instance:

```php	
use Illuminate\Notifications\Messages\VonageMessage;
 
/**
 * Get the Vonage / SMS representation of the notification.
*/
publicfunctiontoVonage(object$notifiable):VonageMessage
{
return (newVonageMessage)
->content('Your SMS message content')
->from('15554443333');
}
```
### [Adding a Client Reference](#adding-a-client-reference)
If you would like to keep track of costs per user, team, or client, you may add a "client reference" to the notification. Vonage will allow you to generate reports using this client reference so that you can better understand a particular customer's SMS usage. The client reference can be any string up to 40 characters:

```php	
use Illuminate\Notifications\Messages\VonageMessage;
 
/**
 * Get the Vonage / SMS representation of the notification.
*/
publicfunctiontoVonage(object$notifiable):VonageMessage
{
return (newVonageMessage)
->clientReference((string) $notifiable->id)
->content('Your SMS message content');
}
```
### [Routing SMS Notifications](#routing-sms-notifications)
To route Vonage notifications to the proper phone number, define a `routeNotificationForVonage` method on your notifiable entity:

```php	
<?php
 
namespace App\Models;
 
use Illuminate\Foundation\Auth\Useras Authenticatable;
use Illuminate\Notifications\Notifiable;
use Illuminate\Notifications\Notification;
 
classUserextendsAuthenticatable
{
useNotifiable;
 
/**
 * Route notifications for the Vonage channel.
*/
publicfunctionrouteNotificationForVonage(Notification$notification):string
 {
return$this->phone_number;
 }
}
```
## [Slack Notifications](#slack-notifications)
### [Prerequisites](#slack-prerequisites)
Before you can send notifications via Slack, you must install the Slack notification channel via Composer:

```shell	
composer requirelaravel/slack-notification-channel
```
You will also need to create a [Slack App](https://api.slack.com/apps?new_app=1) for your team. After creating the App, you should configure an "Incoming Webhook" for the workspace. Slack will then provide you with a webhook URL that you may use when [routing Slack notifications](#routing-slack-notifications).

### [Formatting Slack Notifications](#formatting-slack-notifications)
If a notification supports being sent as a Slack message, you should define a `toSlack` method on the notification class. This method will receive a `$notifiable` entity and should return an `Illuminate\Notifications\Messages\SlackMessage` instance. Slack messages may contain text content as well as an "attachment" that formats additional text or an array of fields. Let's take a look at a basic `toSlack` example:

```php	
use Illuminate\Notifications\Messages\SlackMessage;
 
/**
 * Get the Slack representation of the notification.
*/
publicfunctiontoSlack(object$notifiable):SlackMessage
{
return (newSlackMessage)
->content('One of your invoices has been paid!');
}
```
### [Slack Attachments](#slack-attachments)
You may also add "attachments" to Slack messages. Attachments provide richer formatting options than simple text messages. In this example, we will send an error notification about an exception that occurred in an application, including a link to view more details about the exception:

```php	
use Illuminate\Notifications\Messages\SlackAttachment;
use Illuminate\Notifications\Messages\SlackMessage;
 
/**
 * Get the Slack representation of the notification.
*/
publicfunctiontoSlack(object$notifiable):SlackMessage
{
$url=url('/exceptions/'.$this->exception->id);
 
return (newSlackMessage)
->error()
->content('Whoops! Something went wrong.')
->attachment(function(SlackAttachment$attachment)use($url) {
$attachment->title('Exception: File Not Found', $url)
->content('File [background.jpg] was not found.');
 });
}
```
Attachments also allow you to specify an array of data that should be presented to the user. The given data will be presented in a table-style format for easy reading:

```php	
use Illuminate\Notifications\Messages\SlackAttachment;
use Illuminate\Notifications\Messages\SlackMessage;
 
/**
 * Get the Slack representation of the notification.
*/
publicfunctiontoSlack(object$notifiable):SlackMessage
{
$url=url('/invoices/'.$this->invoice->id);
 
return (newSlackMessage)
->success()
->content('One of your invoices has been paid!')
->attachment(function(SlackAttachment$attachment)use($url) {
$attachment->title('Invoice 1322', $url)
->fields([
'Title'=>'Server Expenses',
'Amount'=>'$1,234',
'Via'=>'American Express',
'Was Overdue'=>':-1:',
 ]);
 });
}
```
#### [Markdown Attachment Content](#markdown-attachment-content)
If some of your attachment fields contain Markdown, you may use the `markdown` method to instruct Slack to parse and display the given attachment fields as Markdown formatted text. The values accepted by this method are: `pretext`, `text`, and / or `fields`. For more information about Slack attachment formatting, check out the [Slack API documentation](https://api.slack.com/docs/message-formatting#message_formatting):

```php	
use Illuminate\Notifications\Messages\SlackAttachment;
use Illuminate\Notifications\Messages\SlackMessage;
 
/**
 * Get the Slack representation of the notification.
*/
publicfunctiontoSlack(object$notifiable):SlackMessage
{
$url=url('/exceptions/'.$this->exception->id);
 
return (newSlackMessage)
->error()
->content('Whoops! Something went wrong.')
->attachment(function(SlackAttachment$attachment)use($url) {
$attachment->title('Exception: File Not Found', $url)
->content('File [background.jpg] was *not found*.')
->markdown(['text']);
 });
}
```
### [Routing Slack Notifications](#routing-slack-notifications)
To route Slack notifications to the proper Slack team and channel, define a `routeNotificationForSlack` method on your notifiable entity. This should return the webhook URL to which the notification should be delivered. Webhook URLs may be generated by adding an "Incoming Webhook" service to your Slack team:

```php	
<?php
 
namespace App\Models;
 
use Illuminate\Foundation\Auth\Useras Authenticatable;
use Illuminate\Notifications\Notifiable;
use Illuminate\Notifications\Notification;
 
classUserextendsAuthenticatable
{
useNotifiable;
 
/**
 * Route notifications for the Slack channel.
*/
publicfunctionrouteNotificationForSlack(Notification$notification):string
 {
return'https://hooks.slack.com/services/...';
 }
}
```
## [Localizing Notifications](#localizing-notifications)
Laravel allows you to send notifications in a locale other than the HTTP request's current locale, and will even remember this locale if the notification is queued.

To accomplish this, the `Illuminate\Notifications\Notification` class offers a `locale` method to set the desired language. The application will change into this locale when the notification is being evaluated and then revert back to the previous locale when evaluation is complete:

```php	
$user->notify((newInvoicePaid($invoice))->locale('es'));
```
Localization of multiple notifiable entries may also be achieved via the `Notification` facade:

```php	
Notification::locale('es')->send(
$users, newInvoicePaid($invoice)
);
```
### [User Preferred Locales](#user-preferred-locales)
Sometimes, applications store each user's preferred locale. By implementing the `HasLocalePreference` contract on your notifiable model, you may instruct Laravel to use this stored locale when sending a notification:

```php	
use Illuminate\Contracts\Translation\HasLocalePreference;
 
classUserextendsModelimplementsHasLocalePreference
{
/**
 * Get the user's preferred locale.
*/
publicfunctionpreferredLocale():string
 {
return$this->locale;
 }
}
```
Once you have implemented the interface, Laravel will automatically use the preferred locale when sending notifications and mailables to the model. Therefore, there is no need to call the `locale` method when using this interface:

```php	
$user->notify(newInvoicePaid($invoice));
```
## [Testing](#testing)
You may use the `Notification` facade's `fake` method to prevent notifications from being sent. Typically, sending notifications is unrelated to the code you are actually testing. Most likely, it is sufficient to simply assert that Laravel was instructed to send a given notification.

After calling the `Notification` facade's `fake` method, you may then assert that notifications were instructed to be sent to users and even inspect the data the notifications received:

```php	
<?php
 
namespace Tests\Feature;
 
use App\Notifications\OrderShipped;
use Illuminate\Support\Facades\Notification;
use Tests\TestCase;
 
classExampleTestextendsTestCase
{
publicfunctiontest_orders_can_be_shipped():void
 {
Notification::fake();
 
// Perform order shipping...
 
// Assert that no notifications were sent...
Notification::assertNothingSent();
 
// Assert a notification was sent to the given users...
Notification::assertSentTo(
 [$user], OrderShipped::class
 );
 
// Assert a notification was not sent...
Notification::assertNotSentTo(
 [$user], AnotherNotification::class
 );
 
// Assert that a given number of notifications were sent...
Notification::assertCount(3);
 }
}
```
You may pass a closure to the `assertSentTo` or `assertNotSentTo` methods in order to assert that a notification was sent that passes a given "truth test". If at least one notification was sent that passes the given truth test then the assertion will be successful:

```php	
Notification::assertSentTo(
$user,
function(OrderShipped$notification, array$channels)use($order) {
return$notification->order->id===$order->id;
 }
);
```
#### [On-Demand Notifications](#on-demand-notifications)
If the code you are testing sends [on-demand notifications](#on-demand-notifications), you can test that the on-demand notification was sent via the `assertSentOnDemand` method:

```php	
Notification::assertSentOnDemand(OrderShipped::class);
```
By passing a closure as the second argument to the `assertSentOnDemand` method, you may determine if an on-demand notification was sent to the correct "route" address:

```php	
Notification::assertSentOnDemand(
OrderShipped::class,
function(OrderShipped$notification, array$channels, object$notifiable)use($user) {
return$notifiable->routes['mail'] ===$user->email;
 }
);
```
## [Notification Events](#notification-events)
#### [Notification Sending Event](#notification-sending-event)
When a notification is sending, the `Illuminate\Notifications\Events\NotificationSending`[event](/docs/master/events) is dispatched by the notification system. This contains the "notifiable" entity and the notification instance itself. You may register listeners for this event in your application's `EventServiceProvider`:

```php	
use App\Listeners\CheckNotificationStatus;
use Illuminate\Notifications\Events\NotificationSending;
 
/**
 * The event listener mappings for the application.
 *
 * @vararray
*/
protected$listen= [
NotificationSending::class=> [
CheckNotificationStatus::class,
 ],
];
```
The notification will not be sent if an event listener for the `NotificationSending` event returns `false` from its `handle` method:

```php	
use Illuminate\Notifications\Events\NotificationSending;
 
/**
 * Handle the event.
*/
publicfunctionhandle(NotificationSending$event):void
{
returnfalse;
}
```
Within an event listener, you may access the `notifiable`, `notification`, and `channel` properties on the event to learn more about the notification recipient or the notification itself:

```php	
/**
 * Handle the event.
*/
publicfunctionhandle(NotificationSending$event):void
{
// $event->channel
// $event->notifiable
// $event->notification
}
```
#### [Notification Sent Event](#notification-sent-event)
When a notification is sent, the `Illuminate\Notifications\Events\NotificationSent`[event](/docs/master/events) is dispatched by the notification system. This contains the "notifiable" entity and the notification instance itself. You may register listeners for this event in your `EventServiceProvider`:

```php	
use App\Listeners\LogNotification;
use Illuminate\Notifications\Events\NotificationSent;
 
/**
 * The event listener mappings for the application.
 *
 * @vararray
*/
protected$listen= [
NotificationSent::class=> [
LogNotification::class,
 ],
];
```
> **Note**  
>  After registering listeners in your `EventServiceProvider`, use the `event:generate` Artisan command to quickly generate listener classes.

Within an event listener, you may access the `notifiable`, `notification`, `channel`, and `response` properties on the event to learn more about the notification recipient or the notification itself:

```php	
/**
 * Handle the event.
*/
publicfunctionhandle(NotificationSent$event):void
{
// $event->channel
// $event->notifiable
// $event->notification
// $event->response
}
```
## [Custom Channels](#custom-channels)
Laravel ships with a handful of notification channels, but you may want to write your own drivers to deliver notifications via other channels. Laravel makes it simple. To get started, define a class that contains a `send` method. The method should receive two arguments: a `$notifiable` and a `$notification`.

Within the `send` method, you may call methods on the notification to retrieve a message object understood by your channel and then send the notification to the `$notifiable` instance however you wish:

```php	
<?php
 
namespace App\Notifications;
 
use Illuminate\Notifications\Notification;
 
classVoiceChannel
{
/**
 * Send the given notification.
*/
publicfunctionsend(object$notifiable, Notification$notification):void
 {
$message=$notification->toVoice($notifiable);
 
// Send notification to the $notifiable instance...
 }
}
```
Once your notification channel class has been defined, you may return the class name from the `via` method of any of your notifications. In this example, the `toVoice` method of your notification can return whatever object you choose to represent voice messages. For example, you might define your own `VoiceMessage` class to represent these messages:

```php	
<?php
 
namespace App\Notifications;
 
use App\Notifications\Messages\VoiceMessage;
use App\Notifications\VoiceChannel;
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Notifications\Notification;
 
classInvoicePaidextendsNotification
{
useQueueable;
 
/**
 * Get the notification channels.
*/
publicfunctionvia(object$notifiable):string
 {
returnVoiceChannel::class;
 }
 
/**
 * Get the voice representation of the notification.
*/
publicfunctiontoVoice(object$notifiable):VoiceMessage
 {
// ...
 }
}
```
