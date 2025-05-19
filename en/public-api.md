## Network > Internet Gateway > API v2 Guide

To use the API, API endpoint and token are required. Refer to [API usage preparations](/Compute/Compute/en/identity-api/) to prepare the information required to use the API.

The Internet Gateway API utilizes an endpoint of type `network`. The exact endpoint is referenced in the `serviceCatalog`of the token issuance response.

| Type | Region | Endpoint |
|---|---|---|
| network | Korea (Pangyo) Region<br>Korea (Pyeongchon) region<br>Japan (Tokyo) Region<br>United States (California) region | https://kr1-api-network-infrastructure.nhncloudservice.com<br>https://kr2-api-network-infrastructure.nhncloudservice.com<br>https://jp1-api-network-infrastructure.nhncloudservice.com<br>https://us1-api-network-infrastructure.nhncloudservice.com |

In each API response, you may find fields that are not specified within this guide. Those fields are for NHN Cloud internal usage, so refrain from using them because they may be changed without prior notice.

## Internet Gateway
### Get an external network ID
When you create an Internet gateway, you must specify the ID of the external network to which you want to connect through the Internet gateway.
The available external networks can be queried by specifying the `router:external=true` query to [the VPC list view API](/Network/VPC/ko/public-api/#vpc_1).
```
GET /v2.0/vpcs?router:external=true
```

### View a list of Internet gateways
Returns a list of available internet gateways.
```
GET /v2.0/internetgateways
X-Auth-Token: {tokenId}
```

#### Request
This API does not require a request body.


| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | Token ID |
| tenant_id | Query | String | - | The tenant to which the Internet gateway you want to look up belongs to |
| id | Query | UUID | - | Internet gateway ID to look up |
| name | Query | String | - | Internet gateway name to look up |
| external_network_id | Query | UUID | - | The ID of the external network to which the Internet gateway is connected. |
| routingtable_id | Query | UUID | - | The ID of the routing table associated with the Internet gateway you want to look up. |

#### Response

| Name | Type | Format | Description |
| --- | --- | --- | --- |
| internetgateways | Body | Array | List of Internet Gateway Information Objects |
| internetgateway.id | Body | UUID | Internet gateway ID |
| internetgateway.name | Body | String | Internet gateway name |
| internetgateway.external_network_id | Body | UUID | The identity of the external network to which the Internet gateway is connected. |
| internetgateway.routingtable_id | Body | UUID | The routing table ID with which the Internet gateway is associated when it is associated with a routing table. |
| internetgateway.state | Body | String | The status of the Internet gateway. <br>`available`: Steady state<br>`unavailable`: Unused, not connected to routing table<br>`migrating`: The state of being moved to another Internet Gateway server for maintenance.<br>`ERROR`: Connected to routing table but unavailable for normal use|
| internetgateway.create_time | Body | Date | Internet gateway creation time (UTC) |
| internetgateway.tenant_id | Body | String | The tenant ID to which the Internet gateway belongs |
| internetgateway.migrate_status | Body | String | Processing status when moving internet gateways due to maintenance<br>`none`: not moving or the move is complete<br>`unbinding_progress`: Status of `unbinding` from existing Internet Gateway servers<br>`unbinding_error`: Error uninstalling from existing internet gateway server<br>`binding_progress`: Status of configuration on the new Internet Gateway server<br>`binding_error`: Error configuring on new Internet Gateway server |
| internetgateway.migrate_error | Body | String | Error messages when the internet gateway is moving due to maintenance |

<details><summary>Example</summary>
<p>

```json
{
  "internetgateways": [
    {
      "id": "71f8e19a-8af3-4d3f-9b0e-b45a547fd7a7",
      "name": "ig-7ef985c1-8568",
      "external_network_id": "50687905-7b9d-423a-b929-ab8b296a7f35",
      "routingtable_id": "7ef985c1-8568-4faa-a1ce-6a7116ba0e4d",
      "state": "available",
      "create_time": "2025-02-11 00:44:18",
      "tenant_id": "130f20670ac34949b64b10ad8a5989c8",
      "migrate_status": "none",
      "migrate_error": null
    }
  ]
}
```

</p>
</details>

---

### View Internet Gateways
Looks up the specified internet gateway.
```
GET /v2.0/internetgateways/{internetgatewayId}
X-Auth-Token: {tokenId}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| internetgatewayId | URL | UUID | O | Routing table ID to query |
| tokenId | Header | String | O | Token ID |

#### Response

| Name | Type | Format | Description |
| --- | --- | --- | --- |
| internetgateway | Body | Array | Internet Gateway Information Object |
| internetgateway.id | Body | UUID | Internet gateway ID |
| internetgateway.name | Body | String | Internet gateway name |
| internetgateway.external_network_id | Body | UUID | The identity of the external network to which the Internet gateway is connected. |
| internetgateway.routingtable_id | Body | UUID | The routing table ID with which the Internet gateway is associated when it is associated with a routing table. |
| internetgateway.state | Body | String | The status of the Internet gateway. <br>`available`: Steady state<br>`unavailable`: Unused, not connected to routing table<br>`migrating`: The state of being moved to another Internet Gateway server for maintenance.<br>`ERROR`: Connected to routing table but unavailable for normal use|
| internetgateway.create_time | Body | Date | Internet gateway creation time (UTC) |
| internetgateway.tenant_id | Body | String | The tenant ID to which the Internet gateway belongs |
| internetgateway.migrate_status | Body | String | Processing status when moving internet gateways due to maintenance<br>`none`: not moving or the move is complete<br>`unbinding_progress`: Status of `unbinding` from existing Internet Gateway servers<br>`unbinding_error`: Error uninstalling from existing internet gateway server<br>`binding_progress`: Status of configuration on the new Internet Gateway server<br>`binding_error`: Error configuring on new Internet Gateway server |
| internetgateway.migrate_error | Body | String | Error messages when the internet gateway is moving due to maintenance |

<details><summary>Example</summary>
<p>

```json
{
  "internetgateway": {
      "id": "71f8e19a-8af3-4d3f-9b0e-b45a547fd7a7",
      "name": "ig-7ef985c1-8568",
      "external_network_id": "50687905-7b9d-423a-b929-ab8b296a7f35",
      "routingtable_id": "7ef985c1-8568-4faa-a1ce-6a7116ba0e4d",
      "state": "available",
      "create_time": "2025-02-11 00:44:18",
      "tenant_id": "130f20670ac34949b64b10ad8a5989c8",
      "migrate_status": "none",
      "migrate_error": null
    }
}
```

</p>
</details>

---

### Create an Internet gateway

Create a new internet gateway.

```
POST /v2.0/internetgateways
X-Auth-Token: {tokenId}
```

#### Request

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | Token ID |
| internetgateway | Body | Object | O | Internet Gateway information object to create |
| internetgateway.name | Body | String | O | Internet gateway name |
| internetgateway.external_network_id | Body | O | UUID | The external network ID for the Internet Gateway to connect to |

#### Response

| Name | Type | Format | Description |
| --- | --- | --- | --- |
| internetgateway | Body | Array | Internet Gateway Information Object |
| internetgateway.id | Body | UUID | Internet gateway ID |
| internetgateway.name | Body | String | Internet gateway name |
| internetgateway.external_network_id | Body | UUID | The identity of the external network to which the Internet gateway is connected. |
| internetgateway.routingtable_id | Body | UUID | The routing table ID with which the Internet gateway is associated when it is associated with a routing table. |
| internetgateway.state | Body | String | The status of the Internet gateway. <br>`available`: Steady state<br>`unavailable`: Unused, not connected to routing table<br>`migrating`: The state of being moved to another Internet Gateway server for maintenance.<br>`ERROR`: Connected to routing table but unavailable for normal use|
| internetgateway.create_time | Body | Date | Internet gateway creation time (UTC) |
| internetgateway.tenant_id | Body | String | The tenant ID to which the Internet gateway belongs |
| internetgateway.migrate_status | Body | String | Processing status when moving internet gateways due to maintenance<br>`none`: not moving or the move is complete<br>`unbinding_progress`: Status of `unbinding` from existing Internet Gateway servers<br>`unbinding_error`: Error uninstalling from existing internet gateway server<br>`binding_progress`: Status of configuration on the new Internet Gateway server<br>`binding_error`: Error configuring on new Internet Gateway server |
| internetgateway.migrate_error | Body | String | Error messages when the internet gateway is moving due to maintenance |

<details><summary>Example</summary>
<p>

```json
{
  "internetgateway": {
      "id": "71f8e19a-8af3-4d3f-9b0e-b45a547fd7a7",
      "name": "ig-7ef985c1-8568",
      "external_network_id": "50687905-7b9d-423a-b929-ab8b296a7f35",
      "state": "unavailable",
      "create_time": "2025-02-11 00:44:18",
      "tenant_id": "130f20670ac34949b64b10ad8a5989c8",
      "migrate_status": "none"
    }
}
```

</p>
</details>

---

### Delete an Internet gateway

Delete the internet gateway. 

```
DELETE /v2.0/internetgateways/{internetgatewayId}
X-Auth-Token: {tokenId}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| internetgatewayId | URL | UUID | O | Routing table to delete |
| tokenId | Header | String | O | Token ID |


#### Response

This API does not return a response body.

---
