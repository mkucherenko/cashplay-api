# HTML 5 API

## Available routes

### Request
`GET /mobile/routes`

### Response
```json
{
  "home_path":"/mobile/home?game_id=:game_id&lat=:gps_lat&lng=:gps_lng",
  "match_prolong_path":"/mobile/matches/:match_id/prolong",
  "match_result_path":"/mobile/matches/:match_id/result"
}
```

## Start application
You request **home_path** after start application.

`GET /mobile/home?game_id=1&lat=50.079752&lng=14.429737`

If GPS position are located in restricted area, response should be redirect back to application
`cashplay://location_denied`

Else You are being redirected to sign in, authorize game and select stake amout.

You must trigger cashplay protocol to switch control back to app. Part of this url are **match id**, **result_until** and **secret token**, that will be used to post result.

Result until is formated date time in iso 8601, that specify timestamp, until client must send result, otherwise his score is 0.

```
cashplay://play?
  match_id=1&
  result_until=2013-06-25T14%3A34%3A24%2B02%3A00&
  secret=9rUZES0nxJmxRDQaWG616v0eIEVFW7sxZqCgpWklAbIGzD5aZxiw
```

## Post results

After playing game, you should post results to **match_result_path**.
```
POST /mobile/matches/1/result
Content-Type: `application/x-www-form-urlencoded

match_score[score]=15&
match_score[secret]=9rUZES0nxJmxRDQaWG616v0eIEVFW7sxZqCgpWklAbIGzD5aZxiw
```

If user **cancel** game, data for result should be:
```
match_score[score]=forfeit&
match_score[secret]=9rUZES0nxJmxRDQaWG616v0eIEVFW7sxZqCgpWklAbIGzD5aZxiw
```

## Prolong post result

If mobile application crash or gaming time exceed timestamp of result until time, you can prolong this timestamp.

### Request

`PUT /mobile/matchs/1/prolong`

### Response

```
cashplay://play?
  match_id=1&
  result_until=2013-06-25T15%3A34%3A24%2B02%3A00&
  secret=9rUZES0nxJmxRDQaWG616v0eIEVFW7sxZqCgpWklAbIGzD5aZxiw
```
