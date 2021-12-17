---
title: Request Service REST API の検証要求を指定する (プレビュー)
titleSuffix: Azure Active Directory Verifiable Credentials
description: 検証可能な資格情報の提示要求を開始する方法について説明します
documentationCenter: ''
author: barclayn
manager: karenh444
ms.service: active-directory
ms.topic: reference
ms.subservice: verifiable-credentials
ms.date: 10/08/2021
ms.author: barclayn
ms.openlocfilehash: c670f060e7849f844997c0feefd60229b90e5202
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131474005"
---
# <a name="request-service-rest-api-presentation-specification-preview"></a>Request Service REST API の提示の仕様 (プレビュー)

Azure Active Directory (Azure AD) 検証可能な資格情報には、要求サービス REST API が含まれています。 この API を使用すると、資格情報を発行して検証できます。 この記事では、提示要求のための Request Service REST API を指定します。 提示要求を使用すると、ユーザーに検証可能な資格情報の提示を求め、その資格情報を検証することができます。

## <a name="http-request"></a>HTTP 要求

Request Service REST API の提示要求では、次の HTTP メソッドがサポートされています。

| メソッド |メモ  |
|---------|---------|
|POST | この記事で指定されているように、JSON ペイロードを使用します。 |

Request Service REST API の提示要求では、次の HTTP ヘッダーが必要です。

| 方法 |値  |
|---------|---------|
|`Authorization`| アクセス トークンをベアラー トークンとして HTTP 要求の Authorization ヘッダーにアタッチします。 たとえば、「 `Authorization: Bearer <token>` 」のように入力します。|
|`Content-Type`| `Application/json`|

Request Service REST API に対する HTTP POST 要求を作成します。 `{tenantID}` を実際のテナント ID またはテナント名に置き換えます。

```http
https://beta.did.msidentity.com/v1.0/{tenantID}/verifiablecredentials/request
```

次の HTTP 要求では、Request Service REST API に対する提示要求を示します。

