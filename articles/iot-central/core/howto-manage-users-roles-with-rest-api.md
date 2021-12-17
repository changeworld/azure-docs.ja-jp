---
title: Azure IoT Central 内で REST API を使用してユーザーとロールを管理する
description: IoT Central REST API を使用してアプリケーション内でユーザーとロールを管理する方法
author: dominicbetts
ms.author: dobett
ms.date: 08/30/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: d9892efecf010ea698e9f628cd92308e9ac8608b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131078505"
---
# <a name="how-to-use-the-iot-central-rest-api-to-manage-users-and-roles"></a>IoT Central REST API を使用してユーザーとロールを管理する方法

IoT Central REST API を使用して、IoT Central アプリケーションと統合するクライアント アプリケーションを開発できます。 REST API を使用すると、ご自身の IoT Central アプリケーション内でユーザーとロールを管理することができます。

すべての IoT Central REST API 呼び出しに承認ヘッダーが必要です。 詳細については、「[IoT Central REST API 呼び出しを認証および承認する方法](howto-authorize-rest-api.md)」を参照してください。

IoT Central REST API のリファレンス ドキュメントについては、「[Azure IoT Central REST API リファレンス](/rest/api/iotcentral/)」をご覧ください。

> [!TIP]
> [プレビュー API](/rest/api/iotcentral/1.1-previewdataplane/users) には、新しい[組織機能](howto-create-organizations.md)のサポートが含まれています。

## <a name="manage-roles"></a>ロールの管理

REST API を使用すると、お使いの IoT Central アプリケーション内で定義されているロールのリストを表示できます。 お使いのアプリケーションからロール ID のリストを取得するには、次の要求を使用します。

```http
GET https://{your app subdomain}.azureiotcentral.com/api/roles?api-version=1.0
```

この要求に対する応答は次の例のようになります。これには 3 つの組み込みロールとカスタム ロールが含まれます。

```json
{
  "value": [
    {
      "id": "ca310b8d-2f4a-44e0-a36e-957c202cd8d4",
      "displayName": "Administrator"
    },
    {
      "id": "ae2c9854-393b-4f97-8c42-479d70ce626e",
      "displayName": "Operator"
    },
    {
      "id": "344138e9-8de4-4497-8c54-5237e96d6aaf",
      "displayName": "Builder"
    },
    {
      "id": "16f8533f-6b82-478f-8ba8-7e676b541b1b",
      "displayName": "Example custom role"
    }
  ]
}
```

## <a name="manage-users"></a>ユーザーの管理

REST API を使用すると、次を行うことができます。

- アプリケーション内のユーザーを一覧表示する
- 個々のユーザーの詳細を取得する
- ユーザーを作成する
- ユーザーを変更する
- ユーザーの削除

### <a name="list-users"></a>List users

お使いのアプリケーションからユーザーのリストを取得するには、次の要求を使用します。

```http
GET https://{your app subdomain}.azureiotcentral.com/api/users?api-version=1.0
```

この要求に対する応答は、次の例のようになります。 ロールの値により、ユーザーが関連付けられているロール ID が特定されます。

```json
{
  "value": [
    {
      "id": "91907508-04fe-4349-91b5-b872f3055a95",
      "type": "email",
      "roles": [
        {
          "role": "ca310b8d-2f4a-44e0-a36e-957c202cd8d4"
        }
      ],
      "email": "user1@contoso.com"
    },
    {
      "id": "dc1c916b-a652-49ea-b128-7c465a54c759",
      "type": "email",
      "roles": [
        {
          "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
        }
      ],
      "email": "user2@contoso.com"
    },
    {
      "id": "3ab9375e-d2d9-42da-b419-6ae86a938321",
      "type": "email",
      "roles": [
        {
          "role": "344138e9-8de4-4497-8c54-5237e96d6aaf"
        }
      ],
      "email": "user3@contoso.com"
    },
    {
      "id": "fc5a250b-83fb-433d-892c-e0a144f68c2b",
      "type": "email",
      "roles": [
        {
          "role": "16f8533f-6b82-478f-8ba8-7e676b541b1b"
        }
      ],
      "email": "user4@contoso.com"
    }
  ]
}
```

### <a name="get-a-user"></a>ユーザーの取得

お使いのアプリケーションから個々のユーザーの詳細を取得するには、次の要求を使用します。

```http
GET https://{your app subdomain}.azureiotcentral.com/api/users/dc1c916b-a652-49ea-b128-7c465a54c759?api-version=1.0
```

この要求に対する応答は、次の例のようになります。 ロールの値により、ユーザーが関連付けられているロール ID が特定されます。

```json
{
  "id": "dc1c916b-a652-49ea-b128-7c465a54c759",
  "type": "email",
  "roles": [
    {
      "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
    }
  ],
  "email": "user2@contoso.com"
}
```

### <a name="create-a-user"></a>ユーザーを作成する

お使いのアプリケーション内でユーザーを作成するには、次の要求を使用します。 ID とメールアドレスは、アプリケーション内で一意である必要があります。

```http
PUT https://{your app subdomain}.azureiotcentral.com/api/users/user-001?api-version=1.0
```

次の要求本文の `role` 値は、前に取得したオペレーター ロールを対象としています。

```json
{
  "id": "user-001",
  "type": "email",
  "roles": [
    {
      "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
    }
  ],
  "email": "user5@contoso.com"
}
```

この要求に対する応答は、次の例のようになります。 ロールの値により、ユーザーが関連付けられているロールが特定されます。

```json
{
  "id": "user-001",
  "type": "email",
  "roles": [
    {
      "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
    }
  ],
  "email": "user5@contoso.com"
}
```

また、REST API 呼び出しにサービス プリンシパル認証を使用する必要がある場合に役立つサービス プリンシパル ユーザーを追加することもできます。 詳細については、[サービス プリンシパル ユーザーの追加または更新](/rest/api/iotcentral/1.0dataplane/users/create#add-or-update-a-service-principal-user)に関するページを参照してください。

### <a name="change-the-role-of-a-user"></a>ユーザーのロールを変更する

ユーザーに割り当てられているロールを変更するには、次の要求を使用します。 この例では、前に取得したビルダー ロールの ID を使用します。

```http
PATCH https://{your app subdomain}.azureiotcentral.com/api/users/user-001?api-version=1.0
```

要求本文。 値は、前に取得したビルダー ロールを対象としています。

```json
{
  "roles": [
    {
      "role": "344138e9-8de4-4497-8c54-5237e96d6aaf"
    }
  ]
}
```

この要求に対する応答は、次の例のようになります。

```json
{
  "id": "user-001",
  "type": "email",
  "roles": [
    {
      "role": "344138e9-8de4-4497-8c54-5237e96d6aaf"
    }
  ],
  "email": "user5@contoso.com"
}
```

### <a name="delete-a-user"></a>ユーザーの削除

ユーザーを削除するには、次の要求を使用します。

```http
DELETE https://{your app subdomain}.azureiotcentral.com/api/users/user-001?api-version=1.0
```

## <a name="next-steps"></a>次のステップ

これで REST API を使用してユーザーとロールを管理する方法を学習したので、推奨される次のステップは「[IoT Central REST API を使用してデータのエクスポートを管理する方法](howto-manage-data-export-with-rest-api.md)」です。