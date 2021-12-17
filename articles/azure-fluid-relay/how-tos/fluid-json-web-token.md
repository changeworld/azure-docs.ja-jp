---
title: Azure Fluid Relay トークン コントラクト
description: Azure Fluid Relay で使用される JSON Web Token について理解を深めます
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: reference
ms.service: azure-fluid
ms.openlocfilehash: 3fe063a6d5b3c76d300a088372e9dee08d9d79c0
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661622"
---
# <a name="azure-fluid-relay-token-contract"></a>Azure Fluid Relay トークン コントラクト

> [!NOTE]
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。

Azure Fluid Relay に送信される要求には、Authorization ヘッダーに JWT トークンが含まれている必要があります。 このトークンには、[テナント キーによる署名](../concepts/authentication-authorization.md)が必要です。

## <a name="claims"></a>Claims

JWT (JSON Web Token) は、次の 3 つの部分に分かれています。 

- **ヘッダー** - トークンの種類や署名方法に関する情報など、トークンの検証方法に関する情報が提供されます。 
- **ペイロード** - サービスを呼び出そうとしているユーザーまたはアプリに関する重要なデータがすべて含まれています。 
- **署名** - トークンの検証に使用される原材料です。 

各部分はピリオド (.) で区切られ、Base64 で個別にエンコードされます。 

## <a name="header-claims"></a>ヘッダーのクレーム

| 要求 | Format | 説明  |
|-------|--------|--------------|
| alg   | string | トークンの署名に使用されるアルゴリズム。 たとえば、"HS256" |
| typ   | string | この値は常に "JWT" である必要があります。 |

## <a name="payload-claims"></a>ペイロードのクレーム

| 要求      | Format                   | 説明 |
|------------|--------------------------|-------------|
| documentId | string                   | FRS によって生成され、トークンが生成されているドキュメントを識別します。 |
| scope      | string[]                 | クライアントに必要な、ドキュメントまたは概要に対するアクセス許可を識別します。 すべてのスコープについて、クライアントに付与するアクセス許可を定義できます。  |
| tenantId   | string                   | テナントを識別します。 |
| user       | JSON                     | "*省略可能*" `{ displayName: <display_name>, id: <user_id>, name: <user_name>, }` アプリケーションのユーザーを識別します。 これは、オーダリング サービスである Alfred によってアプリケーションに返送されます。  アプリケーションでは、これを使用して、Alfred から取得した応答からユーザーを識別することができます。 Azure Fluid Relay では、この情報は検証されません。 |
| iat        | 数字、UNIX タイムスタンプ | "Issued At" は、このトークンの認証がいつ行われたのかを示します。 |
| exp        | 数字、UNIX タイムスタンプ | "exp" (expiration time) 要求は、JWT の有効期限を示します。これ以降は、その JWT を受け入れて処理することはできません。 トークンの有効期間は 1 時間を超えることはできません。 |
| ver        | string                   | アクセス トークンのバージョンを示します。 `1.0` である必要があります。 |
| jti        | string                   | *省略可。*  "jti" (JWT ID) 要求は、JWT の一意の識別子を提供します。 識別子の値は、同じ値が誤って別のデータ オブジェクトに割り当てられるおそれがほとんどないように割り当てる必要があります。 ドキュメントの作成に同じトークンを使用することによる失敗を回避するために、このクレームを使用することをお勧めします。  |

## <a name="a-sample-azure-fluid-relay-token"></a>Azure Fluid Relay トークンのサンプル

```typescript
{ 
  "alg": "HS256",  
  "typ": "JWT" 
}.{ 
  "documentId": "746c4a6f-f778-4970-83cd-9e21bf88326c", 
  "scopes": [ "doc:read", "doc:write", "summary:write" ],   
  "iat": 1599098963,  
  "exp": 1599098963,  
  "tenantId": "AzureFluidTenantId",  
  "ver": "1.0",
  "jti": "d7cd6602-2179-11ec-9621-0242ac130002"
}.[Signature] 
```

## <a name="how-can-you-generate-an-azure-fluid-relay-token"></a>Azure Fluid Relay トークンを生成する方法 

[jsonwebtoken](https://www.npmjs.com/package/jsonwebtoken) npm パッケージを使用でき、このメソッドを使用してトークンに署名できます。

```typescript
export function getSignedToken(
    tenantId: string,
    documentId: string,
    tokenLifetime: number = 60 * 60,
    ver: string = "1.0") {
        jwt.sign(
            {
                documentId, 
                user: {
                    displayName: "displayName", 
                    id: "userId", 
                    name: "userName" 
                }, 
                scopes: ["doc:read", "doc:write", "summary:write"], 
                iat: Math.round((new Date()).getTime() / 1000), 
                exp: Math.round((new Date()).getTime() / 1000) + tokenLifetime, //set the expiry date based on your needs but max-limit is one hour.
                tenantId, 
                ver,
                jti: uuid(), 
            },
            "<tenant_key>");
    }
```
