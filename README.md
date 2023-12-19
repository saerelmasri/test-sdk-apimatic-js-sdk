
# Getting Started with CELITECH API

## Introduction

Welcome to the CELITECH API documentation!

Useful links: [Homepage](https://www.celitech.com) | [Support email](mailto:support@celitech.com) | [Blog](https://www.celitech.com/blog/)

### Introduction

This guide is your go-to resource for the CELITECH API, with full documentation and schemas.

Need help? Email us at support@celitech.com.

"Partners" refers to online service providers that use our eSIM API. Access levels include Gold, Platinum, and Diamond.

#### API

The CELITECH API is designed for use by partner platforms, including both web and mobile applications. It's assumed all endpoint calls are initiated from the backend of an integrated platform.

API URL: `https://api.celitech.net/v1`

#### Authentication & Authorization

CELITECH API uses the OAuth 2.0 protocol for authentication and authorization.
The endpoints are protected using client credentials flow which is based on a token exchange. The token has a defined life span (typically 1 hour), after which a new token must be obtained.

To begin, obtain OAuth 2.0 client credentials ( **CLIENT_ID** & **CLIENT_SECRET** ) from the [CELITECH Dashboard](https://www.dashboard.celitech.com/). Then your client application requests an access token from the CELITECH Authorization Server, extracts a token from the response, and sends the token to the CELITECH API that you want to access.

Security Scheme Type: `OAuth2`

Flow type: `clientCredentials`

Token URL: `https://auth.celitech.net/oauth2/token`

## Install the Package

Run the following command from your project directory to install the package from npm:

```ts
npm install test-sdk-apimatic-sdk@1.0.1
```

For additional package details, see the [Npm page for the test-sdk-apimatic-sdk@1.0.1 npm](https://www.npmjs.com/package/test-sdk-apimatic-sdk/v/1.0.1).

## Initialize the API Client

**_Note:_** Documentation for the client can be found [here.](https://www.github.com/saerelmasri/test-sdk-apimatic-js-sdk/tree/1.0.1/doc/client.md)

The following parameters are configurable for the API Client:

| Parameter | Type | Description |
|  --- | --- | --- |
| `environment` | Environment | The API environment. <br> **Default: `Environment.Production`** |
| `timeout` | `number` | Timeout for API calls.<br>*Default*: `0` |
| `httpClientOptions` | `Partial<HttpClientOptions>` | Stable configurable http client options. |
| `unstableHttpClientOptions` | `any` | Unstable configurable http client options. |
| `oAuthClientId` | `string` | OAuth 2 Client ID |
| `oAuthClientSecret` | `string` | OAuth 2 Client Secret |
| `oAuthToken` | `OAuthToken` | Object for storing information about the OAuth token |

### HttpClientOptions

| Parameter | Type | Description |
|  --- | --- | --- |
| `timeout` | `number` | Timeout in milliseconds. |
| `httpAgent` | `any` | Custom http agent to be used when performing http requests. |
| `httpsAgent` | `any` | Custom https agent to be used when performing http requests. |
| `retryConfig` | `Partial<RetryConfiguration>` | Configurations to retry requests. |

### RetryConfiguration

| Parameter | Type | Description |
|  --- | --- | --- |
| `maxNumberOfRetries` | `number` | Maximum number of retries. <br> *Default*: `0` |
| `retryOnTimeout` | `boolean` | Whether to retry on request timeout. <br> *Default*: `true` |
| `retryInterval` | `number` | Interval before next retry. Used in calculation of wait time for next request in case of failure. <br> *Default*: `1` |
| `maximumRetryWaitTime` | `number` | Overall wait time for the requests getting retried. <br> *Default*: `0` |
| `backoffFactor` | `number` | Used in calculation of wait time for next request in case of failure. <br> *Default*: `2` |
| `httpStatusCodesToRetry` | `number[]` | Http status codes to retry against. <br> *Default*: `[408, 413, 429, 500, 502, 503, 504, 521, 522, 524]` |
| `httpMethodsToRetry` | `HttpMethod[]` | Http methods to retry against. <br> *Default*: `['GET', 'PUT']` |

The API client can be initialized as follows:

```ts
const client = new Client({
  timeout: 0,
  environment: Environment.Production,
  oAuthClientId: 'OAuthClientId',
  oAuthClientSecret: 'OAuthClientSecret',
});
```

## Authorization

This API uses `OAuth 2 Client Credentials Grant`.

## Client Credentials Grant

Your application must obtain user authorization before it can execute an endpoint call in case this SDK chooses to use *OAuth 2.0 Client Credentials Grant*. This authorization includes the following steps

The `fetchToken` method will exchange the OAuth client credentials for an *access token*. The access token is an object containing information for authorizing client requests and refreshing the token itself.

```ts
try {
  const token = await client.clientCredentialsAuthManager.fetchToken();
  client.withConfiguration({
    oAuthToken: token,
  });
} catch(error) {
  // handle ApiError or OAuthProviderError if needed
}
```

The client can now make authorized endpoint calls.

### Storing an access token for reuse

It is recommended that you store the access token for reuse.

```ts
Store the token in session storage or local storage.
```

### Creating a client from a stored token

To authorize a client from a stored access token, just set the access token in Configuration along with the other configuration parameters before creating the client:

```ts
const newClient = client.withConfiguration({oAuthToken: token});
```

### Complete example

```ts
// function for storing token to database
async function saveTokenToDatabase(token: OAuthToken) {
  // Code to save the token to the database
}

// function for loading token from database
async function loadTokenFromDatabase(): Promise<OAuthToken | undefined> {
  // Load token from the database and return it (return undefined if no token exists)
  return undefined;
}

// create a new client
const client = new Client({
  timeout: 0,
  environment: Environment.Production,
  oAuthClientId: 'OAuthClientId',
  oAuthClientSecret: 'OAuthClientSecret',
});

// obtain access token, needed for client to be authorized
const previousToken = await loadTokenFromDatabase();
if (previousToken) {
  // restore previous access token and update the cloned configuration with the token
  return client.withConfiguration({
   oAuthToken: previousToken,
  });
} else {
  // obtain a new access token
  try {
    const token = await client.clientCredentialsAuthManager.fetchToken();
    await saveTokenToDatabase(token);
    return client.withConfiguration({
     oAuthToken: token,
    });
  } catch (error) {
      if (error instanceof OAuthProviderError) {
          // handle OAuthProviderError if needed
      }
      return client;
    }
}
```

## List of APIs

* [O Auth Authorization](https://www.github.com/saerelmasri/test-sdk-apimatic-js-sdk/tree/1.0.1/doc/controllers/o-auth-authorization.md)
* [Destinations](https://www.github.com/saerelmasri/test-sdk-apimatic-js-sdk/tree/1.0.1/doc/controllers/destinations.md)
* [E SIM](https://www.github.com/saerelmasri/test-sdk-apimatic-js-sdk/tree/1.0.1/doc/controllers/e-sim.md)
* [Packages](https://www.github.com/saerelmasri/test-sdk-apimatic-js-sdk/tree/1.0.1/doc/controllers/packages.md)
* [Purchases](https://www.github.com/saerelmasri/test-sdk-apimatic-js-sdk/tree/1.0.1/doc/controllers/purchases.md)

## Classes Documentation

* [ApiResponse](https://www.github.com/saerelmasri/test-sdk-apimatic-js-sdk/tree/1.0.1/doc/api-response.md)
* [ApiError](https://www.github.com/saerelmasri/test-sdk-apimatic-js-sdk/tree/1.0.1/doc/api-error.md)

