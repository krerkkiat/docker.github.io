---
title: Docker Cloud API reference

language_tabs:
  - http
  - go
  - python
  - shell: CLI

toc_footers:

includes:
  - action
  - provider
  - region
  - availabilityzone
  - nodetype
  - nodecluster
  - node
  - registry
  - repository
  - stack
  - service
  - container
  - triggers
  - dockercloud-events
  - errors

search: true
---

# Introduction

Docker Cloud currently offers a **HTTP REST API** and a **Websocket Stream API** which are used by both the [Web UI](https://cloud.docker.com/) and the [CLI](https://github.com/docker/dockercloud-cli). This API documentation contains all API operations currently supported in the platform and provides examples of how to execute them using our Command Line Interface (CLI), [Python SDK](https://github.com/docker/python-dockercloud) and [Go SDK](https://github.com/docker/go-dockercloud).

# Authentication

In order to be able to make requests to the Docker Cloud API, you should first obtain an ApiKey for your account. For this, log into Docker Cloud, click on the menu on the upper right corner of the screen, select **Account info** and then select **API keys**.

## REST API

```python
import dockercloud
dockercloud.user = "username"
dockercloud.apikey = "apikey"
```

```go
import "github.com/docker/go-dockercloud/dockercloud"

dockercloud.User = "username"
dockercloud.ApiKey = "apikey"
```

```http
GET /api/app/v1/service/ HTTP/1.1
Host: cloud.docker.com
Authorization: Basic dXNlcm5hbWU6YXBpa2V5
Accept: application/json
```

```shell
export DOCKERCLOUD_USER=username
export DOCKERCLOUD_APIKEY=apikey
```

> Make sure to replace `username` with your username and `apikey` with your API key.

The Docker Cloud REST API is reachable through the following hostname:

`https://cloud.docker.com/`

All requests should be sent to this endpoint using `Basic` authentication using your API key as password:

`Authorization: Basic dXNlcm5hbWU6YXBpa2V5`

HTTP responses are given in JSON format, so the following `Accept` header is required for every API call:

`Accept: application/json`

## Stream API

```python
import websocket
import base64

header = "Authorization: Basic %s" % base64.b64encode("%s:%s" % (username, password))
ws = websocket.WebSocketApp('wss://ws.cloud.docker.com/v1/events', header=[header])
```

```go
import "github.com/gorilla/websocket"
import "encoding/base64"

var StreamUrl = "wss://ws.cloud.docker.com:443/v1/events"

sEnc := base64.StdEncoding.EncodeToString([]byte(User + ":" + ApiKey))
header := http.Header{}
header.Add("Authorization", fmt.Sprintf("Basic %s", sEnc))

var Dialer websocket.Dialer
ws, _, err := Dialer.Dial(url, header)
if err != nil {
	log.Println(err)
}
```

```http
GET /api/audit/v1/events HTTP/1.1
Host: ws.cloud.docker.com
Authorization: Basic dXNlcm5hbWU6YXBpa2V5
Connection: Upgrade
Upgrade: websocket
```

```shell
export DOCKERCLOUD_USER=username
export DOCKERCLOUD_APIKEY=apikey
```

> Make sure to replace `username` with your username and `apikey` with your API key.

The Docker Cloud Stream API is reachable through the following hostname:

`wss://ws.cloud.docker.com/`

The Stream API requires the same authentication mechanism as the REST API:

`Authorization: Basic dXNlcm5hbWU6YXBpa2V5`


## API roles

> The CLI and the SDKs will detect this environment variable and automatically use it

If you give an [API role](../../docker-cloud/apps/api-roles/) to a container, the environment variable `DOCKERCLOUD_AUTH` inside the container will have the contents of the `Authorization` header that you can use to authenticate against the REST or Stream APIs:

`curl -H "Authorization: $DOCKERCLOUD_AUTH" https://cloud.docker.com/api/app/v1/service/`
