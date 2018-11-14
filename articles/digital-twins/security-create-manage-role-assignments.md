---
title: Azure Digital Twins デバイスの接続と認証について理解する | Microsoft Docs
description: Azure Digital Twins を使用してデバイスを接続および認証する
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lyrana
ms.openlocfilehash: 42c1b0fbb6d87e9ed35d4ecce3971d8512eed4d4
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2018
ms.locfileid: "51012464"
---
# <a name="create-and-manage-role-assignments"></a>ロールの割り当てを作成および管理する

Azure Digital Twins は、ロールベースのアクセス制御 ([RBAC](./security-role-based-access-control.md)) を使用してリソースに対するアクセス権を管理します。

各ロールの割り当ては次のとおりです。

* **オブジェクト識別子**: Azure Active Directory ID、サービス プリンシパル オブジェクト ID、またはドメイン名
* **オブジェクト識別子の型**
* **ロール定義 ID**
* **スペース パス**
* **テナント ID**: ほとんどの場合 Azure Active Directory のテナント ID

## <a name="role-definition-identifiers"></a>ロール定義識別子

次の表に、システム/ロール API のクエリを発行することで取得できる内容を示します。

| **ロール** | **識別子** |
| --- | --- |
| スペース管理者 | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| ユーザー管理者| dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| デバイス管理者 | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| キー管理者 | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| トークン管理者 | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| User | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| サポート スペシャリスト | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| デバイス インストーラー | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| ゲートウェイ デバイス | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |

## <a name="supported-objectidtypes"></a>サポートされている ObjectIdTypes

サポート対象の `ObjectIdTypes`:

* `UserId`
* `DeviceId`
* `DomainName`
* `TenantId`
* `ServicePrincipalId`
* `UserDefinedFunctionId`

## <a name="create-a-role-assignment"></a>役割の割り当ての作成

```plaintext
HTTP POST /api/v1.0/roleassignments
```

| **名前** | **必須** | **種類** | **説明** |
| --- | --- | --- | --- |
| roleId| [はい] |String | ロール定義識別子。 ロール定義とその識別子は、システム API のクエリを実行して見つけます。 |
| objectId | [はい] |String | 関連付けられている型に従って書式設定する必要のある、ロールの割り当てのオブジェクト ID。 `DomainName` ObjectIdType の場合、ObjectId は `“@”` 文字で始まる必要があります。 |
| objectIdType | [はい] |String | ロールの割り当ての型。 この表の次のいずれかの値を指定する必要があります。 |
| tenantId | 多様 | String |テナント識別子。 `DeviceId` および `TenantId` ObjectIdTypes では許可されません。 `UserId` および `ServicePrincipalId` ObjectIdTypes では必須です。 DomainName ObjectIdType では省略可能です。 |
| path* | [はい] | String |`Space` オブジェクトへの完全アクセス パス。 例: `/{Guid}/{Guid}`。 識別子がグラフ全体のロールの割り当てを必要とする場合は、`"/"` を指定します。 この文字はルートの指定ですが、これを使用することはお勧めできません。 常に最小限の特権の原則に従ってください。 |

## <a name="sample-configuration"></a>サンプル構成

この例では、ユーザーには、テナント スペースのフロアへの管理アクセスが必要です。

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : " 0fc863bb-eb51-4704-a312-7d635d70e599",
      "ObjectIdType" : "UserId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/ 091e349c-c0ea-43d4-93cf-6b57abd23a44/ d84e82e6-84d5-45a4-bd9d-006a118e3bab"
    }
  ```

この例では、アプリケーションが、デバイスとセンサーのモック作成のテスト シナリオを実行します。

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : "cabf7acd-af0b-41c5-959a-ce2f4c26565b",
      "ObjectIdType" : "ServicePrincipalId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/"
    }
  ```

ドメインの一部であるすべてのユーザーが、スペース、センサー、およびユーザーの読み取りアクセスを受け取ります。 このアクセスには、対応する関連オブジェクトが含まれます。

  ```JSON
    {
      "RoleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
      "ObjectId" : "@microsoft.com",
      "ObjectIdType" : "DomainName",
      "Path": "/091e349c-c0ea-43d4-93cf-6b57abd23a44"
    }
  ```

GET を使用してロールの割り当てを取得します。

```plaintext
HTTP GET /api/v1/roleassignments?path={path}
```

| **名前** | **含まれる** | **必須** |    **種類** |  **説明** |
| --- | --- | --- | --- | --- |
| Path | Path | True | String | スペースへの完全パス |

DELETE を使用してロールの割り当てを削除します。

```plaintext
HTTP DELETE /api/v1/roleassignments/{id}
```

| **名前** | **含まれる** | **必須** | **種類** | **説明** |
| --- | --- | --- | --- | --- |
| ID | Path | True | String |   ロールの割り当て ID |

## <a name="next-steps"></a>次の手順

Azure Digital Twins のセキュリティの詳細については、[API 認証](./security-authenticating-apis.md)に関するページをご覧ください。
