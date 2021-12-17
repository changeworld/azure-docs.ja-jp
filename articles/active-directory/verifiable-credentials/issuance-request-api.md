---
title: Request Service REST API の発行要求を指定する (プレビュー)
titleSuffix: Azure Active Directory Verifiable Credentials
description: 発行済みの検証可能な資格情報を発行する方法について説明します。
documentationCenter: ''
author: barclayn
manager: karenh444
ms.service: active-directory
ms.topic: reference
ms.subservice: verifiable-credentials
ms.date: 10/08/2021
ms.author: barclayn
ms.openlocfilehash: 40515c37de50379f84475ad5474fb54fbbfa5f0d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440528"
---
# <a name="request-service-rest-api-issuance-specification-preview"></a>Request Service REST API の発行の指定 (プレビュー)

Azure Active Directory (Azure AD) 検証可能な資格情報には、要求サービス REST API が含まれています。 この API を使用すると、資格情報を発行して検証できます。 この記事では、発行要求のための Request Service REST API を指定します。

## <a name="http-request"></a>HTTP 要求

Request Service REST API の発行要求では、次の HTTP メソッドがサポートされています。

| メソッド |メモ  |
|---------|---------|
|POST | この記事で指定されているように、JSON ペイロードを使用します。 |

Request Service REST API の発行要求では、次の HTTP ヘッダーが必要です。

| 方法 |値  |
|---------|---------|
|`Authorization`| アクセス トークンをベアラー トークンとして HTTP 要求の Authorization ヘッダーにアタッチします。 たとえば、「 `Authorization: Bearer <token>` 」のように入力します。|
|`Content-Type`| `Application/json`|

Request Service REST API に対する HTTP POST 要求を作成します。 `{tenantID}` を実際のテナント ID またはテナント名に置き換えます。

```http
https://beta.did.msidentity.com/v1.0/{tenantID}/verifiablecredentials/request
```

次の HTTP 要求では、Request Service REST API に対する要求を示します。

```http
POST https://beta.did.msidentity.com/v1.0/contoso.onmicrosoft.com/verifiablecredentials/request
Content-Type: application/json
Authorization: Bearer  <token>

{
    "includeQRCode": true, 
        "callback": {  
            "url": "https://wwww.contoso.com/vc/callback",  
            "state": "Aaaabbbb11112222", 
            "headers": { 
                "api-key": "an-api-key-can-go-here" 
              }  
        }, 
    ... 
}
```  

