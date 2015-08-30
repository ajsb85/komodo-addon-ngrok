## ngrok Client API

The ngrok client exposes a REST API that grants programmatic access to:

*   Collect status and metrics information
*   Collect and replay captured requests
*   Start and stop tunnels dynamically

#### Base URL and Authentication

<table class="table">

<tbody>

<tr>

<th>Base URL</th>

<td>`http://127.0.0.1:4040/api`</td>

</tr>

<tr>

<th>Authentication</th>

<td>**None**</td>

</tr>

</tbody>

</table>

The ngrok client API is exposed as part of ngrok's local web inspection interface. Because it is served on a local interface, the API has no authentication. The Base URL will change if you override `web_addr` in your configuration file.

###### Access the root API resource of a running ngrok client

<div class="well">

    curl http://localhost:4040/api/

</div>

#### Supported Content Types

Request parameters may be encoded to the API using either `application/x-www-form-urlencoded` or `application/json`. Ensure that your client sets the request's `Content-Type` header appropriately. All responses returned by the API are `application/json`.

#### Versioning and API Stability

The ngrok client API guarantees that breaking changes to the API will never be made unless the caller explicitly opts in to a newer version. The mechanism by which a caller opts into a new version of the API will be determined in the future when it becomes necessary. Examples of non-breaking changes to the API that will not be opt-in include the following.

*   The addition of new resources
*   The addition of new methods to existing resources
*   The addition of new fields on existing resource representations
*   Bug fixes which change the API to match documented behavior

<div class="api">

#### List Tunnels

Returns a list of running tunnels with status and metrics information.

##### Request

<div class="endpoint"><span class="verb">GET</span><span class="path">/api/tunnels</span></div>

##### Response

###### Parameters

<table class="table">

<tbody>

<tr>

<th>`tunnels`</th>

