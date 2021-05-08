---
title: Azure Media Services の信頼されたストレージ
description: このチュートリアルでは、Azure Media Services 用の信頼されたストレージを有効にする方法と、信頼されたストレージにマネージド ID を使用する方法、さらに、ファイアウォールまたは VPN が使用されていても Azure サービスからストレージ アカウントにアクセスできるようにする方法について説明します。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 2/8/2021
ms.openlocfilehash: f076381333457c9ba2fd4325fa8aa7baad5d8a5b
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281087"
---
# <a name="tutorial-media-services-trusted-storage"></a>チュートリアル:Media Services の信頼されたストレージ

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> - Azure Media Services 用の信頼されたストレージを有効にする方法
> - 信頼されたストレージにマネージド ID を使用する方法
> - ネットワーク アクセス制御 (ファイアウォール、VPN など) が使用されていても Azure サービスからストレージ アカウントにアクセスできるようにする方法

2020-05-01 API では、Media Services アカウントにマネージド ID を関連付けることにより、信頼されたストレージを有効にすることができます。

>[!NOTE]
>信頼されたストレージは API でのみ使用でき、現在、Azure portal では有効になっていません。

Media Services は、システム認証を使用してストレージ アカウントに自動的にアクセスすることができます。 Media Services アカウントとストレージ アカウントが同じサブスクリプションに含まれていることが Media Services によって検証されます。 また、関連付けを追加するユーザーが Azure Resource Manager RBAC を使用してストレージ アカウントにアクセスしていることも検証されます。

ただし、ネットワーク アクセス制御を使用してストレージ アカウントのセキュリティを確保しつつ、信頼されたストレージを有効にしたければ、[マネージド ID](concept-managed-identities.md) 認証が必須となります。 これにより、Media Services は、信頼されたストレージ アクセスによって、ファイアウォールまたは VNet 制限で構成されたストレージ アカウントにアクセスできるようになります。

## <a name="overview"></a>概要

> [!IMPORTANT]
> Media Services への要求にはすべて 2020-05-01 API を使用してください。

Media Services 用の信頼されたストレージを作成する一般的な手順は次のとおりです。

1. リソース グループを作成します。
1. ストレージ アカウントを作成します。
1. 準備完了状態になるまでストレージ アカウントをポーリングします。 ストレージ アカウントの準備が完了すると、要求からサービス プリンシパル ID が返されます。
1. "*ストレージ BLOB データ共同作成者*" ロールの ID を見つけます。
1. 認可プロバイダーを呼び出し、ロールの割り当てを追加します。
1. ストレージ アカウントへの認証にマネージド ID を使用するよう Media Services アカウントを更新します。
1. 今後リソースを使う予定がなければ、料金が発生しないようリソースを削除します。

<!-- Link to storage role contributor role access differences information -->

## <a name="prerequisites"></a>前提条件

作業を始めるには、Azure サブスクリプションが必要です。  Azure サブスクリプションを持っていない場合は、[無料試用版アカウントを作成できます](https://azure.microsoft.com/free/)。

### <a name="get-your-tenant-id-and-subscription-id"></a>テナント ID とサブスクリプション ID を取得する

テナント ID とサブスクリプション ID の取得方法がわからない場合は、[テナント ID の調べ方](setup-azure-tenant-how-to.md)と[テナント ID の確認](setup-azure-tenant-how-to.md)に関するページを参照してください。

### <a name="create-a-service-principal-and-secret"></a>サービス プリンシパルとシークレットを作成する

サービス プリンシパルとシークレットの作成方法がわからない場合は、「[Media Services API にアクセスするための資格情報を取得する](access-api-howto.md)」を参照してください。

## <a name="use-a-rest-client"></a>REST クライアントを使用する

このスクリプトは、REST クライアント (Visual Studio Code 拡張機能で利用できる REST クライアントなど) での使用を意図したものです。  実際の開発環境に合わせて調整してください。

## <a name="set-initial-variables"></a>初期変数を設定する

この部分のスクリプトは、REST クライアントで使用するためのものです。 実際の開発環境内では、変数の使い方が異なる場合があります。

```rest
### AAD details
@tenantId = your tenant ID
@servicePrincipalId = the service principal ID
@servicePrincipalSecret = the service principal secret

### AAD resources
@armResource = https%3A%2F%2Fmanagement.core.windows.net%2F
@graphResource = https%3A%2F%2Fgraph.windows.net%2F
@storageResource = https%3A%2F%2Fstorage.azure.com%2F

### Service endpoints
@armEndpoint = management.azure.com
@graphEndpoint = graph.windows.net
@aadEndpoint = login.microsoftonline.com

### ARM details
@subscription = your subscription id
@resourceGroup = the resource group you'll be creating
@storageName = the name of the storage you'll be creating
@accountName = the name of the account you'll be creating
@resourceLocation = East US (or the location that works best for your region)
```

## <a name="get-a-token-for-azure-resource-manager"></a>Azure Resource Manager のトークンを取得する

```rest
// @name getArmToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{armResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="get-a-token-for-the-graph-api"></a>Graph API のトークンを取得する

この部分のスクリプトは、REST クライアントで使用するためのものです。 実際の開発環境内では、変数の使い方が異なる場合があります。

```rest
// @name getGraphToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{graphResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="get-the-service-principal-details"></a>サービス プリンシパルの詳細を取得する

```rest
// @name getServicePrincipals
GET https://{{graphEndpoint}}/{{tenantId}}/servicePrincipals?$filter=appId%20eq%20'{{servicePrincipalId}}'&api-version=1.6
x-ms-client-request-id: cae3e4f7-17a0-476a-a05a-0dab934ba959
Authorization:  Bearer {{getGraphToken.response.body.access_token}}
```

## <a name="store-the-service-principal-id"></a>サービス プリンシパル ID を格納する

```rest
@servicePrincipalObjectId = {{getServicePrincipals.response.body.value[0].objectId}}
```

## <a name="create-a-resource-group"></a>リソース グループを作成する

```rest
// @name createResourceGroup
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}
    ?api-version=2016-09-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "location": "{{resourceLocation}}"
}
```

## <a name="create-storage-account"></a>ストレージ アカウントの作成

```rest
// @name createStorageAccount
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "sku": {
    "name": "Standard_GRS"
    },
    "kind": "StorageV2",
    "location": "{{resourceLocation}}",
    "properties": {
    }
}
```

## <a name="get-the-storage-account-status"></a>ストレージ アカウントの状態を取得する

ストレージ アカウントの準備が完了するまでにしばらく時間がかかるため、この要求で状態をポーリングします。  ストレージ アカウントの準備が完了するまで、この要求を繰り返します。

```rest
// @name getStorageAccountStatus
GET {{createStorageAccount.response.headers.Location}}
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="get-the-storage-account-details"></a>ストレージ アカウントの詳細を取得する

ストレージ アカウントの準備が完了したら、そのプロパティを取得します。

```rest
// @name getStorageAccount
GET https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="get-a-token-for-arm"></a>ARM のトークンを取得する

```rest
// @name getStorageToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{storageResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="create-a-media-services-account-with-a-system-assigned-managed-identity"></a>システム割り当てマネージド ID を使用して Media Services アカウントを作成する

```rest
// @name createMediaServicesAccount
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "identity": {
      "type": "SystemAssigned"
  },
  "properties": {
    "storageAccounts": [
      {
        "id": "{{getStorageAccountStatus.response.body.id}}"
      }
    ],
    "encryption": {
      "type": "SystemKey"
    }
  },
  "location": "{{resourceLocation}}"
}
```

## <a name="get-the-storage-storage-blob-data-role-definition"></a>ストレージの "ストレージ BLOB データ" ロールの定義を取得する

```rest
// @name getStorageBlobDataContributorRoleDefinition
GET https://management.azure.com/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleDefinitions?$filter=roleName%20eq%20%27Storage%20Blob%20Data%20Contributor%27&api-version=2015-07-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

