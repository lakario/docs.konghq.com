---
nav_title: Basic config examples
title: Basic config examples
---

## Basic configuration examples

The following examples provide some typical configurations for enabling the `json-threat-protection` plugin on a [service]({{ site.links.web }}/gateway/api/admin-ee/latest/#/Services).

{ % navtabs %}
{% navtab Enable on a service %}

Make the following request:

```bash
curl -X POST http://localhost:8001/services/{serviceName|Id}/plugins \
  --data "name=json-threat-protection" \
  --data "config.max_container_depth=1" \
  --data "config.max_object_entry_count=2" \
  --data "config.max_object_entry_name_length=3" \
  --data "config.max_array_element_count=2" \
  --data "config.max_string_value_length=3" \
  --data "config.enforce_mode=block" \
  --data "config.error_status_code=400" \
  --data "config.error_message=BadRequest1"
```

{$ endnavtab %}

{% navtab Enable on a route %}

Make the following request:

```bash
curl -X POST http://localhost:8001/routes/{routeName|Id}/plugins \
  --data "name=json-threat-protection" \
  --data "config.max_container_depth=1" \
  --data "config.max_object_entry_count=2" \
  --data "config.max_object_entry_name_length=3" \
  --data "config.max_array_element_count=2" \
  --data "config.max_string_value_length=3" \
  --data "config.enforce_mode=block" \
  --data "config.error_status_code=400" \
  --data "config.error_message=BadRequest1"
```

{$ endnavtab %}

{% navtab Enable globally %}

Make the following request:

```bash
curl -X POST http://localhost:8001/plugins/ \
  --data "name=json-threat-protection" \
  --data "config.max_container_depth=1" \
  --data "config.max_object_entry_count=2" \
  --data "config.max_object_entry_name_length=3" \
  --data "config.max_array_element_count=2" \
  --data "config.max_string_value_length=3" \
  --data "config.enforce_mode=block" \
  --data "config.error_status_code=400" \
  --data "config.error_message=BadRequest1"
```

{$ endnavtab %}

{ % endnavtabs %}
