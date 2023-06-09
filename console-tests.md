# Console Tests
- [Introduction](#introduction)
- [Success / Failure Expectations](#success-failure-expectations)
- [Input / Output Expectations](#input-output-expectations)

## [Introduction](#introduction)
In addition to simplifying HTTP testing, Laravel provides a simple API for testing your application's [custom console commands](/docs/master/artisan).

## [Success / Failure Expectations](#success-failure-expectations)
To get started, let's explore how to make assertions regarding an Artisan command's exit code. To accomplish this, we will use the `artisan` method to invoke an Artisan command from our test. Then, we will use the `assertExitCode` method to assert that the command completed with a given exit code:

```php	
/**
 * Test a console command.
*/
publicfunctiontest_console_command():void
{
$this->artisan('inspire')->assertExitCode(0);
}
```
You may use the `assertNotExitCode` method to assert that the command did not exit with a given exit code:

```php	
$this->artisan('inspire')->assertNotExitCode(1);
```
Of course, all terminal commands typically exit with a status code of `0` when they are successful and a non-zero exit code when they are not successful. Therefore, for convenience, you may utilize the `assertSuccessful` and `assertFailed` assertions to assert that a given command exited with a successful exit code or not:

```php	
$this->artisan('inspire')->assertSuccessful();
 
$this->artisan('inspire')->assertFailed();
```
## [Input / Output Expectations](#input-output-expectations)
Laravel allows you to easily "mock" user input for your console commands using the `expectsQuestion` method. In addition, you may specify the exit code and text that you expect to be output by the console command using the `assertExitCode` and `expectsOutput` methods. For example, consider the following console command:

```php	
Artisan::command('question', function() {
$name=$this->ask('What is your name?');
 
$language=$this->choice('Which language do you prefer?', [
'PHP',
'Ruby',
'Python',
 ]);
 
$this->line('Your name is '.$name.' and you prefer '.$language.'.');
});
```
You may test this command with the following test which utilizes the `expectsQuestion`, `expectsOutput`, `doesntExpectOutput`, `expectsOutputToContain`, `doesntExpectOutputToContain`, and `assertExitCode` methods:

```php	
/**
 * Test a console command.
*/
publicfunctiontest_console_command():void
{
$this->artisan('question')
->expectsQuestion('What is your name?', 'Taylor Otwell')
->expectsQuestion('Which language do you prefer?', 'PHP')
->expectsOutput('Your name is Taylor Otwell and you prefer PHP.')
->doesntExpectOutput('Your name is Taylor Otwell and you prefer Ruby.')
->expectsOutputToContain('Taylor Otwell')
->doesntExpectOutputToContain('you prefer Ruby')
->assertExitCode(0);
}
```
#### [Confirmation Expectations](#confirmation-expectations)
When writing a command which expects confirmation in the form of a "yes" or "no" answer, you may utilize the `expectsConfirmation` method:

```php	
$this->artisan('module:import')
->expectsConfirmation('Do you really wish to run this command?', 'no')
->assertExitCode(1);
```
#### [Table Expectations](#table-expectations)
If your command displays a table of information using Artisan's `table` method, it can be cumbersome to write output expectations for the entire table. Instead, you may use the `expectsTable` method. This method accepts the table's headers as its first argument and the table's data as its second argument:

```php	
$this->artisan('users:all')
->expectsTable([
'ID',
'Email',
 ], [
 [1, '[[email protected]](/cdn-cgi/l/email-protection)'],
 [2, '[[email protected]](/cdn-cgi/l/email-protection)'],
 ]);
```
