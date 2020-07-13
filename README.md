# MailerLite API v2 PHP SDK

It is an official PHP SDK for MailerLite API v2.

You can find more examples and information about MailerLite API v2 here: [http://developers.mailerlite.com](https://developers.mailerlite.com)

## Getting started

In order to use this library you need to have at least PHP 5.6 version.

There are two ways to use MailerLite PHP SDK:

##### Use [Composer](https://getcomposer.org/)

If you are not familiar with Composer, learn about it [here](https://getcomposer.org/doc/01-basic-usage.md).

Then you will need to run this simple command using CLI:

```
composer require mailerlite/mailerlite-api-v2-php-sdk
```

## Usage examples

#### Groups API

In the given example you will see how to initiate selected API and a few actions which are available:

- Create group
- Get groups
- Update group
- Get subscribers who belongs to selected group

```php
$groupsApi = (new \MailerLiteApi\MailerLite('your-api-key'))->groups();

$newGroup = $groupsApi->create(['name' => 'New group']); // creates group and returns it

$allGroups = $groupsApi->get(); // returns array of groups

$groupId = 123;
$singleGroup = $groupsApi->find($groupId); // returns single item object

$subscribers = $groupsApi->getSubscribers($groupId); // get subscribers who belongs to selected group

$subscribers = $groupsApi->getSubscribers($groupId, 'unsubscribed'); // get unsubscribed subscribers who belongs to selected group
```

#### Simple example of Subscribers API usage (adding single person to newsletter list)

```php
<?php

// array holding allowed Origin domains
$allowedOrigins = array(
  '(http(s)://)?(www\.)?yourdomain\.com',
  'localhost'
);
 
if (isset($_SERVER['HTTP_ORIGIN']) && $_SERVER['HTTP_ORIGIN'] != '') {
  foreach ($allowedOrigins as $allowedOrigin) {
    if (preg_match('#' . $allowedOrigin . '#', $_SERVER['HTTP_ORIGIN'])) {
      header('Access-Control-Allow-Origin: ' . $_SERVER['HTTP_ORIGIN']);
      header('Content-Type: application/json; charset=utf-8');
      break;
    }
  }
}

function returnMessage($code, $message) {
  echo json_encode([
    'code' => $code,
    'message' => $message,
  ]);
}

try
{
  require 'vendor/autoload.php';
	$subscribersApi = (new \MailerLiteApi\MailerLite('your-api-key'))->subscribers();

  $data = json_decode(file_get_contents('php://input'), true);
  $email = $data['email'];

  $subscriber = [
    'email' => $email
  ];

  $addedSubscriber = $subscribersApi->create($subscriber);

  if($addedSubscriber->email) {
    returnMessage(200, 'Thank you for signing to our newsletter!');
  } else {
    returnMessage($addedSubscriber->status, 'There was an error' . $addedSubscriber->error->message);
  }
}
catch (Exception $e)
{
  returnMessage($e->code, 'There was an error while adding to a newsletter: ' . $e->getMessage());
}
```

#### Use multiple APIs at once

Also `MailerLiteApi\MailerLite' object can be initiated before selecting API you want to use and it allows to achieve more.

```php
$mailerliteClient = new \MailerLiteApi\MailerLite('your-api-key');

$groupsApi = $mailerliteClient->groups();
$groups = $groupsApi->get(); // returns array of groups

$fieldsApi = $mailerliteClient->fields();
$fields = $fieldsApi->get(); // returns array of fields
```

## Use your preferred HTTP client

MailerLite SDK uses cURL as default HTTP client but it is easy to use your preferred client. It is achieved by using [HTTPlug](https://httplug.io) which is PSR-7 compliant HTTP client abstraction.

Here is an example how to use [Guzzle](https://docs.guzzlephp.org/) instead of cURL:

```php
$guzzle = new \GuzzleHttp\Client();
$guzzleClient = new \Http\Adapter\Guzzle6\Client($guzzle);

$mailerliteClient = new \MailerLiteApi\Mailerlite('your-api-key', $guzzleClient);
```

## Support and Feedback

In case you find any bugs, submit an issue directly here in GitHub.

You are welcome to create SDK for any other programming language.

If you have any troubles using our API or SDK free to contact our support by email [info@mailerlite.com](mailto:info@mailerlite)

Official documentation is at [https://developers.mailerlite.com](https://developers.mailerlite.com)
