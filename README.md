## Dapi

Dapi introspects your database and creates CRUD endpoints. All data from your tables is available at ```localhost:9000/api/v1/crud/<:table>``` and you can specify search criteria with ```?col1=val1&col2=val2```. Special search terms include limit and offset. Additionally, Dapi provides _meta endpoints to enable discoverability.

### Meta Endpoints

To help know the columns and tables available via the API, you can use the ```_meta``` endpoints. ```.../crud/_meta``` will return the meta data for all of your tables, while ```.../crud/<:table>/_meta``` will only return the meta data for that table.

Note, the sample calls below are using HTTPie (pip install httpie). They work just as well with curl.

```
$ http GET :9000/api/v1/crud/user/_meta
HTTP/1.1 200 OK
Content-Length: 465
Content-Type: application/json
Date: Sat, 14 Jun 2014 23:01:41 GMT

{
    "description": "MySQL Table user",
    "location": "/api/v1/crud/user/",
    "methods": [
        "GET",
        "POST",
        "PUT",
        "DELETE"
    ],
    "primary": "id",
    "properties": {
        "email": {
            "description": "",
            "type": "varchar"
        },
        "id": {
            "description": "",
            "type": "int"
        },
        "limit": {
            "description": "Used to limit the number of results returned",
            "type": "int"
        },
        "name": {
            "description": "",
            "type": "varchar"
        },
        "offset": {
            "description": "Used to offset results returned",
            "type": "int"
        }
    },
    "required": [
        "id"
    ],
    "title": "user",
    "type": "object"
}
```

#### Accessing Data

Dapi allows you to easily GET data from your MySQL database.

Sample Call:

Response:

```
# using curl
# $ curl -X GET 'localhost:9000/api/v1/crud/user?limit=1&offset=2'

# using httpie
$ http GET :9000/api/v1/crud/user limit==1 offset==2
HTTP/1.1 200 OK
Content-Length: 50
Content-Type: application/json
Date: Sat, 14 Jun 2014 19:39:32 GMT

[
    {
        "email": "c@example.com",
        "id": "3",
        "name": "john"
    }
]
```

#### Modifying Data

Dapi provides POST, PUT, and DELETE calls to respectively insert, update, and delete records.

```
$ http POST :9000/api/v1/crud/user email="new@example.com" name="New Guy"
HTTP/1.1 200 OK
Content-Length: 39
Content-Type: application/json
Date: Thu, 19 Jun 2014 04:26:01 GMT

{
    "inserted_id": 24,
    "message": "success"
}


$ http PUT :9000/api/v1/crud/user email="updated@example.com" id=24
HTTP/1.1 200 OK
Content-Length: 40
Content-Type: application/json
Date: Thu, 19 Jun 2014 04:26:34 GMT

{
    "message": "success",
    "rows_affected": 1
}


$ http DELETE :9000/api/v1/crud/user name="New Guy" limit=1
HTTP/1.1 200 OK
Content-Length: 40
Content-Type: application/json
Date: Thu, 19 Jun 2014 04:27:01 GMT

{
    "message": "success",
    "rows_affected": 1
}
```

### Testing

Tests have been started for the apid vendored code. ``` $ cd src/vendored/apid && go test```. The current test is an integration test and requires that you have a local mysql instance with root login sans password with a database "apid_integration_test". I plan on updating this to use a testing tag of 'integration' and to allow for a configurable db connection.