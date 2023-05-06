# Mail
- [Introduction](#introduction)
    - [Configuration](#configuration)

    - [Driver Prerequisites](#driver-prerequisites)

    - [Failover Configuration](#failover-configuration)


- [Generating Mailables](#generating-mailables)
- [Writing Mailables](#writing-mailables)
    - [Configuring The Sender](#configuring-the-sender)

    - [Configuring The View](#configuring-the-view)

    - [View Data](#view-data)

    - [Attachments](#attachments)

    - [Inline Attachments](#inline-attachments)

    - [Attachable Objects](#attachable-objects)

    - [Headers](#headers)

    - [Tags & Metadata](#tags-and-metadata)

    - [Customizing The Symfony Message](#customizing-the-symfony-message)


- [Markdown Mailables](#markdown-mailables)
    - [Generating Markdown Mailables](#generating-markdown-mailables)

    - [Writing Markdown Messages](#writing-markdown-messages)

    - [Customizing The Components](#customizing-the-components)


- [Sending Mail](#sending-mail)
    - [Queueing Mail](#queueing-mail)


- [Rendering Mailables](#rendering-mailables)
    - [Previewing Mailables In The Browser](#previewing-mailables-in-the-browser)


- [Localizing Mailables](#localizing-mailables)
- [Testing](#testing-mailables)
    - [Testing Mailable Content](#testing-mailable-content)

    - [Testing Mailable Sending](#testing-mailable-sending)


- [Mail & Local Development](#mail-and-local-development)
- [Events](#events)
- [Custom Transports](#custom-transports)
    - [Additional Symfony Transports](#additional-symfony-transports)



## [Introduction](#introduction)
Sending email doesn't have to be complicated. Laravel provides a clean, simple email API powered by the popular [Symfony Mailer](https://symfony.com/doc/6.0/mailer.html) component. Laravel and Symfony Mailer provide drivers for sending email via SMTP, Mailgun, Postmark, Amazon SES, and `sendmail`, allowing you to quickly get started sending mail through a local or cloud based service of your choice.

### [Configuration](#configuration)
Laravel's email services may be configured via your application's `config/mail.php` configuration file. Each mailer configured within this file may have its own unique configuration and even its own unique "transport", allowing your application to use different email services to send certain email messages. For example, your application might use Postmark to send transactional emails while using Amazon SES to send bulk emails.

Within your `mail` configuration file, you will find a `mailers` configuration array. This array contains a sample configuration entry for each of the major mail drivers / transports supported by Laravel, while the `default` configuration value determines which mailer will be used by default when your application needs to send an email message.

### [Driver / Transport Prerequisites](#driver-prerequisites)
The API based drivers such as Mailgun and Postmark are often simpler and faster than sending mail via SMTP servers. Whenever possible, we recommend that you use one of these drivers.

#### [Mailgun Driver](#mailgun-driver)
To use the Mailgun driver, install Symfony's Mailgun Mailer transport via Composer:

```shell	
composer requiresymfony/mailgun-mailersymfony/http-client
```
Next, set the `default` option in your application's `config/mail.php` configuration file to `mailgun`. After configuring your application's default mailer, verify that your `config/services.php` configuration file contains the following options:

```php	
'mailgun'=> [
'domain'=>env('MAILGUN_DOMAIN'),
'secret'=>env('MAILGUN_SECRET'),
],
```
If you are not using the United States [Mailgun region](https://documentation.mailgun.com/en/latest/api-intro.html#mailgun-regions), you may define your region's endpoint in the `services` configuration file:

```php	
'mailgun'=> [
'domain'=>env('MAILGUN_DOMAIN'),
'secret'=>env('MAILGUN_SECRET'),
'endpoint'=>env('MAILGUN_ENDPOINT','api.eu.mailgun.net'),
],
```
#### [Postmark Driver](#postmark-driver)
To use the Postmark driver, install Symfony's Postmark Mailer transport via Composer:

```shell	
composer requiresymfony/postmark-mailersymfony/http-client
```
Next, set the `default` option in your application's `config/mail.php` configuration file to `postmark`. After configuring your application's default mailer, verify that your `config/services.php` configuration file contains the following options:

```php	
'postmark'=> [
'token'=>env('POSTMARK_TOKEN'),
],
```
If you would like to specify the Postmark message stream that should be used by a given mailer, you may add the `message_stream_id` configuration option to the mailer's configuration array. This configuration array can be found in your application's `config/mail.php` configuration file:

```php	
'postmark'=> [
'transport'=>'postmark',
'message_stream_id'=>env('POSTMARK_MESSAGE_STREAM_ID'),
],
```
This way you are also able to set up multiple Postmark mailers with different message streams.

#### [SES Driver](#ses-driver)
To use the Amazon SES driver you must first install the Amazon AWS SDK for PHP. You may install this library via the Composer package manager:

```shell	
composer requireaws/aws-sdk-php
```
Next, set the `default` option in your `config/mail.php` configuration file to `ses` and verify that your `config/services.php` configuration file contains the following options:

```php	
'ses'=> [
'key'=>env('AWS_ACCESS_KEY_ID'),
'secret'=>env('AWS_SECRET_ACCESS_KEY'),
'region'=>env('AWS_DEFAULT_REGION','us-east-1'),
],
```
To utilize AWS [temporary credentials](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_use-resources.html) via a session token, you may add a `token` key to your application's SES configuration:

```php	
'ses'=> [
'key'=>env('AWS_ACCESS_KEY_ID'),
'secret'=>env('AWS_SECRET_ACCESS_KEY'),
'region'=>env('AWS_DEFAULT_REGION','us-east-1'),
'token'=>env('AWS_SESSION_TOKEN'),
],
```
If you would like to define [additional options](https://docs.aws.amazon.com/aws-sdk-php/v3/api/api-sesv2-2019-09-27.html#sendemail) that Laravel should pass to the AWS SDK's `SendEmail` method when sending an email, you may define an `options` array within your `ses` configuration:

```php	
'ses'=> [
'key'=>env('AWS_ACCESS_KEY_ID'),
'secret'=>env('AWS_SECRET_ACCESS_KEY'),
'region'=>env('AWS_DEFAULT_REGION','us-east-1'),
'options'=> [
'ConfigurationSetName'=>'MyConfigurationSet',
'EmailTags'=> [
 ['Name'=>'foo', 'Value'=>'bar'],
 ],
 ],
],
```
### [Failover Configuration](#failover-configuration)
Sometimes, an external service you have configured to send your application's mail may be down. In these cases, it can be useful to define one or more backup mail delivery configurations that will be used in case your primary delivery driver is down.

To accomplish this, you should define a mailer within your application's `mail` configuration file that uses the `failover` transport. The configuration array for your application's `failover` mailer should contain an array of `mailers` that reference the order in which mail drivers should be chosen for delivery:

```php	
'mailers'=> [
'failover'=> [
'transport'=>'failover',
'mailers'=> [
'postmark',
'mailgun',
'sendmail',
 ],
 ],
 
// ...
],
```
Once your failover mailer has been defined, you should set this mailer as the default mailer used by your application by specifying its name as the value of the `default` configuration key within your application's `mail` configuration file:

```php	
'default'=>env('MAIL_MAILER','failover'),
```
## [Generating Mailables](#generating-mailables)
When building Laravel applications, each type of email sent by your application is represented as a "mailable" class. These classes are stored in the `app/Mail` directory. Don't worry if you don't see this directory in your application, since it will be generated for you when you create your first mailable class using the `make:mail` Artisan command:

```shell	
php artisanmake:mailOrderShipped
```
## [Writing Mailables](#writing-mailables)
Once you have generated a mailable class, open it up so we can explore its contents. Mailable class configuration is done in several methods, including the `envelope`, `content`, and `attachments` methods.

The `envelope` method returns an `Illuminate\Mail\Mailables\Envelope` object that defines the subject and, sometimes, the recipients of the message. The `content` method returns an `Illuminate\Mail\Mailables\Content` object that defines the [Blade template](/docs/master/blade) that will be used to generate the message content.

### [Configuring The Sender](#configuring-the-sender)
#### [Using The Envelope](#using-the-envelope)
First, let's explore configuring the sender of the email. Or, in other words, who the email is going to be "from". There are two ways to configure the sender. First, you may specify the "from" address on your message's envelope:

```php	
use Illuminate\Mail\Mailables\Address;
use Illuminate\Mail\Mailables\Envelope;
 
/**
 * Get the message envelope.
*/
publicfunctionenvelope():Envelope
{
returnnewEnvelope(
 from: newAddress('[[email protected]](/cdn-cgi/l/email-protection)', 'Jeffrey Way'),
 subject: 'Order Shipped',
 );
}
```
If you would like, you may also specify a `replyTo` address:

```php	
returnnewEnvelope(
 from: newAddress('[[email protected]](/cdn-cgi/l/email-protection)', 'Jeffrey Way'),
 replyTo: [
newAddress('[[email protected]](/cdn-cgi/l/email-protection)', 'Taylor Otwell'),
 ],
 subject: 'Order Shipped',
);
```
#### [Using A Global `from` Address](#using-a-global-from-address)
However, if your application uses the same "from" address for all of its emails, it can become cumbersome to call the `from` method in each mailable class you generate. Instead, you may specify a global "from" address in your `config/mail.php` configuration file. This address will be used if no other "from" address is specified within the mailable class:

```php	
'from'=> ['address'=>'[[email protected]](/cdn-cgi/l/email-protection)', 'name'=>'App Name'],
```
In addition, you may define a global "reply_to" address within your `config/mail.php` configuration file:

```php	
'reply_to'=> ['address'=>'[[email protected]](/cdn-cgi/l/email-protection)', 'name'=>'App Name'],
```
### [Configuring The View](#configuring-the-view)
Within a mailable class' `content` method, you may define the `view`, or which template should be used when rendering the email's contents. Since each email typically uses a [Blade template](/docs/master/blade) to render its contents, you have the full power and convenience of the Blade templating engine when building your email's HTML:

```php	
/**
 * Get the message content definition.
*/
publicfunctioncontent():Content
{
returnnewContent(
 view: 'emails.orders.shipped',
 );
}
```
> **Note**  
>  You may wish to create a `resources/views/emails` directory to house all of your email templates; however, you are free to place them wherever you wish within your `resources/views` directory.

#### [Plain Text Emails](#plain-text-emails)
If you would like to define a plain-text version of your email, you may specify the plain-text template when creating the message's `Content` definition. Like the `view` parameter, the `text` parameter should be a template name which will be used to render the contents of the email. You are free to define both an HTML and plain-text version of your message:

```php	
/**
 * Get the message content definition.
*/
publicfunctioncontent():Content
{
returnnewContent(
 view: 'emails.orders.shipped',
 text: 'emails.orders.shipped-text'
 );
}
```
For clarity, the `html` parameter may be used as an alias of the `view` parameter:

```php	
returnnewContent(
 html: 'emails.orders.shipped',
 text: 'emails.orders.shipped-text'
);
```
### [View Data](#view-data)
#### [Via Public Properties](#via-public-properties)
Typically, you will want to pass some data to your view that you can utilize when rendering the email's HTML. There are two ways you may make data available to your view. First, any public property defined on your mailable class will automatically be made available to the view. So, for example, you may pass data into your mailable class' constructor and set that data to public properties defined on the class:

```php	
<?php
 
namespace App\Mail;
 
use App\Models\Order;
use Illuminate\Bus\Queueable;
use Illuminate\Mail\Mailable;
use Illuminate\Mail\Mailables\Content;
use Illuminate\Queue\SerializesModels;
 
classOrderShippedextendsMailable
{
useQueueable, SerializesModels;
 
/**
 * Create a new message instance.
*/
publicfunction__construct(
publicOrder$order,
) {}
 
/**
 * Get the message content definition.
*/
publicfunctioncontent():Content
 {
returnnewContent(
 view: 'emails.orders.shipped',
 );
 }
}
```
Once the data has been set to a public property, it will automatically be available in your view, so you may access it like you would access any other data in your Blade templates:

```php	
<div>
 Price: {{ $order->price }}
</div>
```
#### [Via The `with` Parameter:](#via-the-with-parameter)
If you would like to customize the format of your email's data before it is sent to the template, you may manually pass your data to the view via the `Content` definition's `with` parameter. Typically, you will still pass data via the mailable class' constructor; however, you should set this data to `protected` or `private` properties so the data is not automatically made available to the template:

```php	
<?php
 
namespace App\Mail;
 
use App\Models\Order;
use Illuminate\Bus\Queueable;
use Illuminate\Mail\Mailable;
use Illuminate\Mail\Mailables\Content;
use Illuminate\Queue\SerializesModels;
 
classOrderShippedextendsMailable
{
useQueueable, SerializesModels;
 
/**
 * Create a new message instance.
*/
publicfunction__construct(
protectedOrder$order,
) {}
 
/**
 * Get the message content definition.
*/
publicfunctioncontent():Content
 {
returnnewContent(
 view: 'emails.orders.shipped',
 with: [
'orderName'=>$this->order->name,
'orderPrice'=>$this->order->price,
 ],
 );
 }
}
```
Once the data has been passed to the `with` method, it will automatically be available in your view, so you may access it like you would access any other data in your Blade templates:

```php	
<div>
 Price: {{ $orderPrice }}
</div>
```
### [Attachments](#attachments)
To add attachments to an email, you will add attachments to the array returned by the message's `attachments` method. First, you may add an attachment by providing a file path to the `fromPath` method provided by the `Attachment` class:

```php	
use Illuminate\Mail\Mailables\Attachment;
 
/**
 * Get the attachments for the message.
 *
 * @returnarray<int, \Illuminate\Mail\Mailables\Attachment>
*/
publicfunctionattachments():array
{
return [
Attachment::fromPath('/path/to/file'),
 ];
}
```
When attaching files to a message, you may also specify the display name and / or MIME type for the attachment using the `as` and `withMime` methods:

```php	
/**
 * Get the attachments for the message.
 *
 * @returnarray<int, \Illuminate\Mail\Mailables\Attachment>
*/
publicfunctionattachments():array
{
return [
Attachment::fromPath('/path/to/file')
->as('name.pdf')
->withMime('application/pdf'),
 ];
}
```
#### [Attaching Files From Disk](#attaching-files-from-disk)
If you have stored a file on one of your [filesystem disks](/docs/master/filesystem), you may attach it to the email using the `fromStorage` attachment method:

```php	
/**
 * Get the attachments for the message.
 *
 * @returnarray<int, \Illuminate\Mail\Mailables\Attachment>
*/
publicfunctionattachments():array
{
return [
Attachment::fromStorage('/path/to/file'),
 ];
}
```
Of course, you may also specify the attachment's name and MIME type:

```php	
/**
 * Get the attachments for the message.
 *
 * @returnarray<int, \Illuminate\Mail\Mailables\Attachment>
*/
publicfunctionattachments():array
{
return [
Attachment::fromStorage('/path/to/file')
->as('name.pdf')
->withMime('application/pdf'),
 ];
}
```
The `fromStorageDisk` method may be used if you need to specify a storage disk other than your default disk:

```php	
/**
 * Get the attachments for the message.
 *
 * @returnarray<int, \Illuminate\Mail\Mailables\Attachment>
*/
publicfunctionattachments():array
{
return [
Attachment::fromStorageDisk('s3', '/path/to/file')
->as('name.pdf')
->withMime('application/pdf'),
 ];
}
```
#### [Raw Data Attachments](#raw-data-attachments)
The `fromData` attachment method may be used to attach a raw string of bytes as an attachment. For example, you might use this method if you have generated a PDF in memory and want to attach it to the email without writing it to disk. The `fromData` method accepts a closure which resolves the raw data bytes as well as the name that the attachment should be assigned:

```php	
/**
 * Get the attachments for the message.
 *
 * @returnarray<int, \Illuminate\Mail\Mailables\Attachment>
*/
publicfunctionattachments():array
{
return [
Attachment::fromData(fn() => $this->pdf, 'Report.pdf')
->withMime('application/pdf'),
 ];
}
```
### [Inline Attachments](#inline-attachments)
Embedding inline images into your emails is typically cumbersome; however, Laravel provides a convenient way to attach images to your emails. To embed an inline image, use the `embed` method on the `$message` variable within your email template. Laravel automatically makes the `$message` variable available to all of your email templates, so you don't need to worry about passing it in manually:

```blade	
<body>
 Here is an image:
 
<imgsrc="{{$message->embed($pathToImage)}}">
</body>
```
> **Warning**  
>  The `$message` variable is not available in plain-text message templates since plain-text messages do not utilize inline attachments.

#### [Embedding Raw Data Attachments](#embedding-raw-data-attachments)
If you already have a raw image data string you wish to embed into an email template, you may call the `embedData` method on the `$message` variable. When calling the `embedData` method, you will need to provide a filename that should be assigned to the embedded image:

```blade	
<body>
 Here is an image from raw data:
 
<imgsrc="{{$message->embedData($data, 'example-image.jpg')}}">
</body>
```
### [Attachable Objects](#attachable-objects)
While attaching files to messages via simple string paths is often sufficient, in many cases the attachable entities within your application are represented by classes. For example, if your application is attaching a photo to a message, your application may also have a `Photo` model that represents that photo. When that is the case, wouldn't it be convenient to simply pass the `Photo` model to the `attach` method? Attachable objects allow you to do just that.

To get started, implement the `Illuminate\Contracts\Mail\Attachable` interface on the object that will be attachable to messages. This interface dictates that your class defines a `toMailAttachment` method that returns an `Illuminate\Mail\Attachment` instance:

```php	
<?php
 
namespace App\Models;
 
use Illuminate\Contracts\Mail\Attachable;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Mail\Attachment;
 
classPhotoextendsModelimplementsAttachable
{
/**
 * Get the attachable representation of the model.
*/
publicfunctiontoMailAttachment():Attachment
 {
returnAttachment::fromPath('/path/to/file');
 }
}
```
Once you have defined your attachable object, you may return an instance of that object from the `attachments` method when building an email message:

```php	
/**
 * Get the attachments for the message.
 *
 * @returnarray<int, \Illuminate\Mail\Mailables\Attachment>
*/
publicfunctionattachments():array
{
return [$this->photo];
}
```
Of course, attachment data may be stored on a remote file storage service such as Amazon S3. So, Laravel also allows you to generate attachment instances from data that is stored on one of your application's [filesystem disks](/docs/master/filesystem):

```php	
// Create an attachment from a file on your default disk...
returnAttachment::fromStorage($this->path);
 
// Create an attachment from a file on a specific disk...
returnAttachment::fromStorageDisk('backblaze', $this->path);
```
In addition, you may create attachment instances via data that you have in memory. To accomplish this, provide a closure to the `fromData` method. The closure should return the raw data that represents the attachment:

```php	
returnAttachment::fromData(fn() => $this->content, 'Photo Name');
```
Laravel also provides additional methods that you may use to customize your attachments. For example, you may use the `as` and `withMime` methods to customize the file's name and MIME type:

```php	
returnAttachment::fromPath('/path/to/file')
->as('Photo Name')
->withMime('image/jpeg');
```
### [Headers](#headers)
Sometimes you may need to attach additional headers to the outgoing message. For instance, you may need to set a custom `Message-Id` or other arbitrary text headers.

To accomplish this, define a `headers` method on your mailable. The `headers` method should return an `Illuminate\Mail\Mailables\Headers` instance. This class accepts `messageId`, `references`, and `text` parameters. Of course, you may provide only the parameters you need for your particular message:

```php	
use Illuminate\Mail\Mailables\Headers;
 
/**
 * Get the message headers.
*/
publicfunctionheaders():Headers
{
returnnewHeaders(
 messageId: '[[email protected]](/cdn-cgi/l/email-protection)',
 references: ['[[email protected]](/cdn-cgi/l/email-protection)'],
 text: [
'X-Custom-Header'=>'Custom Value',
 ],
 );
}
```
### [Tags & Metadata](#tags-and-metadata)
Some third-party email providers such as Mailgun and Postmark support message "tags" and "metadata", which may be used to group and track emails sent by your application. You may add tags and metadata to an email message via your `Envelope` definition:

```php	
use Illuminate\Mail\Mailables\Envelope;
 
/**
 * Get the message envelope.
 *
 * @return\Illuminate\Mail\Mailables\Envelope
*/
publicfunctionenvelope():Envelope
{
returnnewEnvelope(
 subject: 'Order Shipped',
 tags: ['shipment'],
 metadata: [
'order_id'=>$this->order->id,
 ],
 );
}
```
If your application is using the Mailgun driver, you may consult Mailgun's documentation for more information on [tags](https://documentation.mailgun.com/en/latest/user_manual.html#tagging-1) and [metadata](https://documentation.mailgun.com/en/latest/user_manual.html#attaching-data-to-messages). Likewise, the Postmark documentation may also be consulted for more information on their support for [tags](https://postmarkapp.com/blog/tags-support-for-smtp) and [metadata](https://postmarkapp.com/support/article/1125-custom-metadata-faq).

If your application is using Amazon SES to send emails, you should use the `metadata` method to attach [SES "tags"](https://docs.aws.amazon.com/ses/latest/APIReference/API_MessageTag.html) to the message.

### [Customizing The Symfony Message](#customizing-the-symfony-message)
Laravel's mail capabilities are powered by Symfony Mailer. Laravel allows you to register custom callbacks that will be invoked with the Symfony Message instance before sending the message. This gives you an opportunity to deeply customize the message before it is sent. To accomplish this, define a `using` parameter on your `Envelope` definition:

```php	
use Illuminate\Mail\Mailables\Envelope;
use Symfony\Component\Mime\Email;
 
/**
 * Get the message envelope.
*/
publicfunctionenvelope():Envelope
{
returnnewEnvelope(
 subject: 'Order Shipped',
 using: [
function(Email$message) {
// ...
 },
 ]
 );
}
```
## [Markdown Mailables](#markdown-mailables)
Markdown mailable messages allow you to take advantage of the pre-built templates and components of [mail notifications](/docs/master/notifications#mail-notifications) in your mailables. Since the messages are written in Markdown, Laravel is able to render beautiful, responsive HTML templates for the messages while also automatically generating a plain-text counterpart.

### [Generating Markdown Mailables](#generating-markdown-mailables)
To generate a mailable with a corresponding Markdown template, you may use the `--markdown` option of the `make:mail` Artisan command:

```shell	
php artisanmake:mailOrderShipped--markdown=emails.orders.shipped
```
Then, when configuring the mailable `Content` definition within its `content` method, use the `markdown` parameter instead of the `view` parameter:

```php	
use Illuminate\Mail\Mailables\Content;
 
/**
 * Get the message content definition.
*/
publicfunctioncontent():Content
{
returnnewContent(
 markdown: 'emails.orders.shipped',
 with: [
'url'=>$this->orderUrl,
 ],
 );
}
```
### [Writing Markdown Messages](#writing-markdown-messages)
Markdown mailables use a combination of Blade components and Markdown syntax which allow you to easily construct mail messages while leveraging Laravel's pre-built email UI components:

```blade	
<x-mail::message>
# Order Shipped
 
Your order has been shipped!
 
<x-mail::button:url="$url">
View Order
</x-mail::button>
 
Thanks,<br>
{{config('app.name') }}
</x-mail::message>
```
> **Note**  
>  Do not use excess indentation when writing Markdown emails. Per Markdown standards, Markdown parsers will render indented content as code blocks.

#### [Button Component](#button-component)
The button component renders a centered button link. The component accepts two arguments, a `url` and an optional `color`. Supported colors are `primary`, `success`, and `error`. You may add as many button components to a message as you wish:

```blade	
<x-mail::button:url="$url"color="success">
View Order
</x-mail::button>
```
#### [Panel Component](#panel-component)
The panel component renders the given block of text in a panel that has a slightly different background color than the rest of the message. This allows you to draw attention to a given block of text:

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
You may export all of the Markdown mail components to your own application for customization. To export the components, use the `vendor:publish` Artisan command to publish the `laravel-mail` asset tag:

```shell	
php artisanvendor:publish--tag=laravel-mail
```
This command will publish the Markdown mail components to the `resources/views/vendor/mail` directory. The `mail` directory will contain an `html` and a `text` directory, each containing their respective representations of every available component. You are free to customize these components however you like.

#### [Customizing The CSS](#customizing-the-css)
After exporting the components, the `resources/views/vendor/mail/html/themes` directory will contain a `default.css` file. You may customize the CSS in this file and your styles will automatically be converted to inline CSS styles within the HTML representations of your Markdown mail messages.

If you would like to build an entirely new theme for Laravel's Markdown components, you may place a CSS file within the `html/themes` directory. After naming and saving your CSS file, update the `theme` option of your application's `config/mail.php` configuration file to match the name of your new theme.

To customize the theme for an individual mailable, you may set the `$theme` property of the mailable class to the name of the theme that should be used when sending that mailable.

## [Sending Mail](#sending-mail)
To send a message, use the `to` method on the `Mail`[facade](/docs/master/facades). The `to` method accepts an email address, a user instance, or a collection of users. If you pass an object or collection of objects, the mailer will automatically use their `email` and `name` properties when determining the email's recipients, so make sure these attributes are available on your objects. Once you have specified your recipients, you may pass an instance of your mailable class to the `send` method:

```php	
<?php
 
namespace App\Http\Controllers;
 
use App\Http\Controllers\Controller;
use App\Mail\OrderShipped;
use App\Models\Order;
use Illuminate\Http\Request;
use Illuminate\Http\Response;
use Illuminate\Support\Facades\Mail;
 
classOrderShipmentControllerextendsController
{
/**
 * Ship the given order.
*/
publicfunctionstore(Request$request):Response
 {
$order=Order::findOrFail($request->order_id);
 
// Ship the order...
 
Mail::to($request->user())->send(newOrderShipped($order));
 
returnresponse()->noContent();
 }
}
```
You are not limited to just specifying the "to" recipients when sending a message. You are free to set "to", "cc", and "bcc" recipients by chaining their respective methods together:

```php	
Mail::to($request->user())
->cc($moreUsers)
->bcc($evenMoreUsers)
->send(newOrderShipped($order));
```
#### [Looping Over Recipients](#looping-over-recipients)
Occasionally, you may need to send a mailable to a list of recipients by iterating over an array of recipients / email addresses. However, since the `to` method appends email addresses to the mailable's list of recipients, each iteration through the loop will send another email to every previous recipient. Therefore, you should always re-create the mailable instance for each recipient:

```php	
foreach (['[[email protected]](/cdn-cgi/l/email-protection)', '[[email protected]](/cdn-cgi/l/email-protection)'] as$recipient) {
Mail::to($recipient)->send(newOrderShipped($order));
}
```
#### [Sending Mail Via A Specific Mailer](#sending-mail-via-a-specific-mailer)
By default, Laravel will send email using the mailer configured as the `default` mailer in your application's `mail` configuration file. However, you may use the `mailer` method to send a message using a specific mailer configuration:

```php	
Mail::mailer('postmark')
->to($request->user())
->send(newOrderShipped($order));
```
### [Queueing Mail](#queueing-mail)
#### [Queueing A Mail Message](#queueing-a-mail-message)
Since sending email messages can negatively impact the response time of your application, many developers choose to queue email messages for background sending. Laravel makes this easy using its built-in [unified queue API](/docs/master/queues). To queue a mail message, use the `queue` method on the `Mail` facade after specifying the message's recipients:

```php	
Mail::to($request->user())
->cc($moreUsers)
->bcc($evenMoreUsers)
->queue(newOrderShipped($order));
```
This method will automatically take care of pushing a job onto the queue so the message is sent in the background. You will need to [configure your queues](/docs/master/queues) before using this feature.

#### [Delayed Message Queueing](#delayed-message-queueing)
If you wish to delay the delivery of a queued email message, you may use the `later` method. As its first argument, the `later` method accepts a `DateTime` instance indicating when the message should be sent:

```php	
Mail::to($request->user())
->cc($moreUsers)
->bcc($evenMoreUsers)
->later(now()->addMinutes(10), newOrderShipped($order));
```
#### [Pushing To Specific Queues](#pushing-to-specific-queues)
Since all mailable classes generated using the `make:mail` command make use of the `Illuminate\Bus\Queueable` trait, you may call the `onQueue` and `onConnection` methods on any mailable class instance, allowing you to specify the connection and queue name for the message:

```php	
$message= (newOrderShipped($order))
->onConnection('sqs')
->onQueue('emails');
 
Mail::to($request->user())
->cc($moreUsers)
->bcc($evenMoreUsers)
->queue($message);
```
#### [Queueing By Default](#queueing-by-default)
If you have mailable classes that you want to always be queued, you may implement the `ShouldQueue` contract on the class. Now, even if you call the `send` method when mailing, the mailable will still be queued since it implements the contract:

```php	
use Illuminate\Contracts\Queue\ShouldQueue;
 
classOrderShippedextendsMailableimplementsShouldQueue
{
// ...
}
```
#### [Queued Mailables & Database Transactions](#queued-mailables-and-database-transactions)
When queued mailables are dispatched within database transactions, they may be processed by the queue before the database transaction has committed. When this happens, any updates you have made to models or database records during the database transaction may not yet be reflected in the database. In addition, any models or database records created within the transaction may not exist in the database. If your mailable depends on these models, unexpected errors can occur when the job that sends the queued mailable is processed.

If your queue connection's `after_commit` configuration option is set to `false`, you may still indicate that a particular queued mailable should be dispatched after all open database transactions have been committed by calling the `afterCommit` method when sending the mail message:

```php	
Mail::to($request->user())->send(
 (newOrderShipped($order))->afterCommit()
);
```
Alternatively, you may call the `afterCommit` method from your mailable's constructor:

```php	
<?php
 
namespace App\Mail;
 
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Mail\Mailable;
use Illuminate\Queue\SerializesModels;
 
classOrderShippedextendsMailableimplementsShouldQueue
{
useQueueable, SerializesModels;
 
/**
 * Create a new message instance.
*/
publicfunction__construct()
 {
$this->afterCommit();
 }
}
```
> **Note**  
>  To learn more about working around these issues, please review the documentation regarding [queued jobs and database transactions](/docs/master/queues#jobs-and-database-transactions).

## [Rendering Mailables](#rendering-mailables)
Sometimes you may wish to capture the HTML content of a mailable without sending it. To accomplish this, you may call the `render` method of the mailable. This method will return the evaluated HTML content of the mailable as a string:

```php	
use App\Mail\InvoicePaid;
use App\Models\Invoice;
 
$invoice=Invoice::find(1);
 
return (newInvoicePaid($invoice))->render();
```
### [Previewing Mailables In The Browser](#previewing-mailables-in-the-browser)
When designing a mailable's template, it is convenient to quickly preview the rendered mailable in your browser like a typical Blade template. For this reason, Laravel allows you to return any mailable directly from a route closure or controller. When a mailable is returned, it will be rendered and displayed in the browser, allowing you to quickly preview its design without needing to send it to an actual email address:

```php	
Route::get('/mailable', function() {
$invoice= App\Models\Invoice::find(1);
 
returnnew App\Mail\InvoicePaid($invoice);
});
```
> **Warning**  
> [Inline attachments](#inline-attachments) will not be rendered when a mailable is previewed in your browser. To preview these mailables, you should send them to an email testing application such as [Mailpit](https://github.com/axllent/mailpit) or [HELO](https://usehelo.com).

## [Localizing Mailables](#localizing-mailables)
Laravel allows you to send mailables in a locale other than the request's current locale, and will even remember this locale if the mail is queued.

To accomplish this, the `Mail` facade offers a `locale` method to set the desired language. The application will change into this locale when the mailable's template is being evaluated and then revert back to the previous locale when evaluation is complete:

```php	
Mail::to($request->user())->locale('es')->send(
newOrderShipped($order)
);
```
### [User Preferred Locales](#user-preferred-locales)
Sometimes, applications store each user's preferred locale. By implementing the `HasLocalePreference` contract on one or more of your models, you may instruct Laravel to use this stored locale when sending mail:

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
Once you have implemented the interface, Laravel will automatically use the preferred locale when sending mailables and notifications to the model. Therefore, there is no need to call the `locale` method when using this interface:

```php	
Mail::to($request->user())->send(newOrderShipped($order));
```
## [Testing](#testing-mailables)
### [Testing Mailable Content](#testing-mailable-content)
Laravel provides a variety of methods for inspecting your mailable's structure. In addition, Laravel provides several convenient methods for testing that your mailable contains the content that you expect. These methods are: `assertSeeInHtml`, `assertDontSeeInHtml`, `assertSeeInOrderInHtml`, `assertSeeInText`, `assertDontSeeInText`, `assertSeeInOrderInText`, `assertHasAttachment`, `assertHasAttachedData`, `assertHasAttachmentFromStorage`, and `assertHasAttachmentFromStorageDisk`.

As you might expect, the "HTML" assertions assert that the HTML version of your mailable contains a given string, while the "text" assertions assert that the plain-text version of your mailable contains a given string:

```php	
use App\Mail\InvoicePaid;
use App\Models\User;
 
publicfunctiontest_mailable_content():void
{
$user=User::factory()->create();
 
$mailable=newInvoicePaid($user);
 
$mailable->assertFrom('[[email protected]](/cdn-cgi/l/email-protection)');
$mailable->assertTo('[[email protected]](/cdn-cgi/l/email-protection)');
$mailable->assertHasCc('[[email protected]](/cdn-cgi/l/email-protection)');
$mailable->assertHasBcc('[[email protected]](/cdn-cgi/l/email-protection)');
$mailable->assertHasReplyTo('[[email protected]](/cdn-cgi/l/email-protection)');
$mailable->assertHasSubject('Invoice Paid');
$mailable->assertHasTag('example-tag');
$mailable->assertHasMetadata('key', 'value');
 
$mailable->assertSeeInHtml($user->email);
$mailable->assertSeeInHtml('Invoice Paid');
$mailable->assertSeeInOrderInHtml(['Invoice Paid', 'Thanks']);
 
$mailable->assertSeeInText($user->email);
$mailable->assertSeeInOrderInText(['Invoice Paid', 'Thanks']);
 
$mailable->assertHasAttachment('/path/to/file');
$mailable->assertHasAttachment(Attachment::fromPath('/path/to/file'));
$mailable->assertHasAttachedData($pdfData, 'name.pdf', ['mime'=>'application/pdf']);
$mailable->assertHasAttachmentFromStorage('/path/to/file', 'name.pdf', ['mime'=>'application/pdf']);
$mailable->assertHasAttachmentFromStorageDisk('s3', '/path/to/file', 'name.pdf', ['mime'=>'application/pdf']);
}
```
### [Testing Mailable Sending](#testing-mailable-sending)
We suggest testing the content of your mailables separately from your tests that assert that a given mailable was "sent" to a specific user. Typically, the content of mailables it not relevant to the code you are testing, and it is sufficient to simply assert that Laravel was instructed to send a given mailable.

You may use the `Mail` facade's `fake` method to prevent mail from being sent. After calling the `Mail` facade's `fake` method, you may then assert that mailables were instructed to be sent to users and even inspect the data the mailables received:

```php	
<?php
 
namespace Tests\Feature;
 
use App\Mail\OrderShipped;
use Illuminate\Support\Facades\Mail;
use Tests\TestCase;
 
classExampleTestextendsTestCase
{
publicfunctiontest_orders_can_be_shipped():void
 {
Mail::fake();
 
// Perform order shipping...
 
// Assert that no mailables were sent...
Mail::assertNothingSent();
 
// Assert that a mailable was sent...
Mail::assertSent(OrderShipped::class);
 
// Assert a mailable was sent twice...
Mail::assertSent(OrderShipped::class, 2);
 
// Assert a mailable was not sent...
Mail::assertNotSent(AnotherMailable::class);
 }
}
```
If you are queueing mailables for delivery in the background, you should use the `assertQueued` method instead of `assertSent`:

```php	
Mail::assertQueued(OrderShipped::class);
 
Mail::assertNotQueued(OrderShipped::class);
 
Mail::assertNothingQueued();
```
You may pass a closure to the `assertSent`, `assertNotSent`, `assertQueued`, or `assertNotQueued` methods in order to assert that a mailable was sent that passes a given "truth test". If at least one mailable was sent that passes the given truth test then the assertion will be successful:

```php	
Mail::assertSent(function(OrderShipped$mail)use($order) {
return$mail->order->id===$order->id;
});
```
When calling the `Mail` facade's assertion methods, the mailable instance accepted by the provided closure exposes helpful methods for examining the mailable:

```php	
Mail::assertSent(OrderShipped::class, function(OrderShipped$mail)use($user) {
return$mail->hasTo($user->email) &&
$mail->hasCc('...') &&
$mail->hasBcc('...') &&
$mail->hasReplyTo('...') &&
$mail->hasFrom('...') &&
$mail->hasSubject('...');
});
```
The mailable instance also includes several helpful methods for examining the attachments on a mailable:

```php	
use Illuminate\Mail\Mailables\Attachment;
 
Mail::assertSent(OrderShipped::class, function(OrderShipped$mail) {
return$mail->hasAttachment(
Attachment::fromPath('/path/to/file')
->as('name.pdf')
->withMime('application/pdf')
 );
});
 
Mail::assertSent(OrderShipped::class, function(OrderShipped$mail) {
return$mail->hasAttachment(
Attachment::fromStorageDisk('s3', '/path/to/file')
 );
});
 
Mail::assertSent(OrderShipped::class, function(OrderShipped$mail)use($pdfData) {
return$mail->hasAttachment(
Attachment::fromData(fn() => $pdfData, 'name.pdf')
 );
});
```
You may have noticed that there are two methods for asserting that mail was not sent: `assertNotSent` and `assertNotQueued`. Sometimes you may wish to assert that no mail was sent **or** queued. To accomplish this, you may use the `assertNothingOutgoing` and `assertNotOutgoing` methods:

```php	
Mail::assertNothingOutgoing();
 
Mail::assertNotOutgoing(function(OrderShipped$mail)use($order) {
return$mail->order->id===$order->id;
});
```
## [Mail & Local Development](#mail-and-local-development)
When developing an application that sends email, you probably don't want to actually send emails to live email addresses. Laravel provides several ways to "disable" the actual sending of emails during local development.

#### [Log Driver](#log-driver)
Instead of sending your emails, the `log` mail driver will write all email messages to your log files for inspection. Typically, this driver would only be used during local development. For more information on configuring your application per environment, check out the [configuration documentation](/docs/master/configuration#environment-configuration).

#### [HELO / Mailtrap / Mailpit](#mailtrap)
Alternatively, you may use a service like [HELO](https://usehelo.com) or [Mailtrap](https://mailtrap.io) and the `smtp` driver to send your email messages to a "dummy" mailbox where you may view them in a true email client. This approach has the benefit of allowing you to actually inspect the final emails in Mailtrap's message viewer.

If you are using [Laravel Sail](/docs/master/sail), you may preview your messages using [Mailpit](https://github.com/axllent/mailpit). When Sail is running, you may access the Mailpit interface at: `http://localhost:8025`.

#### [Using A Global `to` Address](#using-a-global-to-address)
Finally, you may specify a global "to" address by invoking the `alwaysTo` method offered by the `Mail` facade. Typically, this method should be called from the `boot` method of one of your application's service providers:

```php	
use Illuminate\Support\Facades\Mail;
 
/**
 * Bootstrap any application services.
*/
publicfunctionboot():void
{
if ($this->app->environment('local')) {
Mail::alwaysTo('[[email protected]](/cdn-cgi/l/email-protection)');
 }
}
```
## [Events](#events)
Laravel fires two events during the process of sending mail messages. The `MessageSending` event is fired prior to a message being sent, while the `MessageSent` event is fired after a message has been sent. Remember, these events are fired when the mail is being *sent*, not when it is queued. You may register event listeners for this event in your `App\Providers\EventServiceProvider` service provider:

```php	
use App\Listeners\LogSendingMessage;
use App\Listeners\LogSentMessage;
use Illuminate\Mail\Events\MessageSending;
use Illuminate\Mail\Events\MessageSent;
 
/**
 * The event listener mappings for the application.
 *
 * @vararray
*/
protected$listen= [
MessageSending::class=> [
LogSendingMessage::class,
 ],
 
MessageSent::class=> [
LogSentMessage::class,
 ],
];
```
## [Custom Transports](#custom-transports)
Laravel includes a variety of mail transports; however, you may wish to write your own transports to deliver email via other services that Laravel does not support out of the box. To get started, define a class that extends the `Symfony\Component\Mailer\Transport\AbstractTransport` class. Then, implement the `doSend` and `__toString()` methods on your transport:

```php	
use MailchimpTransactional\ApiClient;
use Symfony\Component\Mailer\SentMessage;
use Symfony\Component\Mailer\Transport\AbstractTransport;
use Symfony\Component\Mime\Address;
use Symfony\Component\Mime\MessageConverter;
 
classMailchimpTransportextendsAbstractTransport
{
/**
 * Create a new Mailchimp transport instance.
*/
publicfunction__construct(
protectedApiClient$client,
) {
parent::__construct();
 }
 
/**
 * {@inheritDoc}
*/
protectedfunctiondoSend(SentMessage$message):void
 {
$email=MessageConverter::toEmail($message->getOriginalMessage());
 
$this->client->messages->send(['message'=> [
'from_email'=>$email->getFrom(),
'to'=>collect($email->getTo())->map(function(Address$email) {
return ['email'=>$email->getAddress(), 'type'=>'to'];
 })->all(),
'subject'=>$email->getSubject(),
'text'=>$email->getTextBody(),
 ]]);
 }
 
/**
 * Get the string representation of the transport.
*/
publicfunction__toString():string
 {
return'mailchimp';
 }
}
```
Once you've defined your custom transport, you may register it via the `extend` method provided by the `Mail` facade. Typically, this should be done within the `boot` method of your application's `AppServiceProvider` service provider. A `$config` argument will be passed to the closure provided to the `extend` method. This argument will contain the configuration array defined for the mailer in the application's `config/mail.php` configuration file:

```php	
use App\Mail\MailchimpTransport;
use Illuminate\Support\Facades\Mail;
 
/**
 * Bootstrap any application services.
*/
publicfunctionboot():void
{
Mail::extend('mailchimp', function(array$config= []) {
returnnewMailchimpTransport(/* ... */);
 });
}
```
Once your custom transport has been defined and registered, you may create a mailer definition within your application's `config/mail.php` configuration file that utilizes the new transport:

```php	
'mailchimp'=> [
'transport'=>'mailchimp',
// ...
],
```
### [Additional Symfony Transports](#additional-symfony-transports)
Laravel includes support for some existing Symfony maintained mail transports like Mailgun and Postmark. However, you may wish to extend Laravel with support for additional Symfony maintained transports. You can do so by requiring the necessary Symfony mailer via Composer and registering the transport with Laravel. For example, you may install and register the "Sendinblue" Symfony mailer:

```none	
composer require symfony/sendinblue-mailer symfony/http-client
```
Once the Sendinblue mailer package has been installed, you may add an entry for your Sendinblue API credentials to your application's `services` configuration file:

```php	
'sendinblue'=> [
'key'=>'your-api-key',
],
```
Next, you may use the `Mail` facade's `extend` method to register the transport with Laravel. Typically, this should be done within the `boot` method of a service provider:

```php	
use Illuminate\Support\Facades\Mail;
use Symfony\Component\Mailer\Bridge\Sendinblue\Transport\SendinblueTransportFactory;
use Symfony\Component\Mailer\Transport\Dsn;
 
/**
 * Bootstrap any application services.
*/
publicfunctionboot():void
{
Mail::extend('sendinblue', function() {
return (newSendinblueTransportFactory)->create(
newDsn(
'sendinblue+api',
'default',
config('services.sendinblue.key')
 )
 );
 });
}
```
Once your transport has been registered, you may create a mailer definition within your application's config/mail.php configuration file that utilizes the new transport:

```php	
'sendinblue'=> [
'transport'=>'sendinblue',
// ...
],
```
