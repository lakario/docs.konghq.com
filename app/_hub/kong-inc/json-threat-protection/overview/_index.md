---
nav_title: Overview
---

The JSON Threat Protection plugin provides security validation on various aspects of JSON to ensure that the incoming JSON in requests adheres to the security policy limits.

It will check the following points:

- Maximum container depth of the entire JSON
- Maximum number of array elements
- Maximum number of object entries
- Maximum length of object keys
- Maximum length of strings

For example, for the following JSON:

```
{
  "name": "Jason",
  "age": 20,
  "gender": "male",
  "parents": ["Joseph", "Viva"]
}
```

- Maximum container depth: 2
- Maximum number of array elements: 2
- Maximum number of object entries: 4
- Maximum length of object keys: 7 (`parents`)
- Maximum length of strings: 6 (`Joseph`)



## Using the plugin

We first create a service and route, and then create a JSON-threat-protection policy.



### Create a Service

```bash
curl -i -s -X POST http://localhost:8001/services \
  --data name=example_service \
  --data url='http://localhost/'
```

In this example, an Nginx instance is started locally with the default port 80.

The response is:

```json
{
  "tls_verify": null,
  "created_at": 1718876195,
  "tls_verify_depth": null,
  "connect_timeout": 60000,
  "write_timeout": 60000,
  "host": "localhost",
  "updated_at": 1718876195,
  "name": "example_service",
  "protocol": "http",
  "enabled": true,
  "ca_certificates": null,
  "id": "1c5ba231-a3fe-4884-b422-ce6395fa227f",
  "port": 80,
  "read_timeout": 60000,
  "tags": null,
  "client_certificate": null,
  "retries": 5,
  "path": "/"
}
```



### Create a Route

```bash
curl -i -X POST http://localhost:8001/services/example_service/routes \
  --data 'paths[]=/' \
  --data name=example_route
```

The response is:

```json
{
  "created_at": 1718876213,
  "https_redirect_status_code": 426,
  "protocols": [
    "http",
    "https"
  ],
  "hosts": null,
  "methods": null,
  "path_handling": "v0",
  "updated_at": 1718876213,
  "destinations": null,
  "preserve_host": false,
  "name": "example_route",
  "headers": null,
  "paths": [
    "/"
  ],
  "service": {
    "id": "1c5ba231-a3fe-4884-b422-ce6395fa227f"
  },
  "sources": null,
  "snis": null,
  "id": "8ec673f6-3399-4b74-b3dc-f31632770dd1",
  "regex_priority": 0,
  "tags": null,
  "strip_path": true,
  "request_buffering": true,
  "response_buffering": true
}
```



### Create a JSON-Threat-Protection Policy

```bash
curl -X POST http://127.0.0.1:8001/plugins \
  --data "name=json-threat-protection" \
  --data "service.name=example_service" \
  --data "config.max_body_size=1024" \
  --data "config.max_container_depth=2" \
  --data "config.max_object_entry_count=4" \
  --data "config.max_object_entry_name_length=7" \
  --data "config.max_array_element_count=2" \
  --data "config.max_string_value_length=6" \
  --data "config.enforce_mode=block" \
  --data "config.error_status_code=400" \
  --data "config.error_message=BadRequest1"
```

The response is:

```json
{
  "updated_at": 1718876232,
  "created_at": 1718876232,
  "consumer": null,
  "config": {
    "max_body_size": 1024,
    "max_container_depth": 2,
    "max_array_element_count": 2,
    "max_object_entry_count": 4,
    "max_object_entry_name_length": 7,
    "max_string_value_length": 6,
    "enforce_mode": "block",
    "error_status_code": 400,
    "error_message": "BadRequest1"
  },
  "protocols": [
    "grpc",
    "grpcs",
    "http",
    "https"
  ],
  "service": {
    "id": "1c5ba231-a3fe-4884-b422-ce6395fa227f"
  },
  "name": "json-threat-protection",
  "id": "d8ef3743-f97e-4c98-87f1-53621cb28958",
  "tags": null,
  "route": null,
  "ordering": null,
  "consumer_group": null,
  "instance_name": null,
  "enabled": true
}
```



## Examples:

This request will successfully fetch the default page from the upstream Nginx.

```bash
curl -XPOST http://127.1:8000/ \
  -H "Content-Type: application/json" \
  -d '{"name": "Jason","age": 20,"gender": "male","parents": ["Joseph", "Viva"]}' 
```

The response is:

```html
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```



This request will be intercepted by the JSON-Threat-Protection plugin and will return the configured message.

```bash
curl -XPOST http://127.1:8000/ \
  -H "Content-Type: application/json" \
  -d '{"name": "Jason","age": 20,"gender": "male","parents": ["Dad Joseph", "Viva"]}' 
```

The response is:

```json
{
  "message":"BadRequest1",
  "request_id":"176e093c766974458e8de53b907ff25f"
}
```