```http
POST https://beta.did.msidentity.com/v1.0/contoso.onmicrosoft.com/verifiablecredentials/request
Content-Type: application/json
Authorization: Bearer  <token>

{  
    "includeQRCode": true,  
    "callback": {  
    "url": "https://www.contoso.com/api/verifier/presentationCallbac",  
    "state": "11111111-2222-2222-2222-333333333333",  
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

## <a name="presentation-request-payload"></a>提示要求ペイロード

提示要求ペイロードには、検証可能な資格情報の提示要求に関する情報が含まれています。 次の例は、特定の発行者からの提示要求を示しています。 この要求の結果として、提示プロセスを開始するためのリンクを含む QR コードが返されます。

```json
{
  "includeQRCode": true,
  "callback": {
    "url": "https://www.contoso.com/api/verifier/presentationCallback",
    "state": "92d076dd-450a-4247-aa5b-d2e75a1a5d58",
    "headers": {
      "api-key": "OPTIONAL API-KEY for VERIFIER CALLBACK API"
    }
  },
  "authority": "did:ion:EiCLL8lzCqlGLYTGbjwgR6SN6OkIjO6uUKyF5zM7fQZ8Jg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfOTAyZmM2NmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiTEdUOWk3aFYzN1dUcFhHcUg5c1VDekxTVlFWcVl3S2JNY1Fsc0RILUZJUSIsInkiOiJiRWo5MDY...",
  "registration": {
    "clientName": "Veritable Credential Expert Verifier"
  },
  "presentation": {
    "includeReceipt": true,
    "requestedCredentials": [
      {
        "type": "VerifiedCredentialExpert",
        "purpose": "So we can see that you a veritable credentials expert",
        "acceptedIssuers": [
          "did:ion:EiCLL8lzCqlGLYTGbjwgR6SN6OkIjO6uUKyF5zM7fQZ8Jg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfOTAyZmM2NmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiTEdUOWk3aFYzN1dUcFhHcUg5c1VDekxTVlFWcVl3S2JNY1Fsc0RILUZJUSIsInkiO..."
        ]
      }
    ]
  }
}
```

ペイロードには、次のプロパティが含まれます。  

|パラメーター |Type  | 説明 |
|---------|---------|---------|
| `includeQRCode` |  ブール型 |   この要求の応答に QR コードを含まれるかどうかを決定します。 QR コードを提示し、ユーザーにそれをスキャンするよう指示します。 QR コードをスキャンすると、この提示要求で認証アプリが起動されます。 指定できる値は、`true` (既定値) または `false`です。 値を `false` に設定したら、戻された `url` プロパティを使用してディープ リンクを表示します。  |
| `authority` | string|  検証者の Azure AD テナントの分散型識別子 (DID) です。 詳細については、「[テナントの詳細を収集してサンプル アプリケーションを設定する](verifiable-credentials-configure-verifier.md#gather-tenant-details-to-set-up-your-sample-application)」を参照してください。|
| `registration` | [RequestRegistration](#requestregistration-type)|  検証者に関する情報を提供します。 |
| `presentation` | [RequestPresentation](#requestpresentation-type)| 検証可能な資格情報の提示要求に関する情報を提供します。  |
|`callback`|  [Callback](#callback-type)| 検証可能な資格情報の提示プロセス中に、開発者が UI を更新できるようにします。 ユーザーがこのプロセスを完了して、結果がアプリケーションに返されたら、プロセスを続行します。|

### <a name="requestregistration-type"></a>RequestRegistration 型

`RequestRegistration` 型により、発行者の情報登録が提供されます。 `RequestRegistration` 型には次のプロパティが含まれます。

|プロパティ |Type |説明 |
|---------|---------|---------|
| `clientName` | string|  検証可能な資格情報の発行者の表示名。 この名前は、認証アプリのユーザーに表示されます。 |

次のスクリーンショットは、提示要求の `clientName` プロパティと `authority` (検証者) の表示名を示しています。

![提示要求を承認する方法を示すスクリーンショット。](media/presentation-request-api/approve-presentation-request.jpg)

### <a name="requestpresentation-type"></a>RequestPresentation 型

`RequestPresentation` 型は、検証可能な資格情報の提示に必要な情報を提供します。 `RequestPresentation` には次のプロパティが含まれます。

|プロパティ |Type |説明 |
|---------|---------|---------|
| `includeReceipt` |  ブール型 | この要求の応答に受信確認を含めるかどうかを決定します。 指定できる値は `true` または `false` (既定値) です。 受信確認には、認証子から検証可能な資格情報サービスに送信された元のペイロードが含まれます。 受信確認はトラブルシューティングに役立ちます。既定では設定しないでください。 `OpenId Connect SIOP` 要求の場合、受信確認には元の要求の ID トークンが含まれます。 |
| `requestedCredentials` | collection| [RequestCredential](#requestcredential-type) オブジェクトのコレクション。|

### <a name="requestcredential-type"></a>RequestCredential 型

`RequestCredential` により、ユーザーが指定する必要のある要求された資格情報に関する情報が提供されます。 `RequestCredential` には次のプロパティが含まれます。

|プロパティ |Type |説明 |
|---------|---------|---------|
| `type`| string| 検証可能な資格情報の種類。 `type` は、`issuer` の検証可能な資格情報のマニフェスト (`VerifiedCredentialExpert` など) で定義されている種類と一致している必要があります。 発行者マニフェストを取得するには、「[資格情報と環境の詳細を収集してサンプル アプリケーションを設定する](verifiable-credentials-configure-issuer.md)」をご覧ください。 **[Issue credential URL]\(資格情報 URL の発行\)** をコピーして Web ブラウザーで開き、**id** プロパティを確認します。 |
| `purpose`| string | この検証可能な資格情報を要求する目的についての情報を提供します。 |
| `acceptedIssuers`| 文字列コレクション | サブジェクトで提示できる検証可能な資格情報の種類を発行できる発行者の DID のコレクション。 発行者 DID を取得するには、「[資格情報と環境の詳細を収集してサンプル アプリケーションを設定する](verifiable-credentials-configure-issuer.md)」を参照し、**分散型識別子 (DID)** の値をコピーします。 |

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
    "requestId": "e4ef27ca-eb8c-4b63-823b-3b95140eac11",
    "url": "openid://vc/?request_uri=https://beta.did.msidentity.com/v1.0/87654321-0000-0000-0000-000000000000/verifiablecredentials/request/e4ef27ca-eb8c-4b63-823b-3b95140eac11",
    "expiry": 1633017751,
    "qrCode": "data:image/png;base64,iVBORw0KGgoA<SNIP>"
} 
```

