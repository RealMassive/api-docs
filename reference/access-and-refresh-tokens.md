# Access and refresh tokens

To manage authorization and authentication, RealMassive uses [JSON Web Tokens (JWT)](https://jwt.io/) for users. Most operations you do against our API require you to pass along your JWT with each request*. If you do not, you will receive the following error:

```json
{
    "errors": [
        {
            "status": "401",
            "detail": "Missing or invalid jwt token in Authorization Bearer header.",
            "title": "Invalid JWT"
        }
    ]
}
```

\* _The only exceptions to this rule are GET requests against most entity types, except for users and permissions._

## Getting a JWT

If you have a RealMassive account, you can get a JWT by logging in at our `/accounts/login` endpoint. Send an HTTP POST request to `/accounts/login` with a JSON payload containg `email` and `password` keys. Here is an example via cURL:

```bash
curl -X POST \
  https://www.realmassive.com/accounts/login \
  -H 'content-type: application/json' \
  -d '{
    "email": "youremailhere@domain.com",
    "password": "yourpasswordhere"
}'
```

You will receive a JSON object containing information about your account as well as two JWTs: one for your access token, and the other for your refresh token. Here's an example of what the response looks like for me:

```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJyZWFsbWFzc2l2ZSIsInN1YiI6IndheXNwdXJyY2hlbkByZWFsbWFzc2l2ZS5jb20iLCJleHAiOjE0OTYxNzQ1NDh9.ecl9ZMKLAuNFeyzil0cDEs9NEKifGmMSBXA2OJDYhiM",
  "avatar": null,
  "expires_in": 3600,
  "plan": null,
  "refresh_token": "d2F5c3B1cnJjaGVuQHJlYWxtYXNzaXZlLmNvbXxmNmJhZDVjYy00NDNjLTQxOGMtYWNkNy0zZjljYTM0OTRmNGQ=",
  "token_type": "Bearer",
  "user": {
    "attributes": {
      "created": "2016-05-16T19:39:07.185430",
      "deleted": false,
      "email": "wayspurrchen@realmassive.com",
      "tos_status": "ACCEPTED",
      "updated": "2016-05-16T19:39:15.885950"
    },
    "id": "1295335289799050885",
    "relationships": {
      "memberships": {
        "links": {
          "related": "/users/1295335289799050885/memberships"
        }
      }
    },
    "type": "users"
  }
}
```

Here's another example, using jQuery in JavaScript to send a POST request:

```javascript
$.ajax({
    url: 'https://www.realmassive.com/accounts/login',
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    data: JSON.stringify({
        email: 'youremailhere@domain.com',
        password: 'yourpasswordhere'
    }),
    success: function (response) {
        console.log(response);
    }
});
```

This logs the same JSON object shown above. Note that you must turn the data into a JSON string with `JSON.stringify` and must specify the `Content-Type` header value to be `application/json`.

## Using JWT with requests

In order to use your JWT, you pass it in the `Authorization` header with the value `Bearer <token>`. So for example, with my `access_token` above which is `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJyZWFsbWFzc2l2ZSIsInN1YiI6IndheXNwdXJyY2hlbkByZWFsbWFzc2l2ZS5jb20iLCJleHAiOjE0OTYxNzQ1NDh9.ecl9ZMKLAuNFeyzil0cDEs9NEKifGmMSBXA2OJDYhiM`, I use the header `Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJyZWFsbWFzc2l2ZSIsInN1YiI6IndheXNwdXJyY2hlbkByZWFsbWFzc2l2ZS5jb20iLCJleHAiOjE0OTYxNzQ1NDh9.ecl9ZMKLAuNFeyzil0cDEs9NEKifGmMSBXA2OJDYhiM`. I can then use this to perform various operations against the RealMassive API.

You can prove that your access token is working by sending a GET request to your user entity in the API, whose id you received as the `user.id` property when you received your access token:

```bash
curl https://api.realmassive.com/users/<your_user_id> \
  -H 'Authorization: Bearer <TOKEN>'
```

You should receive the exact payload for the `user` key that you received when first getting your access token.

## Refreshing JWT tokens

**Access tokens last for exactly one hour.** This is indicated by the `expires_in` key in the access token return payload above, which is set to `3600`. That number is seconds, and 3600 seconds is an hour. Once an hour elapses, your access token will no longer function.

If you have a long-running task or application that will run for over an hour, you can obtain a new access token by using the `refresh_token` that is given to you and POSTing it to the `/accounts/refresh` endpoint in a JSON payload:

```bash
curl -X POST \
  https://www.realmassive.com/accounts/refresh \
  -H 'content-type: application/json' \
  -d '{
    "refresh_token": "refreshtokenhere"
}'
```

Here is a JavaScript example:

```javascript
$.ajax({
    url: 'https://www.realmassive.com/accounts/login',
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    data: JSON.stringify({
        refresh_token: ''
    }),
    success: function (response) {
        console.log(response);
    }
});
```

Note that in all cases you must include the `Content-Type: application/json` header. Upon a successful refresh, you will receive back a payload similar to the payload you receive when logging in for the first time, but your access token will have been updated.

Because we do not provide a specific timestamp at which your token will expire, you should calculate this yourself by taking the current timestamp and the expiry to it. Then, you can check at a regular interval whether your access token has expired. Here is an example of how you might implement this in JavaScript (note that this example is in ES6):

```javascript
/**
 * First we do some initial setup of our expiry state and state-checking functionality
 */

const generateExpiryTimestamp = (expiresIn) => {
    // Multiply expiry by 1000 because Date.now() returns milliseconds
    return Date.now() + (expiresIn * 1000);
};

// Checks if the expiry is past the expiration date
const pastExpiration = (expiry) => {
    // While checking for exact equivalence isn't technically past the expiration,
    // by the time we make the request to the server, it will be.
    return Date.now() >= expiry;
};

// Determines whether we're within 20 minutes of our expiration time. 20m
// is chosen arbitrarily as a decent time to refresh the access token
const aboutToExpire = (expiry) => {
    const fortyMinutes = 2400000;
    return Date.now() >= expiry - fortyMinutes && !pastExpiration(expiry);
};

let interval;
let expiryTimestamp;

const refreshAtInterval = () => {
    interval = setInterval(() => {
        if (pastExpiration(expiryTimestamp)) {
            // We expired somehow, so the user/your script needs to reauthenticate
            // against RealMassive here.
        }

        if (aboutToExpire()) {
            clearInterval(interval);

            // This function takes a callback which expects to receive the expiry
            // (which in this example will be 3600), and then generates a new
            // timestamp and kicks off the refresh interval again.
            yourRefreshFunction((expiry) => {
                generateExpiryTimestamp(expiry);
                refreshAtInterval();
            });
        }
    }, 5000)
};

/**
 * Here, we actually get the tokens and kick off our refresh functionality
 */

// Get tokens...
const tokens = yourTokenAcquiringFunction();

// Set up our first expiry
expiryTimestamp = generateExpiryTimestamp();

// This kicks off the regular check of the status of our access token
refreshAtInterval();
```

The above example omits places where you would cache your refresh and access tokens in local state, which are assumed to be handled within `yourTokenAcquiringFunction` and `yourRefreshFunction`. This also lacks error handling in cases where you could not refresh your token for some reason.
