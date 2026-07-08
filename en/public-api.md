<a id="network-internet-gateway-api-v2-guide"></a>
## Network > Internet Gateway > API v2 Guide { #network-internet-gateway-api-v2-guide }

NHN Cloud Network services use IaaS tokens for authentication and authorization when making API calls. The IaaS token is an authentication token used for NHN Cloud's OpenStack-based infrastructure services (IaaS). For more information on issuing and using IaaS tokens, please refer to the [IaaS Token](/nhncloud/en/public-api/iaas-token).

For Internet Gateway APIs, the `network` type endpoint is used. For more details, see `serviceCatalog`from the response of token issuance.

| Type | Region | Endpoint |
|---|---|---|
| network | Korea (Pangyo) Region<br>Korea (Pyeongchon) Region<br>Korea (Gwangju) Region<br>Japan (Tokyo) Region | https://kr1-api-network-infrastructure.nhncloudservice.com<br>https://kr2-api-network-infrastructure.nhncloudservice.com<br>https://kr3-api-network-infrastructure.nhncloudservice.com<br>https://jp1-api-network-infrastructure.nhncloudservice.com |

In each API response, you may find fields that are not specified within this guide. Those fields are for NHN Cloud internal usage, so refrain from using them because they may be changed without prior notice.

<a id="internet-gateway"></a>
## Internet Gateway { #internet-gateway }
<a id="get-an-external-network-id"></a>
### Get an external network ID { #get-an-external-network-id }
When creating an Internet Gateway, you must specify the ID of the external network to connect through the Internet Gateway.
The available external networks can be queried by specifying the `router:external=true` query to [the VPC list view API](/Network/VPC/en/public-api/#vpc_1).
```
GET /v2.0/vpcs?router:external=true
```

<a id="get-a-list-of-internet-gateways"></a>
### Get a list of Internet Gateways { #get-a-list-of-internet-gateways }
Returns a list of available internet gateways.
```
GET /v2.0/internetgateways
X-Auth-Token: {tokenId}
```

<a id="get-a-list-of-internet-gateways-request"></a>
#### Request
This API does not require a request body.


| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | Token ID |
| tenant_id | Query | String | - | The tenant to which the internet gateway to look up belongs |
| id | Query | UUID | - | Internet gateway ID to look up |
| name | Query | String | - | Internet gateway name to look up |
| external_network_id | Query | UUID | - | The ID of the external network to which the internet gateway is connected. |
| routingtable_id | Query | UUID | - | The ID of the routing table associated with the internet gateway to look up. |

<a id="get-a-list-of-internet-gateways-response"></a>
#### Response

| Name | Type | Format | Description |
| --- | --- | --- | --- |
| internetgateways | Body | Array | List of Internet Gateway Information Objects |
| internetgateway.id | Body | UUID | Internet gateway ID |
| internetgateway.name | Body | String | Internet gateway name |
| internetgateway.external_network_id | Body | UUID | The ID of the external network to which the internet gateway is connected. |
| internetgateway.routingtable_id | Body | UUID | The ID of the routing table that the internet gateway is associated with when connected to a routing table |
| internetgateway.state | Body | String | The status of the Internet Gateway. <br>`available`: Operating normally<br>`unavailable`: Not in use; not connected to a routing table<br>`migrating`: Being moved to another internet gateway server for maintenance<br>`ERROR`: Connected to a routing table but not functioning properly|
| internetgateway.create_time | Body | Date | Internet gateway creation time (UTC) |
| internetgateway.tenant_id | Body | String | The tenant ID to which the internet gateway belongs |
| internetgateway.migrate_status | Body | String | Processing status when migrating internet gateways due to maintenance<br>`none`: Not migrating or migration has completed<br>`unbinding_progress`: Being removed from existing internet gateway server<br>`unbinding_error`: Error occurred while removing from existing internet gateway server<br>`binding_progress`: Being configured on the new internet gateway server<br>`binding_error`: Error occurred while configuring on the new internet gateway server |
| internetgateway.migrate_error | Body | String | Error messages during the internet gateway migration for maintenance |

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

<a id="view-internet-gateways"></a>
### View Internet Gateways { #view-internet-gateways }
Looks up the specified internet gateway.
```
GET /v2.0/internetgateways/{internetgatewayId}
X-Auth-Token: {tokenId}
```

<a id="view-internet-gateways-request"></a>
#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| internetgatewayId | URL | UUID | O | Routing table ID to query |
| tokenId | Header | String | O | Token ID |

<a id="view-internet-gateways-response"></a>
#### Response

| Name | Type | Format | Description |
| --- | --- | --- | --- |
| internetgateway | Body | Array | Internet Gateway Information Object |
| internetgateway.id | Body | UUID | Internet gateway ID |
| internetgateway.name | Body | String | Internet gateway name |
| internetgateway.external_network_id | Body | UUID | The ID of the external network to which the Internet gateway is connected. |
| internetgateway.routingtable_id | Body | UUID | The ID of the routing table that the Internet Gateway is associated with when connected to a routing table |
| internetgateway.state | Body | String | The status of the Internet Gateway. <br>`available`: Operating normally<br>`unavailable`: Not in use; not connected to a routing table<br>`migrating`: Being moved to another internet gateway server for maintenance<br>`ERROR`: Connected to a routing table but not functioning properly|
| internetgateway.create_time | Body | Date | Internet gateway creation time (UTC) |
| internetgateway.tenant_id | Body | String | The tenant ID to which the internet gateway belongs |
| internetgateway.migrate_status | Body | String | Processing status when migrating internet gateways due to maintenance<br>`none`: not moving or the move is complete<br>`unbinding_progress`: Status of `unbinding` from existing internet gateway servers<br>`unbinding_error`: Error uninstalling from existing internet gateway server<br>`binding_progress`: Status of configuration on the new internet gateway server<br>`binding_error`: Error configuring on new internet gateway server |
| internetgateway.migrate_error | Body | String | Error messages during the internet gateway migration for maintenance |

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

<a id="create-an-internet-gateway"></a>
### Create an Internet gateway { #create-an-internet-gateway }

Create a new internet gateway.

```
POST /v2.0/internetgateways
X-Auth-Token: {tokenId}
```

<a id="create-an-internet-gateway-request"></a>
#### Request

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | Token ID |
| internetgateway | Body | Object | O | Internet gateway information object to create |
| internetgateway.name | Body | String | O | Internet gateway name |
| internetgateway.external_network_id | Body | O | UUID | The external network ID for the internet gateway to connect to |

<a id="create-an-internet-gateway-response"></a>
#### Response

| Name | Type | Format | Description |
| --- | --- | --- | --- |
| internetgateway | Body | Array | Internet Gateway Information Object |
| internetgateway.id | Body | UUID | Internet gateway ID |
| internetgateway.name | Body | String | Internet gateway name |
| internetgateway.external_network_id | Body | UUID | The ID of the external network to which the Internet gateway is connected. |
| internetgateway.routingtable_id | Body | UUID | The ID of the routing table that the Internet Gateway is associated with when connected to a routing table |
| internetgateway.state | Body | String | The status of the Internet Gateway. <br>`available`: Operating normally<br>`unavailable`: Not in use; not connected to a routing table<br>`migrating`: Being moved to another internet gateway server for maintenance<br>`ERROR`: Connected to a routing table but not functioning properly|
| internetgateway.create_time | Body | Date | Internet gateway creation time (UTC) |
| internetgateway.tenant_id | Body | String | The tenant ID to which the Internet Gateway belongs |
| internetgateway.migrate_status | Body | String | Processing status when migrating internet gateways due to maintenance<br>`none`: not moving or the move is complete<br>`unbinding_progress`: Status of `unbinding` from existing internet gateway servers<br>`unbinding_error`: Error uninstalling from existing internet gateway server<br>`binding_progress`: Status of configuration on the new internet gateway server<br>`binding_error`: Error configuring on new internet gateway server |
| internetgateway.migrate_error | Body | String | Error messages during the internet gateway migration for maintenance |

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

<a id="delete-an-internet-gateway"></a>
### Delete an Internet gateway { #delete-an-internet-gateway }

Delete the internet gateway. 

```
DELETE /v2.0/internetgateways/{internetgatewayId}
X-Auth-Token: {tokenId}
```

<a id="delete-an-internet-gateway-request"></a>
#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| internetgatewayId | URL | UUID | O | Routing table to delete |
| tokenId | Header | String | O | Token ID |


<a id="delete-an-internet-gateway-response"></a>
#### Response

This API does not return a response body.

---