# HTML 5 API

## Available routes

### Request
`GET /mobile/routes`

### Response
```json
{
  "home_path":"/mobile/home?game_id=:game_id&latitude=:gps_latitude&longitude=:gps_longitude",
  "match_result_path":"/mobile/matches/:match_id/result"
}
```

## Start application
You request **home_path** after start application.

`GET /mobile/home?game_id=1&latitude=50.079752&longitude=14.429737`

If GPS position are located in restricted area, response should be redirect back to application
`cashplay://location_denied`

Else You are being redirected to sign in, authorize game and select stake amout.

You must trigger cashplay protocol to switch control back to app. Part of this url are **match id** and **secret token**, that will be used to post result.

`cashplay://play?match_id=1&secret=9rUZES0nxJmxRDQaWG616v0eIEVFW7sxZqCgpWklAbIGzD5aZxiw`

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