### <a name="set-the-storage-role-assignment"></a>ストレージ ロールの割り当てを設定する

Media Services アカウントのサービス プリンシパルには、"*ストレージ BLOB データ共同作成者*" というストレージ ロールが割り当てられていることが、ロールの割り当てからわかります。  これにはしばらく時間がかかることがあるので、お待ちください。時間が経たないと、Media Services アカウントが正しく設定されません。

```rest
PUT https://management.azure.com/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleAssignments/{{$guid}}?api-version=2020-04-01-preview
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "roleDefinitionId": "/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleDefinitions/{{getStorageBlobDataContributorRoleDefinition.response.body.value[0].name}}",
    "principalId": "{{createMediaServicesAccount.response.body.identity.principalId}}"
  }
}
```

## <a name="give-managed-identity-bypass-access-to-the-storage-account"></a>マネージド ID にストレージ アカウントへのバイパス アクセス権を与える

この操作によって、アクセスがシステム マネージド ID からマネージド ID に変更されます。 これにより、ファイアウォールを介したストレージ アカウントへのアクセスが可能となります。Azure サービスは、IP アクセス ルール (ACL) に関係なくストレージ アカウントにアクセスできるためです。

この場合も、ストレージ アカウントにロールが割り当てられるまで待ってください。そうしないと、Media Services アカウントが正しく設定されません。

```rest
// @name setStorageAccountFirewall
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "sku": {
    "name": "Standard_GRS"
    },
    "kind": "StorageV2",
    "location": "{{resourceLocation}}",
    "properties": {
      "minimumTlsVersion": "TLS1_2",
      "networkAcls": {
        "bypass": "AzureServices",
        "virtualNetworkRules": [],
        "ipRules": [],
        "defaultAction": "Deny"
      }
    }
}
```

## <a name="update-the-media-services-account-to-use-the-managed-identity"></a>マネージド ID を使用するように Media Services アカウントを更新します。

ストレージのロールの割り当てが伝達されるまでには数分かかることもあるので、この要求は何度か再試行しなければならない場合があります。

```rest
// @name updateMediaServicesAccountWithManagedStorageAuth
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "identity": {
      "type": "SystemAssigned"
  },
  "properties": {
    "storageAccounts": [
      {
        "id": "{{getStorageAccountStatus.response.body.id}}"
      }
    ],
    "storageAuthentication": "ManagedIdentity",
    "encryption": {
      "type": "SystemKey"
    }
  },
  "location": "{{resourceLocation}}"
}
```

## <a name="test-access"></a>アクセスをテストする

ストレージ アカウントに資産を作成してアクセスをテストします。

```rest
// @name createAsset
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}/assets/testasset{{index}}withoutmi?api-version=2018-07-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
}
```

## <a name="delete-resources"></a>リソースを削除する

作成したリソースをそのままにすると今後も料金が発生します。それを避けるためにはリソースを削除してください。

```rest
### Clean up the Storage account
DELETE https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}

### Clean up the Media Services account
DELETE https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}

### Clean up the Media Services account
GET https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="next-steps"></a>次の手順

[資産を作成する方法](asset-create-asset-how-to.md)
