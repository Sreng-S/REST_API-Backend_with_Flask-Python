REST API Backend with Flask
==========================
Here is how it looks at this point of time ([live demo](http://flask-restplus-example-server.herokuapp.com/api/v1/)):

![Flask RESTplus Example API](https://raw.githubusercontent.com/frol/flask-restplus-server-example/master/docs/static/Flask_RESTplus_Example_API.png)

```
$ pip install -r app/requirements.txt
$ python server.py
```

Open http://127.0.0.1:5000 and examine the interactive documentation for your
new RESTful API server! You can use any HTTP tools (e.g. `cURL`, `wget`,
Python `requests`, or just a web browser) to communicate with it, or generate
specialized API client libraries for many programming languages using
[Swagger Codegen](https://github.com/swagger-api/swagger-codegen) (learn more
in the [API Integration](#api-integration) section).

Note, this whole repo features much more than that; it demonstrates how I would
organize a production-ready RESTful API server *project*, so stay tunned.

### Application Structure

```
app/
├── requirements.txt
├── __init__.py
├── extensions
│   └── __init__.py
└── modules
    ├── __init__.py
    ├── api
    │   └── __init__.py
    ├── auth
    │   ├── __init__.py
    │   ├── models.py
    │   ├── parameters.py
    │   └── views.py
    ├── users
    │   ├── __init__.py
    │   ├── models.py
    │   ├── parameters.py
    │   ├── permissions.py
    │   ├── resources.py
    │   └── schemas.py
    └── teams
        ├── __init__.py
        ├── models.py
        ├── parameters.py
        ├── resources.py
        └── schemas.py
```

### Using Docker

It is very easy to start exploring the example using Docker:

```bash
$ docker run -it --rm --publish 5000:5000 frolvlad/flask-restplus-server-example
```


### Authentication with Login and Password (Resource Owner Password Credentials Grant)

Here is how you authenticate with user login and password credentials using cURL:

```
$ curl 'http://127.0.0.1:5000/auth/oauth2/token?grant_type=password&client_id=documentation&username=root&password=q'
{
    "token_type": "Bearer",
    "access_token": "oqvUpO4aKg5KgYK2EUY2HPsbOlAyEZ",
    "refresh_token": "3UTjLPlnomJPx5FvgsC2wS7GfVNrfH",
    "expires_in": 3600,
    "scope": "auth:read auth:write users:read users:write teams:read teams:write"
}
```


```
$ curl 'http://127.0.0.1:5000/auth/oauth2/token?grant_type=password' -F 'client_id=documentation' -F 'username=root' -F 'password=q'
```

, or even pass `client_id` as Basic HTTP Auth:

```
$ curl 'http://127.0.0.1:5000/auth/oauth2/token?grant_type=password' --user 'documentation:' -F 'username=root' -F 'password=q'
```

You grab the `access_token` and put it into `Authorization` header
to request "protected" resources:

```
$ curl --header 'Authorization: Bearer oqvUpO4aKg5KgYK2EUY2HPsbOlAyEZ' 'http://127.0.0.1:5000/api/v1/users/me'
{
    "id": 1,
    "username": "root",
    "email": "root@localhost",
    "first_name": "",
    "middle_name": "",
    "last_name": "",
    "is_active": true,
    "is_regular_user": true,
    "is_admin": true,
    "created": "2016-10-20T14:00:35.912576+00:00",
    "updated": "2016-10-20T14:00:35.912602+00:00"
}
```

Once the access token expires, you can refresh it with `refresh_token`. To do
that, OAuth2 RFC defines Refresh Token Flow (notice that there is no need to
store user credentials to do the refresh procedure):

```
$ curl 'http://127.0.0.1:5000/auth/oauth2/token?grant_type=refresh_token' --user 'documentation:' -F 'refresh_token=3UTjLPlnomJPx5FvgsC2wS7GfVNrfH'
{
    "token_type": "Bearer",
    "access_token": "FwaS90XWwBpM1sLeAytaGGTubhHaok",
    "refresh_token": "YD5Rc1FojKX1ZY9vltMSnFxhm9qpbb",
    "expires_in": 3600,
    "scope": "auth:read auth:write users:read users:write teams:read teams:write"
}
```

### Authentication with Client ID and Secret (Client Credentials Grant)

Here is how you authenticate with user login and password credentials using cURL:

```
$ curl 'http://127.0.0.1:5000/auth/oauth2/token?grant_type=client_credentials' --user 'documentation:KQ()SWK)SQK)QWSKQW(SKQ)S(QWSQW(SJ*HQ&HQW*SQ*^SSQWSGQSG'
{
    "token_type": "Bearer",
    "access_token": "oqvUpO4aKg5KgYK2EUY2HPsbOlAyEZ",
    "expires_in": 3600,
    "scope": "teams:read users:read users:write teams:write"
}
```

