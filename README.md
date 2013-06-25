# Cashplay API

### Data format

CashPlay API works only with JSON and UTF-8 encoding - no exceptions.


### HTTP status codes

HTTP status codes are used for reporting the request status. If no error was 
occured, the server returns 200 OK response.

Errors that can be solved by modifying request are reported with 4xx status 
codes:

* `401 Unauthorized` - Perform authentication first.
* `403 Forbidden`
* `404 Not Found` - Resource (given by the URL) was not found.
* `422 Unauthorized Entity` - Resource provided contains invalid data (in most 
 likely case the model did not pass the validation)

Internal server errors are reported with 5xx status codes and it is 
responsibility of the client to retry the request later.


### User agent

Althrough not required at this point, it's good practice to identify your app 
via `User-Agent` header (see Authentication examples).


### Pagination

Any action that returns the list can limit the number of returned records using
`per_page` and `page` attributes. `per_page` specifies the number of records 
per fetch and `page` specifies the pagination (starting with 1).

```json
{
  "matches": [ ... ],
  "meta": {
    "count": 1234,
    "next_page": "https://cashplay.co/api/v2/me/challenges/unmatched.json?page=2",
    "prev_page": null
  }
}
```


### Test environment

Our test environment has following URL: `http://integration.cashplay.co`.


### API sections

* [HTML5 Pages](sections/html5_pages.md)
* [Async Gaming API](sections/async_gaming.md)