応答には次のプロパティが含まれます。

|プロパティ |Type |説明 |
|---------|---------|---------|
| `requestId`| string | 自動生成された関連付け ID。 [コールバック](#callback-events)は同じ要求を使用し、提示要求とそのコールバックを追跡できるようにします。 |
| `url`|  string| 認証アプリを起動し、提示プロセスを開始する URL。 ユーザーが QR コードをスキャンできない場合は、この URL を提示できます。 |
| `expiry`| 整数 (integer)| 応答の有効期限がいつ切れるかを示します。 |
| `qrCode`| string | ユーザーがスキャンして提示フローを開始することができる QR コード。 |

アプリで応答を受信したら、QR コードをユーザーに提示する必要があります。 ユーザーが QR コードをスキャンすると、認証アプリが開き、提示プロセスを開始されます。

## <a name="error-response"></a>エラー応答

アプリでエラーを適切に処理できるように、エラー応答を返すこともできます。 次の JSON は、未承認エラーのメッセージを示します。


```json
{
    "requestId": "fb888ac646c96083de83b099b2678de0",
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
| `date`| date | エラーの時刻。 |
| `error.code` | string | 戻りエラー コード。 |
| `error.message`| string | エラー メッセージ。 |

## <a name="callback-events"></a>コールバック イベント

ユーザーが QR コードをスキャンするか、認証アプリのディープ リンクを使用するか、提示プロセスを完了すると、コールバック エンドポイントが呼び出されます。 

|プロパティ |Type |説明 |
|---------|---------|---------|
| `requestId`| string | ペイロードが検証可能な資格情報サービスにポストされるときに、元の要求にマップされます。|
| `code` |string |認証アプリによって要求が取得されたときに返されるコード。 指定できる値 <ul><li>`request_retrieved`: ユーザーが QR コードをスキャンするか、提示フローを開始するリンクを選択しました。</li><li>`presentation_verified`: 検証可能な資格情報の検証が正常に完了しました。</li></ul>    |
| `state` |string| 元のペイロードで渡した状態値が返されます。   |
| `subject`|string | 検証可能な資格情報ユーザー DID。|
| `issuers`| array |要求された検証可能な資格情報の配列を返します。 検証可能な資格情報ごとに、以下が提供されます。 </li><li>検証可能な資格情報の種類。</li><li>取得された要求。</li><li>検証可能な資格情報の発行者のドメイン。 </li><li>検証可能な資格情報の発行者のドメイン検証状態。 </li></ul> |
| `receipt`| string | 省略可能。 受信確認には、認証子から検証可能な資格情報に送信された元のペイロードが含まれます。  |

次の例は、認証アプリがプレゼンテーション要求を開始するときのコールバック ペイロードを示しています。

```json
{  
    "requestId":"aef2133ba45886ce2c38974339ba1057",  
    "code":"request_retrieved",  
    "state":"Wy0ThUz1gSasAjS1"
} 
```

次の例は、検証可能な資格情報の提示が正常に完了した後のコールバックのペイロードを示しています。

```json
{
  "requestId": "87e1cb24-9096-409f-81cb-9e645f23a4ba",
  "code": "presentation_verified",
  "state": "f3d94e35-ca5f-4b1b-a7d7-a88caa05e322",
  "subject": "did:ion:EiAlrenrtD3Lsw0GlbzS1O2YFdy3Xtu8yo35W<SNIP>…",
  "issuers": [
    {
      "type": [
        "VerifiableCredential",
        "VerifiedCredentialExpert"
      ],
      "claims": {
        "firstName": "John",
        "lastName": "Doe"
      },
      "domain": "https://contoso.com/",
      "verified": "DNS",
      "issuer": "did:ion:….."
    }
  ],
  "receipt": {
    "id_token": "eyJraWQiOiJkaWQ6aW<SNIP>"
  }
} 
```

## <a name="next-steps"></a>次の手順

[Request Service REST API を呼び出す方法](get-started-request-api.md)を確認します。
