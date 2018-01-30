[![NPM Version][npm-image]][npm-url]
[![Build Status][travis-image]][travis-url]
[![Test Coverage][coveralls-image]][coveralls-url]
[![NPM Downloads][downloads-image]][downloads-url]
<!-- [![Maintainability][codeclimate-maintainability-image]][codeclimate-maintainability-url] -->
<!-- [![Test Coverage][codeclimate-coverage-image]][codeclimate-coverage-url] -->

# requestXn

Wraps request-promise with a retry handler, in order to provide an easy way to send requests with retries and promises.

[![NPM](https://nodei.co/npm/requestxn.png?downloads=true&downloadRank=true&stars=true)][npm-stats]

- [requestXn](#requestxn)
  - [Options](#options)
    - [max](#max)
    - [backoffBase](#backoffbase)
    - [backoffExponent](#backoffexponent)
    - [retryOn5xx](#retryon5xx)
    - [rejectOn5xx](#rejecton5xx)
    - [retryStrategy](#retrystrategy)
    - [onSuccess](#onsuccess)
    - [onError](#onerror)
    - [Example](#example)

## Options

requestxn should support all [request-promise-native](https://github.com/request/request-promise-native) functionality, so you can pass all options as you would pass them to the original package

In addition to the original *request-promise* options, the following extra options are accepted

### max

Maximum number of attempts. Default: 1

```js
max: 1
```

### backoffBase

Initial backoff duration in ms. Default: 100

```js
backoffBase: 100
```

### backoffExponent

Exponent to increase backoff on each attempt. Default: 1.1

```js
backoffExponent: 1.1
```

### retryOn5xx

Retry on 5xx status code. Default: false

```js
retryOn5xx: true
```

### rejectOn5xx

Reject when getting 5xx status code and simple=false. Default: false

By default, requestXn would resolve with the response when simple=false.

By setting this option to true it would reject on such cases.

```js
rejectOn5xx: true
```

### retryStrategy

Custom retry logic function

```js
retryStrategy: function (response) {
  // return a boolean
}
```

### onSuccess

Function to be executed on success

```js
onSuccess: function (options, response, attempts) {
    // do something on success
}
```

### onError

Function to be executed on error

```js
onError: function (options, error, attempts) {
  // do something on error
}
```

### Example

```js
const request = require('requestxn');

const options = {
  url: 'http://www.site-with-issues.com',
  body: {/* body */},
  json: true,
  max: 3,
  backoffBase: 500,
  backoffExponent: 1.3,
  retryOn5xx: true,
  retryStrategy: function(response) {
    return response.statusCode === 500 && response.body.match(/Temporary error/);
  },
  onError: function(options, error, attempts) {
    console.error(`- Request to ${options.uri} failed on the ${attempts} attempt with error ${error.message}`);
  },
  onSuccess: function(options, response, attempts) {
    console.info(`- Got status-code ${response.statusCode} on request to ${request.uri} after ${attempts}`);
  }
}
```

### Result

```js
> request.post(options).then()...
- "Request to http://www.site-with-issues.com failed on the 1 attempt with RequestError: Error: getaddrinfo ENOTFOUND www.site-with-issues.com www.site-with-issues.com:80"
- "Request to http://www.site-with-issues.com failed on the 2 attempt with RequestError: Error: getaddrinfo ENOTFOUND www.site-with-issues.com www.site-with-issues.com:80"
- "Got status-code 200 on request to http://www.site-with-issues.com"
```

### Usage with defaults

```js
const request = require('requestxn');

const requestWithDefaults = request.defaults({
  json: true,
  max: 3,
  backoffBase: 500,
  retryOn5xx: true,
  retryStrategy: function(response) {
    return response.statusCode === 500 && response.body.match(/Temporary error/);
  },
  onError: function(request, error, errorCount) {
    console.error(`- Request to ${request.url} failed on the ${retries} attempt with error ${error.message}`);
  },
  onSuccess: function(request, response) {
    console.info(`- Got status-code ${response.statusCode} on request to ${request.url}`);
  }
});

requestWithDefaults.get('http://www.site-with-issues.com').then...
```

[npm-image]: https://img.shields.io/npm/v/requestxn.svg?style=flat
[npm-url]: https://npmjs.org/package/requestxn
[travis-image]: https://travis-ci.org/kobik/requestxn.svg?branch=master
[travis-url]: https://travis-ci.org/kobik/requestxn
[coveralls-image]: https://coveralls.io/repos/github/kobik/requestxn/badge.svg?branch=master
[coveralls-url]: https://coveralls.io/repos/github/kobik/requestxn/badge.svg?branch=master
[downloads-image]: http://img.shields.io/npm/dm/requestxn.svg?style=flat
[downloads-url]: https://npmjs.org/package/requestxn
[npm-stats]: https://nodei.co/npm/requestxn/
[codeclimate-maintainability-image]: https://api.codeclimate.com/v1/badges/d7bd5d2253291c57dd69/maintainability
[codeclimate-maintainability-url]: https://codeclimate.com/github/kobik/requestxn/maintainability
[codeclimate-coverage-image]: https://api.codeclimate.com/v1/badges/d7bd5d2253291c57dd69/test_coverage
[codeclimate-coverage-url]: https://codeclimate.com/github/kobik/requestxn/test_coverage