Request Service REST API を呼び出すには、次のアクセス許可が必要です。 詳細については、「[アクセス トークンを取得するためのアクセス許可を付与する](verifiable-credentials-configure-tenant.md#grant-permissions-to-get-access-tokens)」を参照してください。

| アクセス許可の種類 | アクセス許可  |
|---------|---------|
| Application | bbb94529-53a3-4be5-a069-7eaf2712b826/.default|

## <a name="issuance-request-payload"></a>発行要求のペイロード

発行要求のペイロードには、検証可能な資格情報の発行要求に関する情報が含まれています。 次の例では、姓や名などのユーザー クレームを含む PIN コード フローを使用し、発行要求を示します。 この要求の結果では、発行プロセスを開始するためのリンクを含む QR コードが返されます。

```json
{
    "includeQRCode": true,
    "callback": {
        "url": "https://www.contoso.com/api/issuer/issuanceCallback",
        "state": "de19cb6b-36c1-45fe-9409-909a51292a9c",
        "headers": {
            "api-key": "OPTIONAL API-KEY for VERIFIER CALLBACK API"
        }
    },
    "authority": "did:ion:EiCLL8lzCqlGLYTGbjwgR6SN6OkIjO6uUKyF5zM7fQZ8Jg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfOTAyZmM2NmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiTEdUOWk3aFYzN1dUcFhHcUg5c1VDek...",
    "registration": {
        "clientName": "Verifiable Credential Expert Sample"
    },
    "issuance": {
        "type": "VerifiedCredentialExpert",
        "manifest": "https://beta.did.msidentity.com/v1.0/12345678-0000-0000-0000-000000000000/verifiableCredential/contracts/VerifiedCredentialExpert",
        "pin": {
            "value": "3539",
            "length": 4
        },
        "claims": {
            "given_name": "Megan",
            "family_name": "Bowen"
        }
    }
}
```

ペイロードには、次のプロパティが含まれます。  

|パラメーター |Type  | 説明 |
|---------|---------|---------|
| `includeQRCode` |  ブール型 |   この要求の応答に QR コードを含まれるかどうかを決定します。 QR コードを提示し、ユーザーにそれをスキャンするよう指示します。 QR コードをスキャンすると、この発行要求で認証アプリが起動されます。 指定できる値は、`true` (既定値) または `false`です。 値を `false` に設定したら、戻された `url` プロパティを使用してディープ リンクを表示します。  |
| `authority` | string|  発行者の分散化識別子 (DID)。 詳細については、「[資格情報と環境の詳細を収集して、サンプル アプリケーションを設定する](verifiable-credentials-configure-issuer.md)」を参照してください。|
| `registration` | [RequestRegistration](#requestregistration-type)|  認証アプリに表示できる発行者に関する情報を提供します。 |
| `issuance` | [RequestIssuance](#requestissuance-type)| 発行要求に関する情報を提供します。  |
|`callback`|  [Callback](#callback-type)| 検証可能な資格情報の発行プロセスの間に、開発者がフローに関する情報を非同期に取得できるようにします。 たとえば、ユーザーが QR コードをスキャンしたときに、開発者は呼び出しを必要とする場合があります。|

### <a name="requestregistration-type"></a>RequestRegistration 型

`RequestRegistration` 型により、発行者の情報登録が提供されます。 `RequestRegistration` 型には次のプロパティが含まれます。

|プロパティ |Type |説明 |
|---------|---------|---------|
| `clientName` | string|  検証可能な資格情報の発行者の表示名。  |
| `logoUrl` |  string |  省略可能。 発行者のロゴの URL。  |
| `termsOfServiceUrl` |  string | 省略可能。 発行する検証可能な資格情報の使用条件の URL。  |

> [!NOTE]
> 現時点では、`RequestRegistration` の情報は、Microsoft Authenticator アプリでの発行中に提示されません。 ただし、この情報は、ペイロードで使用できます。

### <a name="requestissuance-type"></a>RequestIssuance 型

`RequestIssuance` 型は、検証可能な資格情報の発行に必要な情報を提供します。 現在、`RequestIssuance` で送信できる入力には 3 つの種類があります。 Azure AD の検証可能な資格情報では、これらの種類を使用して、検証可能な資格情報にクレームを挿入し、その情報を発行者 DID で構成証明するために使用されます。 次の 3 つの種類があります。

- ID トークン
- 検証可能なプレゼンテーションを使用した検証可能な資格情報
- 自己証明クレーム

入力の種類の詳細については、[検証可能な資格情報のカスタマイズ](credential-design.md)に関する記事を参照してください。 

`RequestIssuance` 型には次のプロパティが含まれます。

|プロパティ |Type |説明 |
|---------|---------|---------|
| `type` |  string |  検証可能な資格情報の種類。 検証可能な資格情報のマニフェストで定義されている種類と一致している必要があります。 例: `VerifiedCredentialExpert`。 詳細については、「[Azure で検証された資格情報エキスパート カードを作成する](verifiable-credentials-configure-issuer.md)」を参照してください。 |
| `manifest` | string| 検証可能な資格情報のマニフェスト ドキュメントの URL。 詳細については、「[資格情報と環境の詳細を収集して、サンプル アプリケーションを設定する](verifiable-credentials-configure-issuer.md)」を参照してください。|
| `claims` | string| 省略可能。 検証可能な資格情報でサブジェクトに関して行われたアサーションのコレクションを含めます。 PIN コード フローの場合は、ユーザーの姓と名を提供することが重要です。 詳細については、「[検証可能な資格情報の名前](verifiable-credentials-configure-issuer.md#verifiable-credential-names)」を参照してください。 |
| `pin` | [PIN](#pin-type)| 省略可能。 発行の間に追加のセキュリティを提供するための PIN 番号。 PIN コード フローの場合、このプロパティは必須です。 PIN コードを生成し、アプリのユーザーにそれを提示します。 ユーザーは、生成した PIN コードを提供する必要があります。 |

### <a name="pin-type"></a>PIN の種類

`pin` 型は、発行の一部として表示できる PIN コードを定義します。 `pin` は省略可能であり、使用する場合は、常に帯域外で送信される必要があります。 HASH PIN コードを使用する場合は、`salt`、`alg`、`iterations` の各プロパティを定義する必要があります。 `pin` には次のプロパティが含まれます。

|プロパティ |Type |説明 |
|---------|---------|---------|
| `value` | string| プレーンテキストで PIN の値を格納します。 ハッシュされた PIN を使用している場合、value プロパティには、base64 でエンコードされたソルト化されたハッシュが含まれます。|
| `type` | string|  PIN コードの型。 指定できる値: `numeric` (既定値)。 |
| `length` | 整数 (integer)|  PIN コードの長さ。 既定の長さは 6、最小値は 4、最大値は 16 です。|
| `salt` | string|  ハッシュされた PIN コードのソルト。 ソルトは、ハッシュ計算中に先頭に付加されます。 エンコード: UTF-8。 |
| `alg` | string|  ハッシュ化された PIN のハッシュ アルゴリズム。 サポートされるアルゴリズム: `sha256`。 |
| `iterations` | 整数 (integer)| ハッシュの反復の数。 指定できる値: `1`。|

### <a name="callback-type"></a>コールバックの種類

Request Service REST API により、コールバック エンドポイントに対する複数のイベントが生成されます。 それらのイベントを使用すると、UI を更新し、結果がアプリケーションに返されたらプロセスを続行できます。 `Callback` 型には次のプロパティが含まれます。

|プロパティ |Type |説明 |
|---------|---------|---------|
| `url` | string| アプリケーションのコールバック エンドポイントへの URI。 |
| `state` | string| 元のペイロードで渡された状態と関連付けます。 |
| `headers` | string| 省略可能。 POST メッセージの受信側で必要な HTTP ヘッダーのコレクションを含めることができます。 ヘッダーには、`api-key` または承認に必要なヘッダーのみが含まれる必要があります。|

## <a name="successful-response"></a>成功応答

成功した場合、このメソッドからは、応答コード (HTTP 201 Created) と、応答本文内のイベント オブジェクトのコレクションが返されます。 次の JSON は、成功した応答を示しています。

```json
{  
    "requestId": :"799f23ea-5241-45af-99ad-cf8e5018814e",  
    "url": "openid://vc?request_uri=https://beta.did.msidentity.com/v1.0/12345678-0000-0000-0000-000000000000/verifiablecredentials/request/178319f7-20be-4945-80fb-7d52d47ae82e",  
    "expiry": 1622227690,  
    "qrCode": "data:image/png;base64,iVBORw0KggoA<SNIP>"  
} 
```

応答には次のプロパティが含まれます。

|プロパティ |Type |説明 |
|---------|---------|---------|
| `requestId`| string | 自動生成された関連付け ID。 [コールバック](#callback-events)は同じ要求を使用し、発行要求とそのコールバックを追跡できるようにします。 |
| `url`|  string| 認証アプリを起動し、発行プロセスを開始する URL。 ユーザーが QR コードをスキャンできない場合は、この URL を提示できます。 |
| `expiry`| 整数 (integer)| 応答の有効期限がいつ切れるかを示します。 |
| `qrCode`| string | ユーザーがスキャンして発行フローを開始できる QR コード。 |

アプリで応答を受信したら、QR コードをユーザーに提示する必要があります。 ユーザーが QR コードをスキャンすると、発行プロセスを開始する認証アプリが開きます。

## <a name="error-response"></a>エラー応答

アプリでエラーを適切に処理できるように、エラー応答を返すこともできます。 次の JSON は、未承認エラーのメッセージを示します。


```json
{
    "requestId": "d60b068e7fbd975896e179b99347866a",
    "date": "Wed, 29 Sep 2021 21:49:00 GMT",
    "error": {
        "code": "unauthorized",
        "message": "Failed to authenticate the request."
    }
}
```

応答には次のプロパティが含まれます。

|プロパティ |Type |説明 |
|---------|---------|---------|
| `requestId`| string | 自動生成された要求 ID。|
| `date`| date| エラーの時刻。 |
| `error.code` | string| 戻りエラー コード。 |
| `error.message`| string| エラー メッセージ。 |

## <a name="callback-events"></a>コールバック イベント

ユーザーが QR コードをスキャンするか、認証アプリのディープ リンクを使用するか、発行プロセスを完了すると、コールバック エンドポイントが呼び出されます。 

|プロパティ |Type |説明 |
|---------|---------|---------|
| `requestId`| string | ペイロードが検証可能な資格情報サービスにポストされるときに、元の要求にマップされます。|
| `code` |string |認証アプリによって要求が取得されたときに返されるコード。 指定できる値 <ul><li>`request_retrieved` ユーザーが QR コードをスキャンするか、発行フローを開始するリンクを選択しました。</li><li>`issuance_successful`: 検証可能な資格情報の発行が成功しました。</li><li>`Issuance_error`: 発行中にエラーが発生しました。 詳細については、`error` プロパティを参照してください。</li></ul>    |
| `state` |string| 元のペイロードで渡した状態値が返されます。   |
| `error`| error | `code` プロパティ値が `Issuance_error` の場合、このプロパティにはエラーに関する情報が含まれます。| 
| `error.code` | string| 戻りエラー コード。 |
| `error.message`| string| エラー メッセージ。 |

次の例は、認証アプリが発行要求を開始するときのコールバック ペイロードを示しています。

```json
{  
    "requestId":"aef2133ba45886ce2c38974339ba1057",  
    "code":"request_retrieved",  
    "state":"Wy0ThUz1gSasAjS1"
} 
```

次の例では、ユーザーが発行プロセスを正常に完了した後のコールバックのペイロードを示します。

```json
{  
    "requestId":"87e1cb24-9096-409f-81cb-9e645f23a4ba",
    "code":"issuance_successful",
    "state":"f3d94e35-ca5f-4b1b-a7d7-a88caa05e322",
} 
```

### <a name="callback-errors"></a>コールバック エラー  

コールバック エンドポイントは、エラー メッセージと一緒に呼び出される場合があります。 次の表に、エラー コードの一覧を示します。

|Message  |定義    |
|---------|---------|
| `fetch_contract_error*`| 検証可能な資格情報のコントラクトをフェッチできません。 このエラーは、通常、要求のペイロードの [RequestIssuance オブジェクト](#requestissuance-type)で指定されているマニフェストを API でフェッチできない場合に発生します。|
| `issuance_service_error*` | 検証可能な資格情報サービスは、要件を検証できないか、検証可能な資格情報で問題が発生しました。|
| `unspecified_error`| このエラーは一般的ではありませんが、調査する価値があります。 |

次の例では、エラーが発生したときのコールバックのペイロードを示します。

```json
{  
    "requestId":"87e1cb24-9096-409f-81cb-9e645f23a4ba",  
    "code": "issuance_error",  
    "state":"f3d94e35-ca5f-4b1b-a7d7-a88caa05e322",  
    "error": { 
      "code":"IssuanceFlowFailed", 
      "message":"issuance_service_error”, 
    } 
} 
``` 

## <a name="next-steps"></a>次の手順

[Request Service REST API を呼び出す方法](get-started-request-api.md)を確認します。
