# Plaid PHP SDK

A PHP package to help kickstart your next [Plaid](https://plaid.com) integration.

## Introduction

The Plaid PHP SDK is a PHP package that provides a simple and easy-to-use interface for interacting with the Plaid API.

## Table of Contents

- [Installation](#installation)
- [Getting Started](#getting-started)
  - [Versioning](#versioning)
- [Error Handling](#error-handling)
- [Promise Support](#promise-support)
- [Products Supported](#products-supported)

## Installation

You can install the package using [Composer](https://getcomposer.org):

```shell
composer require xpendy-dev/plaid-php-sdk
```

## Getting Started

To get started, a new instance of the `Plaid` client should be created. You can then use the instance to interact with the Plaid API. In the following example, we request a public link token that can then be used to initialize a `Link` session.

```php
<?php

use Plaid\Environment;
use Plaid\Plaid;

// Create the Plaid client
$plaid = new Plaid(
    clientId: env('PLAID_CLIENT_ID'),
    clientSecret: env('PLAID_SECRET'),
    environment: Environment::SANDBOX
);

// Request a public link token
$token = $plaid
    ->link()
    ->publicTokenCreate([
        "client_name" => env('APP_NAME'),
        "products" => ["auth"],
        "country_codes" => ["US"],
        "language" => "en",
        "user" => [
          "client_user_id" => "1"
        ]
      ])
      ->json('link_token');
```

### Versioning

A specific Plaid API version can be used by adding a `Plaid-Version` header to the request. The following example demonstrates how to request a specific version of the `Auth` endpoint.

```php
<?php

use Plaid\Environment;
use Plaid\Exceptions\PlaidException;
use Plaid\Http\Requests\Auth\GetRequest;
use Plaid\Plaid;

// Create the Plaid client
$plaid = new Plaid(
    clientId: env('PLAID_CLIENT_ID'),
    clientSecret: env('PLAID_SECRET'),
    accessToken: $accessToken,
    environment: Environment::SANDBOX
);

// Create a request
$request = new GetRequest();
$request->headers()->add('Plaid-Version', '2020-09-14');

// Send the request
$data = $plaid->send($request)->json();
```

## Error Handling

The Plaid PHP SDK throws exceptions when an API error occurs. You can catch these exceptions and handle them accordingly with a standard `try/catch` block. All errors thrown by the SDK extend the `PlaidException` class. For a more elegant approach to error handling, consider using the [promise-based](#promise-support) approach.

```php
<?php

use Plaid\Environment;
use Plaid\Exceptions\PlaidException;
use Plaid\Plaid;

// Create the Plaid client
$plaid = new Plaid(
    clientId: env('PLAID_CLIENT_ID'),
    clientSecret: env('PLAID_SECRET'),
    environment: Environment::SANDBOX
);

// Perform a request
try {
    $transactions = $plaid->transactions([
        'start_date' => '2021-01-01',
        'end_date' => '2021-01-31'
    ])->get()->json();
} catch (PlaidException $exception) {
    // Handle API exceptions
} catch (Exception $e) {
    // Handle PHP exceptions
}
```

## Promise Support

The Plaid PHP SDK can send asynchronous requests using a promise-based approach. This allows you to handle both successful and failed requests in a more elegant way.

```php
<?php

use Plaid\Environment;
use Plaid\Http\Requests\Auth\GetRequest;
use Plaid\Plaid;
use Saloon\Exceptions\Request\RequestException;
use Saloon\Http\Response;

// Create the Plaid client
$plaid = new Plaid(
    clientId: env('PLAID_CLIENT_ID'),
    clientSecret: env('PLAID_SECRET'),
    accessToken: $accessToken,
    environment: Environment::SANDBOX
);

// Create a promise
$promise = $plaid->sendAsync(new GetRequest());

// Send the request
$promise
    ->then(function (Response $response) {
        // Handle successful response
    })
    ->otherwise(function (RequestException $exception) {
        // Handle failed request
    });

// Force the promise to be resolved
$promise->wait();
```

## Products Supported

Currently, the following Plaid products are supported:

- Auth
- Identity
- Link
- Sandbox
- Transactions
- Transfer

## Contributing

Please see [here](../.github/CONTRIBUTING.md) for more details about contributing.
