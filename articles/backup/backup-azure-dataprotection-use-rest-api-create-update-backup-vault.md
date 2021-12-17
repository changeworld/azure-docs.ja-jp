---
title: REST API を使用して BLOB 用 Azure Backup ポリシーを作成する。
description: この記事では、REST API を使用してストレージ アカウント内の BLOB をバックアップするポリシーを作成する方法について説明します。
ms.topic: conceptual
ms.date: 07/09/2021
ms.assetid: 93861379-5bec-4ed5-95d2-46f534a115fd
ms.openlocfilehash: 9b32e69bcd96d48dcd1321a69132790a93b1220b
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598754"
---
# <a name="create-azure-backup-vault-using-rest-api"></a>REST API を使用して Azure Backup コンテナーを作成する

Azure Backup の新しい Data Protection プラットフォームでは、ストレージ アカウント内の BLOB、マネージド ディスク、PostGre SQL サーバーの PaaS プラットフォームなど、より新しいワークロードのバックアップと復元の機能が強化されています。 バックアップの整理を容易にしながら、管理オーバーヘッドを最小限に抑えることを目的としています。 "バックアップ コンテナー" は Data Protection プラットフォームの基盤であり、"Recovery Services" コンテナーとは異なります。

REST API を使用して Azure Backup コンテナーを作成する手順の概要は、[コンテナー作成 REST API](/rest/api/dataprotection/backup-vaults/create-or-update) に関するドキュメントで説明されています。 このドキュメントを参考にして、"West US" で "TestBkpVaultRG" リソース グループの下に、"testBkpVault" という名前のコンテナーを作成しましょう。

Azure Backup コンテナーを作成または更新するには、次の *PUT* 操作を使用します。

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/testBkpVault?api-version=2021-01-01
```

## <a name="create-a-request"></a>要求を作成する

*PUT* 要求を作成するには、`{subscription-id}` パラメーターが必要です。 複数のサブスクリプションをお持ちの場合は､[Working with multiple subscriptions](/cli/azure/manage-azure-subscriptions-azure-cli)を参照してください｡ リソースの `{resourceGroupName}` と `{vaultName}` を `api-version` パラメーターと共に定義します。 この記事では、`api-version=2021-01-01` を使用します。

次のヘッダーは必須です｡

| 要求ヘッダー   | 説明 |
|------------------|-----------------|
| *Content-Type:*  | 必須。 `application/json` を設定します。 |
| *Authorization:* | 必須。 有効な `Bearer` [アクセス トークン](/rest/api/azure/#authorization-code-grant-interactive-clients)を設定します。 |

要求の作成方法の詳細については、「[Components of a REST API request/response](/rest/api/azure/#components-of-a-rest-api-requestresponse)」(REST API 要求/応答のコンポーネント) を参照してください。

## <a name="create-the-request-body"></a>要求本文を作成する

要求本文を作成するには、以下の一般的な定義が使用されます。

|名前  |必須  |型  |説明  |
|---------|---------|---------|---------|
|eTag     |         |   String      |  省略可能な eTag       |
|location     |  true       |String         |   リソースの場所      |
|properties     |   true      | [BackupVault](/rest/api/dataprotection/backup-vaults/create-or-update#backupvault)        |  コンテナーのプロパティ       |
|ID     |         |  [DPPIdentityDetails](/rest/api/dataprotection/backup-vaults/create-or-update#dppidentitydetails)       |    各 Azure リソースの一意のシステム ID を示します     |
|tags     |         | Object        |     リソース タグ    |

コンテナー名とリソース グループ名は PUT URI で提供することに注意してください。 要求本文では場所を定義します。

## <a name="example-request-body"></a>要求本文の例

次の例の本文は、"West US" にコンテナーを作成するために使用されています。 場所を指定します。

```json
{
  "location": "WestUS",
  "tags": {
    "key1": "val1"
  },
  "identity": {
    "type": "None"
  },
  "properties": {
    "storageSettings": [
      {
        "datastoreType": "VaultStore",
        "type": "LocallyRedundant"
      }
    ]
  }
}
```

バックアップ コンテナーを作成し、システム割り当て ID も生成する場合は、次の要求本文を指定する必要があります。

```json
{
  "location": "WestUS",
  "tags": {
    "key1": "val1"
  },
  "identity": {
    "type": "systemAssigned"
  },
  "properties": {
    "storageSettings": [
      {
        "datastoreType": "VaultStore",
        "type": "LocallyRedundant"
      }
    ]
  }
}
```

## <a name="responses"></a>応答

バックアップ コンテナーの作成は、[非同期操作](../azure-resource-manager/management/async-operations.md)です。 つまり、この操作では、個別に追跡する必要がある別の操作が作成されます。
バックアップ コンテナーの作成または更新操作には、次の 2 種類の成功応答があります。

|名前  |型  |説明  |
|---------|---------|---------|
|200 OK     |   [BackupVaultResource](/rest/api/dataprotection/backup-vaults/create-or-update#backupvaultresource)      | [OK]        |
|201 Created     | [BackupVaultResource](/rest/api/dataprotection/backup-vaults/create-or-update#backupvaultresource)        |   作成済み      |
| その他の状態コード  |  [CloudError](/rest/api/dataprotection/backup-vaults/create-or-update#clouderror)

REST API の応答の詳細については、「[Process the response message](/rest/api/azure/#process-the-response-message)」(応答メッセージを処理する) を参照してください。

### <a name="example-response"></a>応答の例

前の要求本文の例からの圧縮された *201 Created* 応答では、*id* が割り当てられ、*provisioningState* が *Succeeded* であることが示されています。

```json
{
    "eTag": null,
    "id": "/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/TestBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/testBkpVault",
    "identity": {
      "principalId": "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenantId": "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "SystemAssigned"
    },
    "location": "westUS",
    "name": "testBkpVault",
    "properties": {
      "provisioningState": "Succeeded",
      "storageSettings": [
        {
          "datastoreType": "VaultStore",
          "type": "GeoRedundant"
        }
      ]
    },
    "resourceGroup": "TestBkpVaultRG",
    "systemData": null,
    "tags": {},
    "type": "Microsoft.DataProtection/backupVaults"
  }
```

## <a name="next-steps"></a>次のステップ

[このコンテナー内の BLOB をバックアップするためのバックアップ ポリシーを作成します](backup-azure-dataprotection-use-rest-api-create-update-blob-policy.md)。

Azure REST API について詳しくは、次のドキュメントをご覧ください。

- [Azure Data Protection プロバイダー REST API](/rest/api/dataprotection/)
- [Azure Rest API の開始](/rest/api/azure/)
