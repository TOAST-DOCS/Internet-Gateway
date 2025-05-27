## Network > Internet Gateway > API v2 가이드

API를 사용하려면 API 엔드포인트와 토큰 등이 필요합니다. [API 사용 준비](/Compute/Compute/ko/identity-api/)를 참고하여 API 사용에 필요한 정보를 준비합니다.

인터넷 게이트웨이 API는 `network` 타입 엔드포인트를 이용합니다. 정확한 엔드포인트는 토큰 발급 응답의 `serviceCatalog`를 참조합니다.

| 타입 | 리전 | 엔드포인트 |
|---|---|---|
| network | 한국(판교) 리전<br>한국(평촌) 리전 | https://kr1-api-network-infrastructure.gov-nhncloudservice.com<br>https://kr2-api-network-infrastructure.gov-nhncloudservice.com |

API 응답에 가이드에 명시되지 않은 필드가 나타날 수 있습니다. 이런 필드는 NHN Cloud 내부 용도로 사용되며 사전 공지 없이 변경될 수 있으므로 사용하지 않습니다.

## 인터넷 게이트웨이
### 외부 네트워크 ID 조회하기
인터넷 게이트웨이를 생성할 때, 인터넷 게이트웨이를 통해 연결할 외부 네트워크의 ID를 지정해야 합니다.
사용할 수 있는 외부 네트워크는 [VPC 목록 보기 API](/Network/VPC/ko/public-api-gov/#vpc_1)에 `router:external=true` 쿼리를 지정하여 조회할 수 있습니다.
```
GET /v2.0/vpcs?router:external=true
```

### 인터넷 게이트웨이 목록 보기
사용 가능한 인터넷 게이트웨이 목록을 반환합니다.
```
GET /v2.0/internetgateways
X-Auth-Token: {tokenId}
```

#### 요청
이 API는 요청 본문을 요구하지 않습니다.


| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | 토큰 ID |
| tenant_id | Query | String | - | 조회할 인터넷 게이트웨이가 속한 테넌트 |
| id | Query | UUID | - | 조회할 인터넷 게이트웨이 ID |
| name | Query | String | - | 조회할 인터넷 게이트웨이 이름 |
| external_network_id | Query | UUID | - | 조회할 인터넷 게이트웨이가 연결한 외부 네트워크의 ID |
| routingtable_id | Query | UUID | - | 조회할 인터넷 게이트웨이를 연결한 라우팅 테이블의 ID |

#### 응답

| 이름 | 종류 | 형식 | 설명 |
| --- | --- | --- | --- |
| internetgateways | Body | Array | 인터넷 게이트웨이 정보 객체 목록 |
| internetgateway.id | Body | UUID | 인터넷 게이트웨이 ID |
| internetgateway.name | Body | String | 인터넷 게이트웨이 이름 |
| internetgateway.external_network_id | Body | UUID | 인터넷 게이트웨이가 연결한 외부 네트워크의 ID |
| internetgateway.routingtable_id | Body | UUID | 라우팅 테이블과 연결되어 있을 때 인터넷 게이트웨이를 연결한 라우팅 테이블 ID |
| internetgateway.state | Body | String | 인터넷 게이트웨이의 상태. <br>`available`: 정상 상태<br>`unavailable`: 라우팅 테이블에 연결되지 않은 미사용 상태<br>`migrating`: 점검을 위해 다른 인터넷 게이트웨이 서버로 이동 중인 상태<br>`error`:  라우팅 테이블에 연결되었으나 정상 사용이 불가능한 상태|
| internetgateway.create_time | Body | Date | 인터넷 게이트웨이 생성 시간(UTC) |
| internetgateway.tenant_id | Body | String | 인터넷 게이트웨이가 속한 테넌트 ID |
| internetgateway.migrate_status | Body | String | 점검으로 인한 인터넷 게이트웨이 이동 시 처리 상태<br>`none`: 이동 중이지 않거나 이동이 완료된 상태<br>`unbinding_progress`: 기존 인터넷 게이트웨이 서버에서 제거 중인 상태<br>`unbinding_error`: 기존 인터넷 게이트웨이 서버에서 제거 중 오류 발생<br>`binding_progress`: 신규 인터넷 게이트웨이 서버에서 구성 중인 상태<br>`binding_error`: 신규 인터넷 게이트웨이 서버에서 구성 중 오류 발생 |
| internetgateway.migrate_error | Body | String | 점검으로 인한 인터넷 게이트웨이 이동 중 오류 발생 시 오류 메시지 |

<details><summary>예시</summary>
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

### 인터넷 게이트웨이 보기
지정한 인터넷 게이트웨이를 조회합니다.
```
GET /v2.0/internetgateways/{internetgatewayId}
X-Auth-Token: {tokenId}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| internetgatewayId | URL | UUID | O | 조회할 라우팅 테이블 ID |
| tokenId | Header | String | O | 토큰 ID |

#### 응답

| 이름 | 종류 | 형식 | 설명 |
| --- | --- | --- | --- |
| internetgateway | Body | Array | 인터넷 게이트웨이 정보 객체 |
| internetgateway.id | Body | UUID | 인터넷 게이트웨이 ID |
| internetgateway.name | Body | String | 인터넷 게이트웨이 이름 |
| internetgateway.external_network_id | Body | UUID | 인터넷 게이트웨이가 연결한 외부 네트워크의 ID |
| internetgateway.routingtable_id | Body | UUID | 라우팅 테이블과 연결되어 있을 때 인터넷 게이트웨이를 연결한 라우팅 테이블 ID |
| internetgateway.state | Body | String | 인터넷 게이트웨이의 상태. <br>`available`: 정상 상태<br>`unavailable`: 라우팅 테이블에 연결되지 않은 미사용 상태<br>`migrating`: 점검을 위해 다른 인터넷 게이트웨이 서버로 이동 중인 상태<br>`error`:  라우팅 테이블에 연결되었으나 정상 사용이 불가능한 상태|
| internetgateway.create_time | Body | Date | 인터넷 게이트웨이 생성 시간(UTC) |
| internetgateway.tenant_id | Body | String | 인터넷 게이트웨이가 속한 테넌트 ID |
| internetgateway.migrate_status | Body | String | 점검으로 인한 인터넷 게이트웨이 이동 시 처리 상태<br>`none`: 이동 중이지 않거나 이동이 완료된 상태<br>`unbinding_progress`: 기존 인터넷 게이트웨이 서버에서 제거 중인 상태<br>`unbinding_error`: 기존 인터넷 게이트웨이 서버에서 제거 중 오류 발생<br>`binding_progress`: 신규 인터넷 게이트웨이 서버에서 구성 중인 상태<br>`binding_error`: 신규 인터넷 게이트웨이 서버에서 구성 중 오류 발생 |
| internetgateway.migrate_error | Body | String | 점검으로 인한 인터넷 게이트웨이 이동 중 오류 발생 시 오류 메시지 |

<details><summary>예시</summary>
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

### 인터넷 게이트웨이 생성하기

새로운 인터넷 게이트웨이를 생성합니다.

```
POST /v2.0/internetgateways
X-Auth-Token: {tokenId}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | 토큰 ID |
| internetgateway | Body | Object | O | 생성할 인터넷 게이트웨이 정보 객체 |
| internetgateway.name | Body | String | O | 인터넷 게이트웨이 이름 |
| internetgateway.external_network_id | Body | O | UUID | 인터넷 게이트웨이가 연결할 외부 네트워크 ID |

#### 응답

| 이름 | 종류 | 형식 | 설명 |
| --- | --- | --- | --- |
| internetgateway | Body | Array | 인터넷 게이트웨이 정보 객체 |
| internetgateway.id | Body | UUID | 인터넷 게이트웨이 ID |
| internetgateway.name | Body | String | 인터넷 게이트웨이 이름 |
| internetgateway.external_network_id | Body | UUID | 인터넷 게이트웨이가 연결한 외부 네트워크의 ID |
| internetgateway.routingtable_id | Body | UUID | 라우팅 테이블과 연결되어 있을 때 인터넷 게이트웨이를 연결한 라우팅 테이블 ID |
| internetgateway.state | Body | String | 인터넷 게이트웨이의 상태. <br>`available`: 정상 상태<br>`unavailable`: 라우팅 테이블에 연결되지 않은 미사용 상태<br>`migrating`: 점검을 위해 다른 인터넷 게이트웨이 서버로 이동 중인 상태<br>`error`:  라우팅 테이블에 연결되었으나 정상 사용이 불가능한 상태|
| internetgateway.create_time | Body | Date | 인터넷 게이트웨이 생성 시간(UTC) |
| internetgateway.tenant_id | Body | String | 인터넷 게이트웨이가 속한 테넌트 ID |
| internetgateway.migrate_status | Body | String | 점검으로 인한 인터넷 게이트웨이 이동 시 처리 상태<br>`none`: 이동 중이지 않거나 이동이 완료된 상태<br>`unbinding_progress`: 기존 인터넷 게이트웨이 서버에서 제거 중인 상태<br>`unbinding_error`: 기존 인터넷 게이트웨이 서버에서 제거 중 오류 발생<br>`binding_progress`: 신규 인터넷 게이트웨이 서버에서 구성 중인 상태<br>`binding_error`: 신규 인터넷 게이트웨이 서버에서 구성 중 오류 발생 |
| internetgateway.migrate_error | Body | String | 점검으로 인한 인터넷 게이트웨이 이동 중 오류 발생 시 오류 메시지 |

<details><summary>예시</summary>
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

### 인터넷 게이트웨이 삭제하기

인터넷 게이트웨이를 삭제합니다. 

```
DELETE /v2.0/internetgateways/{internetgatewayId}
X-Auth-Token: {tokenId}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| internetgatewayId | URL | UUID | O | 삭제할 라우팅 테이블 ID |
| tokenId | Header | String | O | 토큰 ID |


#### 응답

이 API는 응답 본문을 반환하지 않습니다.

---