<td>list of all running tunnels. See the [Tunnel detail](#tunnel-detail) resource for docs on the parameters of each tunnel object</td>

</tr>

</tbody>

</table>

###### Example Response

<div class="well">

    {
        "tunnels": [
            {
                "name": "command_line",
                "uri": "/api/tunnels/command_line",
                "public_url": "https://d95211d2.ngrok.io",
                "proto": "https",
                "config": {
                    "addr": "localhost:80",
                    "inspect": true,
                },
                "metrics": {
                    "conns": {
                        "count": 0,
                        "gauge": 0,
                        "rate1": 0,
                        "rate5": 0,
                        "rate15": 0,
                        "p50": 0,
                        "p90": 0,
                        "p95": 0,
                        "p99": 0
                    },
                    "http": {
                        "count": 0,
                        "rate1": 0,
                        "rate5": 0,
                        "rate15": 0,
                        "p50": 0,
                        "p90": 0,
                        "p95": 0,
                        "p99": 0
                    }
                }
            },
            ...
        ],
        "uri": "/api/tunnels"
    }

</div>

#### Start tunnel

Dynamically starts a new tunnel on the ngrok client. The request body parameters are the same as those you would use to define the tunnel in the configuration file.

##### Request

<div class="endpoint"><span class="verb">POST</span><span class="path">/api/tunnels</span></div>

###### Parameters

Parameter names and behaviors are identical to those those defined in the configuration file. Use the [tunnel definitions](#tunnel-definitions) section as a reference for configuration parameters and their behaviors.

###### Example request body

<div class="well">

    {
        "addr": "22",
        "proto": "tcp",
        "name": "ssh"
    }

</div>

##### Response

201 status code with a response body describing the started tunnel. See the [Tunnel detail](#tunnel-detail) resource for docs on the parameters of the response object

###### Example Response

<div class="well">

    {
        "name": "",
        "uri": "/api/tunnels/",
        "public_url": "tcp://0.tcp.ngrok.io:53476",
        "proto": "tcp",
        "config": {
            "addr": "localhost:22",
            "inspect": false,
        },
        "metrics": {
            "conns": {
                "count": 0,
                "gauge": 0,
                "rate1": 0,
                "rate5": 0,
                "rate15": 0,
                "p50": 0,
                "p90": 0,
                "p95": 0,
                "p99": 0
            },
            "http": {
                "count": 0,
                "rate1": 0,
                "rate5": 0,
                "rate15": 0,
                "p50": 0,
                "p90": 0,
                "p95": 0,
                "p99": 0
            }
        }
    }

</div>

#### Tunnel detail

Get status and metrics about the named running tunnel

##### Request

<div class="endpoint"><span class="verb">GET</span><span class="path">/api/tunnels/:name</span></div>

##### Response

###### Example Response

<div class="well">

    {
        "name": "command_line",
        "uri": "/api/tunnels/command_line",
        "public_url": "https://ac294125.ngrok.io",
        "proto": "https",
        "config": {
            "addr": "localhost:80",
            "inspect": true,
        },
        "metrics": {
            "conns": {
                "count": 0,
                "gauge": 0,
                "rate1": 0,
                "rate5": 0,
                "rate15": 0,
                "p50": 0,
                "p90": 0,
                "p95": 0,
                "p99": 0
            },
            "http": {
                "count": 0,
                "rate1": 0,
                "rate5": 0,
                "rate15": 0,
                "p50": 0,
                "p90": 0,
                "p95": 0,
                "p99": 0
            }
        }
    }

</div>

#### Stop tunnel

Stop a running tunnel

##### Request

<div class="endpoint"><span class="verb">DELETE</span><span class="path">/api/tunnels/:name</span></div>

##### Response

204 status code with an empty body

#### List Captured Requests

Returns a list of all HTTP requests captured for inspection. This will only return requests that are still in memory (ngrok evicts captured requests when their memory usage exceeds `inspect_db_size`)

##### Request

<div class="endpoint"><span class="verb">GET</span><span class="path">/api/requests/http</span></div>

###### Query Parameters

<table class="table">

<tbody>

<tr>

<th>`limit`</th>

<td>maximum number of requests to return</td>

</tr>

<tr>

<th>`tunnel_name`</th>

<td>filter requests only for the given tunnel name</td>

</tr>

</tbody>

</table>

###### Example Request

<div class="well">

    curl http://localhost:4040/requests/http?limit=50

</div>

##### Response

<table class="table">

<tbody>

<tr>

<th>`requests`</th>

<td>list of captured requests. See the [Captured Request Detail](#request-detail) resource for docs on the request objects</td>

</tr>

</tbody>

</table>

###### Example Response

<div class="well">

    {
        "uri": "/api/requests/http",
        "requests": [
            {
                "uri": "/api/requests/http/548fb5c700000002",
                "id": "548fb5c700000002",
                "tunnel_name": "command_line (http)",
                "remote_addr": "192.168.100.25",
                "start": "2014-12-15T20:32:07-08:00",
                "duration": 3893202,
                "request": {
                    "method": "GET",
                    "proto": "HTTP/1.1",
                    "headers": {
                        "Accept": [
                            "*/*"
                        ],
                        "Accept-Encoding": [
                            "gzip, deflate, sdch"
                        ],
                        "Accept-Language": [
                            "en-US,en;q=0.8"
                        ],
                        "Connection": [
                            "keep-alive"
                        ],
                        "User-Agent": [
                            "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/39.0.2171.71 Safari/537.36"
                        ],
                        "X-Original-Host": [
                            "c159663f.ngrok.io"
                        ]
                    },
                    "uri": "/favicon.ico",
                    "raw": ""
                },
                "response": {
                    "status": "502 Bad Gateway",
                    "status_code": 502,
                    "proto": "HTTP/1.1",
                    "headers": {
                        "Content-Length": [
                            "1716"
                        ]
                    },
                    "raw": "",
                }
            },
            ...
        ]
    }

</div>

#### Replay Captured Request

Replays a request against the local endpoint of a tunnel

##### Request

<div class="endpoint"><span class="verb">POST</span><span class="path">/api/requests/http</span></div>

###### Parameters

<table class="table">

<tbody>

<tr>

<th>`id`</th>

<td>id of request to replay</td>

</tr>

<tr>

<th>`tunnel_name`</th>

<td>name of the tunnel to play the request against. If unspecified, the request is played against the same tunnel it was recorded on</td>

</tr>

</tbody>

</table>

###### Example Request

<div class="well">

    curl -d "id=548fb5c700000002" http://localhost:4040/requests/http

</div>

##### Response

204 status code with an empty body

#### Delete Captured Requests

Deletes all captured requests

##### Request

<div class="endpoint"><span class="verb">DELETE</span><span class="path">/api/requests/http</span></div>

##### Response

204 status code with no response body

#### Captured Request Detail

Returns metadata and raw bytes of a captured request. The raw data is base64-encoded in the JSON response. The `response` value maybe `null` if the local server has not yet responded to a request.

##### Request

<div class="endpoint"><span class="verb">GET</span><span class="path">/api/requests/http/:request_id</span></div>

##### Response

###### Example Response

<div class="well">

    {
        "uri": "/api/requests/http/548fb5c700000002",
        "id": "548fb5c700000002",
        "tunnel_name": "command_line (http)",
        "remote_addr": "192.168.100.25",
        "start": "2014-12-15T20:32:07-08:00",
        "duration": 3893202,
        "request": {
            "method": "GET",
            "proto": "HTTP/1.1",
            "headers": {
                "Accept": [
                    "*/*"
                ],
                "Accept-Encoding": [
                    "gzip, deflate, sdch"
                ],
                "Accept-Language": [
                    "en-US,en;q=0.8"
                ],
                "Connection": [
                    "keep-alive"
                ],
                "User-Agent": [
                    "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/39.0.2171.71 Safari/537.36"
                ],
                "X-Original-Host": [
                    "c159663f.ngrok.io"
                ]
            },
            "uri": "/favicon.ico",
            "raw": ""
        },
        "response": {
            "status": "502 Bad Gateway",
            "status_code": 502,
            "proto": "HTTP/1.1",
            "headers": {
                "Content-Length": [
                    "1716"
                ]
            },
            "raw": "",
        }
    }

</div>

</div>

