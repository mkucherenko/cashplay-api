# Asynchronous Gaming API

This API is extension that adds support for asynchronous games to the CashPlay API.


## General workflow:
* Sign-in (Api::V2)
* Authorize app (OAuth)
* Look-up matches (New Async API)
** If no match found - request to create new one (New Async API)
** If match found - accept it (New Async API)
* Play game
* Post result (New Async API) - and return winner/loser and amounts won/lost

## OAuth2

When a user first tries to perform an action that requires API authentication.

### Authorize application

```
GET /oauth/authorize?client_id=701b86b743d2dfe50d167...&
redirect_uri=http://www.lvh.me:3005/session/callback&
scope=public&
response_type=code
```

After the user consents or refuses to grant access to your application, it will redirect the user to the redirect_uri that you specified.

If the user granted access to your application, it will have appended a code parameter to the redirect_uri. This value is a temporary authorization code that you can exchange for an access token.

`http://www.lvh.me:3005/session/callback?code=d941d06c9c9a4c75feb7fe02fdce2...`

### Exchange authorization code for refresh and access tokens

Assuming the user has granted access to your application, exchange the authorization code for a refresh token and access token.

```
POST /oauth/token HTTP/1.1
Content-Type: `application/x-www-form-urlencoded

grant_type=authorization_code&
code=d941d06c9c9a4c75feb7fe02fdce2...&
client_id=701b86b743d2dfe50d167...&
client_secret=fe2b9a1b591a2e9eabc25e7b...&
redirect_uri=http://www.lvh.me:3005/session/callback
```
Process response and store tokens

#### Response with short-lived access token and a refresh token:

Status: `200 OK`

```json
{
  "access_token" : "ya29.AHES6ZTtm7SuokEB-RGtbBty9IIlNiP9-eNMMQKtXdMP3sfjL1Fc",
  "token_type" : "Bearer",
  "expires_in" : 3600,
  "refresh_token" : "1/HKSmLFXzqP0leUihZp2xUt3-5wkU7Gmu2Os_eBnzw74"
}
```

Important: Your application should store both values.

Refreshing an access token

If your application obtains a refresh token during the authorization process, then you will need to periodically use that token to obtain a new, valid access token. Server-side web applications, installed applications, and devices all obtain refresh tokens.

```
POST /oauth/token HTTP/1.1
Content-Type: application/x-www-form-urlencoded


client_id=701b86b743d2dfe50d167...&
client_secret=fe2b9a1b591a2e9eabc25e7b...&
refresh_token=1/HKSmLFXzqP0leUihZp2xUt3-5wkU7Gmu2Os_eBnzw74&
grant_type=refresh_token
```

#### Respond JSON object that contains a new access token:
```json
{
  "access_token":"1/fFAGRNJru1FTz70BzhT3Zg",
  "expires_in":3920,
  "token_type":"Bearer"
}
```

## Look-up matches

URL: `GET /api/gaming-async/v1/matches.json`

### Required parameters

* Stake (in cents)
* Currency
* Game ID

### Example response:

Status: `200 OK`

```json
{
  "matches": [
  ]
}
```

### Other HTTP return codes

* `401 Unauthorized`
* `422 Unprocessable Entity` - Invalid resource given


## Accept a match

URL: `PUT /api/gaming-async/v1/matches/:id/accept.json`


### Example response:

Status: `200 OK`

```json
{
  "match": {
    "id": "6c66eb60b0ad01304a33040ccee136f0",
    "challenger_id": 123,
    "challenged_id": 321,
    "stake_amount": 500,
    "currency": "GBP",
    "state": "matched"
  }
}
```

### Other HTTP return codes

* `401 Unauthorized`
* `404 Not Found` - Invalid ID given
* `422 Unprocessable Entity` - Invalid resource given


## Create a match

URL: `POST /api/gaming-async/v1/matches.json`

## Example payload:

```json
{
  "match": {
    "stake": 500,
    "currency": "GBP",
    "game_id": 123,
    "challenger_skill_level": 1
  }
}
```


### Example response:

Status: `201 Created`

```json
{
  "match": {
    "id": "6c66eb60b0ad01304a33040ccee136f0",
    "challenger_id": 123,
    "challenged_id": null,
    "stake_amount": 500,
    "currency": "GBP",
    "state": "unmatched",
    "secret": "bC1GfwVGnE6SXamOXekjgNsHxUoWvTCeFVBZcoSd1k"
  }
}
```


### Other HTTP return codes

* `401 Unauthorized`
* `422 Unprocessable Entity` - Invalid resource given


## Report game score

URL: `POST /api/gaming-async/v1/matches/:id/result.json`


## Example payload:

```json
{
  "match_score": {
    "score": 1235,
    "secret": "bC1GfwVGnE6SXamOXekjgNsHxUoWvTCeFVBZcoSd1k"
  }
}
```

### Example response (first score reported):

Status: `200 OK`

```json
{
  "match_result": {
    "match_id": "6c66eb60b0ad01304a33040ccee136f0",
    "challenger_score": 1235,
    "challenged_score": null,
    "winner_id": null,
    "amount_won": null,
    "currency": null
  }
}
```



### Example response (second score reported):

Status: `200 OK`

```json
{
  "match_result": {
    "match_id": "6c66eb60b0ad01304a33040ccee136f0",
    "challenger_score": 102,
    "challenged_score": 221,
    "winner_id": 123,
    "amount_won": 500,
    "currency": "GBP"
  }
}
```

### Other HTTP return codes

* `401 Unauthorized` - Invalid authentication details
* `403 Forbidden`
* `404 Not Found` - Invalid ID given
* `422 Unprocessable Entity` - Invalid resource given
