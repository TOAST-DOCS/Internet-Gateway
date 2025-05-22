
## Network > Internet Gateway > API v2ガイド

APIを使用するには、APIエンドポイントとトークンなどが必要です。 [API使用準備](/Compute/Compute/ja/identity-api/)を参考にしてAPI使用に必要な情報を準備します。

インターネットゲートウェイAPIは`network`タイプエンドポイントを利用します。正確なエンドポイントはトークン発行レスポンスの`serviceCatalog`を参照します。

| タイプ | リージョン | エンドポイント |
|---|---|---|
| network | 韓国(パンギョ)リージョン<br>韓国(ピョンチョン)リージョン<br>日本(東京)リージョン<br>米国(カリフォルニア)リージョン | https://kr1-api-network-infrastructure.nhncloudservice.com<br>https://kr2-api-network-infrastructure.nhncloudservice.com<br>https://jp1-api-network-infrastructure.nhncloudservice.com<br>https://us1-api-network-infrastructure.nhncloudservice.com |

APIレスポンスにガイドに記載されていないフィールドが表示される場合があります。このようなフィールドは、NHN Cloudの内部用途に使用され、事前告知なしに変更される可能性があるため、使用しないでください。

## インターネットゲートウェイ
### 外部ネットワークIDを照会する
インターネットゲートウェイを作成する際、インターネットゲートウェイを介して接続する外部ネットワークのIDを指定する必要があります。
使用可能な外部ネットワークは、[VPCリスト表示API](/Network/VPC/ja/public-api/#vpc_1)に`router:external=true`クエリを指定して照会できます。

```
GET /v2.0/vpcs?router:external=true
```

### インターネットゲートウェイリスト表示
利用可能なインターネットゲートウェイのリストを返します。

```
GET /v2.0/internetgateways
X-Auth-Token: {tokenId}
```

#### リクエスト
このAPIはリクエスト本文を要求しません。


| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | トークンID |
| tenant_id | Query | String | - | 照会するインターネットゲートウェイが属するテナント |
| id | Query | UUID | - | 照会するインターネットゲートウェイID |
| name | Query | String | - | 照会するインターネットゲートウェイ名 |
| external_network_id | Query | UUID | - | 照会するインターネットゲートウェイが接続した外部ネットワークのID |
| routingtable_id | Query | UUID | - | 照会するインターネットゲートウェイを接続したルーティングテーブルのID |

#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
| --- | --- | --- | --- |
| internetgateways | Body | Array | インターネットゲートウェイ情報オブジェクトリスト |
| internetgateway.id | Body | UUID | インターネットゲートウェイID |
| internetgateway.name | Body | String | インターネットゲートウェイ名 |
| internetgateway.external_network_id | Body | UUID | インターネットゲートウェイが接続した外部ネットワークのID |
| internetgateway.routingtable_id | Body | UUID | ルーティングテーブルと接接続されているときにインターネットゲートウェイを接続したルーティングテーブルID |
| internetgateway.state | Body | String | インターネットゲートウェイの状態。 <br>`available`:正常状態<br>`unavailable`:ルーティングテーブルに接続されていない未使用状態<br>`migrating`:点検のために他のインターネットゲートウェイサーバーに移動中の状態<br>`error`:ルーティングテーブルに接続されているが、正常な使用ができない状態|
| internetgateway.create_time | Body | Date | インターネットゲートウェイ作成時間(UTC) |
| internetgateway.tenant_id | Body | String | インターネットゲートウェイが属するテナントID |
| internetgateway.migrate_status | Body | String | 点検によるインターネットゲートウェイ移行時の処理状態<br>`none`:移動中ではないか移動が完了した状態<br>`unbinding_progress`:既存インターネットゲートウェイサーバーから削除中の状態<br>`unbinding_error`:既存インターネットゲートウェイサーバーから削除中にエラーが発生<br>`binding_progress`:新規インターネットゲートウェイサーバーで構成中の状態<br>`binding_error`:新規インターネットゲートウェイサーバーで構成中にエラー発生 |
| internetgateway.migrate_error | Body | String | 点検によるインターネットゲートウェイの移動中にエラーが発生した場合のエラーメッセージ |

<details><summary>例</summary>

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


### インターネットゲートウェイ表示
指定したインターネットゲートウェイを照会します。

```
GET /v2.0/internetgateways/{internetgatewayId}
X-Auth-Token: {tokenId}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| internetgatewayId | URL | UUID | O | 照会するルーティングテーブルID |
| tokenId | Header | String | O | トークンID |

#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
| --- | --- | --- | --- |
| internetgateway | Body | Array | インターネットゲートウェイ情報オブジェクト |
| internetgateway.id | Body | UUID | インターネットゲートウェイID |
| internetgateway.name | Body | String | インターネットゲートウェイ名 |
| internetgateway.external_network_id | Body | UUID | インターネットゲートウェイが接続した外部ネットワークのID |
| internetgateway.routingtable_id | Body | UUID | ルーティングテーブルと接接続されているときにインターネットゲートウェイを接続したルーティングテーブルID |
| internetgateway.state | Body | String | インターネットゲートウェイの状態。 <br>`available`:正常状態<br>`unavailable`:ルーティングテーブルに接続されていない未使用状態<br>`migrating`:点検のために他のインターネットゲートウェイサーバーに移動中の状態<br>`error`:ルーティングテーブルに接続されているが、正常な使用ができない状態|
| internetgateway.create_time | Body | Date | インターネットゲートウェイ作成時間(UTC) |
| internetgateway.tenant_id | Body | String | インターネットゲートウェイが属するテナントID |
| internetgateway.migrate_status | Body | String | 点検によるインターネットゲートウェイ移行時の処理状態<br>`none`:移動中ではないか移動が完了した状態<br>`unbinding_progress`:既存インターネットゲートウェイサーバーから削除中の状態<br>`unbinding_error`:既存インターネットゲートウェイサーバーから削除中にエラーが発生<br>`binding_progress`:新規インターネットゲートウェイサーバーで構成中の状態<br>`binding_error`:新規インターネットゲートウェイサーバーで構成中にエラー発生 |
| internetgateway.migrate_error | Body | String | 点検によるインターネットゲートウェイの移動中にエラーが発生した場合のエラーメッセージ |

<details><summary>例</summary>=
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

### インターネットゲートウェイの作成

新しいインターネットゲートウェイを作成します。

```
POST /v2.0/internetgateways
X-Auth-Token: {tokenId}
```


#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | トークンID |
| internetgateway | Body | Object | O | 作成するインターネットゲートウェイ情報オブジェクト |
| internetgateway.name | Body | String | O | インターネットゲートウェイ名 |
| internetgateway.external_network_id | Body | O | UUID | インターネットゲートウェイが接続する外部ネットワークID |

#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
| --- | --- | --- | --- |
| internetgateway | Body | Array | インターネットゲートウェイ情報オブジェクト |
| internetgateway.id | Body | UUID | インターネットゲートウェイID |
| internetgateway.name | Body | String | インターネットゲートウェイ名 |
| internetgateway.external_network_id | Body | UUID | インターネットゲートウェイが接続した外部ネットワークのID |
| internetgateway.routingtable_id | Body | UUID | ルーティングテーブルと接接続されているときにインターネットゲートウェイを接続したルーティングテーブルID |
| internetgateway.state | Body | String | インターネットゲートウェイの状態。 <br>`available`:正常状態<br>`unavailable`:ルーティングテーブルに接続されていない未使用状態<br>`migrating`:点検のために他のインターネットゲートウェイサーバーに移動中の状態<br>`error`:ルーティングテーブルに接続されているが、正常な使用ができない状態|
| internetgateway.create_time | Body | Date | インターネットゲートウェイ作成時間(UTC) |
| internetgateway.tenant_id | Body | String | インターネットゲートウェイが属するテナントID |
| internetgateway.migrate_status | Body | String | 点検によるインターネットゲートウェイ移行時の処理状態<br>`none`:移動中ではないか移動が完了した状態<br>`unbinding_progress`:既存インターネットゲートウェイサーバーから削除中の状態<br>`unbinding_error`:既存インターネットゲートウェイサーバーから削除中にエラーが発生<br>`binding_progress`:新規インターネットゲートウェイサーバーで構成中の状態<br>`binding_error`:新規インターネットゲートウェイサーバーで構成中にエラー発生 |
| internetgateway.migrate_error | Body | String | 点検によるインターネットゲートウェイの移動中にエラーが発生した場合のエラーメッセージ |

<details><summary>例</summary>

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

### インターネットゲートウェイの削除

インターネットゲートウェイを削除します。 

```
DELETE /v2.0/internetgateways/{internetgatewayId}
X-Auth-Token: {tokenId}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| internetgatewayId | URL | UUID | O | 削除するルーティングテーブルID |
| tokenId | Header | String | O | トークンID |


#### レスポンス

このAPIはレスポンス本文を返しません。

---
