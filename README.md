# Laravel API Response

![Build Status](https://travis-ci.org/mdhesari/api-response.svg?branch=master)
[![StyleCI](https://github.styleci.io/repos/206981157/shield?branch=master)](https://github.styleci.io/repos/206981157)
![Packagist](https://img.shields.io/packagist/l/mdhesari/api-response) ![Packagist Version](https://img.shields.io/packagist/v/mdhesari/api-response)

Simple Laravel API response wrapper.

---
![API response code sample](https://i.ibb.co/S5bLwKc/carbon.png)
---

## Installation
1. Install the package through composer:

    `$ composer require mdhesari/api-response`

2. Register the package service provider to the providers array in `app.php` file:

    `Obiefy\API\ApiResponseServiceProvider::class`

3. Register the package facade alias to the aliases array in `app.php` file:

    `'API' => Obiefy\API\Facades\API::class,`

5. And finally you can publish the config file:

    `php artisan vendor:publish --tag=api-response`

Note: You could also include "`use Obiefy\API\Facades\API;`" at the top of the class, but we recommend not to.

## Basic usage
There are to ways of utilizing the package: using the `facade`, or using the `helper` function.
On either way you will get the same result, it is totally up to you.

#### Facade:
```php
use API;

...

public function index()
{
    $users = User::all();

    return API::response(200, 'users list', $users);
}
```
Note: If you decide not to register the service provider and the facade, alias then you need to include `use Obiefy\API\Facades\API;` at the top of the class, but we recommend not to.

#### Helper function:
```php
public function index()
{
    $users = User::all();

    return api()->response(200, 'users list', $users);
}
```

## Advanced usage
The `response()` method accepts three mandatory parameters:
 - `int $status`
 - `string $message`
 - `string | array $data`

For example, in the below example we are calling the `response()` method thru the facade and we are passing the following parameters: `200` as the status code, `User list` as the message and `$users` (a collection of users) as the data.

```php
use API;

...

public function index()
{
    $users = User::all();

    return API::response(200, 'Users list', $users);
}
```
This is the result:
```json
{
    "STATUS": 200,
    "MESSAGE": "Users list",
    "DATA": [
        {"name": "Obay Hamed"}
    ]
}
```

If you need more data other than the defaults `STATUS`, `MESSAGE`, and `DATA` attributes on your json response, you could pass as many parameters as you need after `$data`. However, we do recommend formating the extra parameters as a `$key => $value` array.

As you can see in the below example, we are calling the `api()` helper and we are passing the following parameters: `200` as the status code, `User list` as the message, `$users` (a collection of users) as the data, `$code` as a key value array and `$error` as another key value array.

```php
public function index()
{
    $users = User::all();
    $code = ['code' => 30566];
    $error = ['reference' => 'ERROR-2019-09-14'];

    return api()->response(200, 'Users list', $users, $code, $error);
}
```
This is the result:
```json
{
    "STATUS": 200,
    "MESSAGE": "Users list",
    "DATA": [
        {"name": "Obay Hamed"}
    ],
    "code": 30566,
    "error": "ERROR-2019-09-14"
}
```

Another way of creating a response is by calling `api()` method and passing the parameters directly to the helper function. Again, it is up to you how you better want to use it.

Check the below code example.

```php
public function index()
{
    $users = User::all();

    return api(200, 'Users list', $users);
}
```
This is the result:
```json
{
    "STATUS": 200,
    "MESSAGE": "users list",
    "DATA": [
        {"name": "Obay Hamed"}
    ]
}
```

## Helper functions
The package ships with a group of functions that will help you to speed up your development process. For example, you could call directly `api()->ok()` if the response was successful, instead of building the response.

#### `function ok()`
The `ok()` function can be used without passing any parameters, it will defaulted the status code to `200` and use the default message from the configuration file.

```php
return api()->ok();
```
Result:
```json
{
    "STATUS": 200,
    "MESSAGE": "Process is successfully completed",
    "DATA": {}
}
```

Or you could pass to the function a custom message and the data you need. In this case, as mentioned before, the `ok()` status code will be defaulted to 200.

```php
$users = User::all();

return api()->ok("User list", $users);
```
Result:
```json
{
    "STATUS": 200,
    "MESSAGE": "User list",
    "DATA": [
        {"name": "Obay Hamed"}
    ]
}
```

#### `function notFound()`
The `notFound()` function, as its name states, should be used for the case when the resource is not found and the status code will be defaulted to `404`. You could pass a custom message to this function otherwise it will use the default message from the configuration file.

```php
return api()->notFound();
```

#### `function validation()`
The `validation()` function can be used on the case of a validation error exist, throwing a 422 status code by default. It accepts two mandatory parameters: a message and an array of errors, and as many extra parameters you need (we recommend a key value array format). If the message is empty, then the default message will be used instead.

```php
return api()->validation('These fields are required.', ['name', 'lastName']);
```

#### `function error()`
The `error()` function can be used when an internal server error occurs throwing a 500 status code by default. It accepts two mandatory parameters: a message and an array of errors, and as many extra parameters you need (we recommend a key value array format). If the message is empty, then the default message will be used instead.

## Configuration

### JSON Response Labels
If you need to customize the default messages or the json response labels, you can do it directly on the `api.php` configuration file.

|method| default status code  | change code |  message  |
|--|--| -- | --- |
|`ok()`|  200 |`config('api.codes.success)` | `trans('api-response::messages.success)`
|`notFound()`|  404 |`config('api.codes.notfound)` | `trans('api-response::messages.notfound)`
|`validation()`|  422 |`config('api.codes.validation)` | `trans('api-response::messages.validation)`
|`error()`|  500 |`config('api.codes.error)` | `trans('api-response::messages.error)`

### Matching Status Codes
By default, all API responses return a 200 OK HTTP status header. If you'd like the status header to match the Response's status, set the `matchstatus` configuration to `true`

### Include The Count Of Data 
You can optionally include the count of the `DATA` portion of the response, by setting `includeDataCount` to `true` in the `api.php` configuration file. You can also override the label, if desired, by updating the label in the`keys` array of the configuration file.

```json
{
    "STATUS": "200",
    "MESSAGE": "User Profile data",
    "DATA": [
        ...
    ],
    "DATACOUNT": 6
}
```

## Contributing
We will be happy if we see PR from you.

## License
The API Response is a free package released under the MIT License.
