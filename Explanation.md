## What was the bug?

When api is true, HttpClient did not refresh the token or add an Authorization header when oauth2Token was a plain JavaScript object, rather than null or an OAuth2Token instance.

## Why did it happen?

The logic treated OAuth2Token as the only valid token type. If oauth2Token was a plain object, it was not considered “missing”, so no refresh happened, and it was not recognized as an OAuth2Token, so no Authorization header was added.

## Why does your fix actually solve it?

The fix considers any non null object that is not an OAuth2Token to be an invalid token state and forces a refresh. After refreshOAuth2 runs, oauth2Token is replaced with a valid OAuth2Token, so the request consistently includes the Authorization header.

## What is one realistic case your tests still do not cover?

Even if a plain object token includes a valid access token and an expiration time in the future, the current fix still forces a refresh instead of using it. To support plain object tokens properly, the client would need a clearly defined token shape and a safe way to evaluate expiration from that shape.
