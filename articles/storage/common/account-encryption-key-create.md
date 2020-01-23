---
title: テーブルとキューのカスタマーマネージド キーがサポートされるアカウントを作成する
titleSuffix: Azure Storage
description: テーブルとキューのカスタマーマネージド キーの構成がサポートされるストレージ アカウントを作成する方法について説明します。 Azure CLI または Azure Resource Manager テンプレートを使って、Azure Storage 暗号化にアカウント暗号化キーを使用するストレージ アカウントを作成します。 これにより、そのアカウントのカスタマーマネージド キーを構成できるようになります。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 8cf1f8ecb68e31f93c19d93d6ebc4f8ef37724e7
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028439"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>テーブルとキューのカスタマーマネージド キーがサポートされるアカウントを作成する

Azure Storage は、保存されているストレージ アカウント内のすべてのデータを暗号化します。 既定では、Queue storage と Table Storage で使用されるキーは、サービスに対してスコープ指定されていて、Microsoft によって管理されます。 また、カスタマーマネージド キーを使用してキューまたはテーブルのデータを暗号化することもできます。 キューとテーブルでカスタマーマネージド キーを使用するには、サービスではなくアカウントに対してスコープ指定された暗号化キーを使用するストレージ アカウントを最初に作成しなければなりません。 キューとテーブルのデータにアカウント暗号化キーを使用するアカウントを作成した後、そのストレージ アカウントのカスタマーマネージド キーを Azure Key Vault で構成できます。

この記事では、アカウントに対してスコープ指定されたキーを使用するストレージ アカウントの作成方法について説明します。 アカウントが作成されると、最初は Microsoft がアカウント キーを使用してそのアカウントのデータを暗号化します。また、キーは Microsoft によって管理されます。 後からアカウントのカスタマーマネージド キーを構成することで、独自のキーの指定、キー バージョンの更新、キーのローテーション、アクセス制御の取り消しを行えるといった利点を活用できます。

## <a name="about-the-feature"></a>機能について

Queue storage と Table Storage でアカウント暗号化キーを使用するストレージ アカウントを作成するには、最初に、Azure でこの機能を使用するための登録を行う必要があります。 容量が限られているため、アクセス権のリクエストが承認されるまでに数か月かかる場合があることにご注意ください。

Queue storage と Table Storage でアカウント暗号化キーを使用するストレージ アカウントを作成できるのは、次のリージョンになります。

- East US
- 米国中南部
- 米国西部 2  

### <a name="register-to-use-the-account-encryption-key"></a>アカウント暗号化キーを使用するための登録を行う

Azure CLI を使用して登録を行うには、[az feature register](/cli/azure/feature#az-feature-register) コマンドを呼び出します。

Queue storage でアカウント暗号化キーを使用するための登録を行うには:

```azurecli
az feature register --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForQueues
```

Table Storage でアカウント暗号化キーを使用するための登録を行うには:

```azurecli
az feature register --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForTables
```

### <a name="check-the-status-of-your-registration"></a>登録の状態を確認する

Queue storage の登録の状態を確認するには:

```azurecli
az feature show --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForQueues
```

Table Storage の登録の状態を確認するには:

```azurecli
az feature show --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForTables
```

### <a name="re-register-the-azure-storage-resource-provider"></a>Azure Storage リソース プロバイダーを再登録する

登録が承認されたら、Azure Storage リソース プロバイダーを再登録する必要があります。 [az provider register](/cli/azure/provider#az-provider-register) コマンドを呼び出します。

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>アカウント暗号化キーを使用するアカウントを作成する

新しいストレージ アカウントの作成時に、キューとテーブルにアカウント暗号化キーを使用するようそのストレージ アカウントを構成する必要があります。 アカウントの作成後は、暗号化キーのスコープを変更することはできません。

ストレージ アカウントの種類は汎用 v2 にする必要があります。 Azure CLI または Azure Resource Manager テンプレートのいずれかを使用して、ストレージ アカウントを作成し、アカウント暗号化キーを使用するよう構成できます。

> [!NOTE]
> アカウント暗号化キーを使用してデータを暗号化するための構成を、ストレージ アカウントの作成時に任意で行えるのは、Queue storage と Table Storage だけです。 Blob Storage と Azure Files では、常にアカウント暗号化キーを使用してデータを暗号化します。

### <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して、アカウント暗号化キーを使用するストレージ アカウントを作成するには、Azure CLI バージョン 2.0.80 以降がインストールされていることを確認してください。 詳細については、「 [Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

次に、適切なパラメーターを指定して [az storage account create](/cli/azure/storage/account#az-storage-account-create) コマンドを呼び出し、汎用 v2 ストレージ アカウントを作成します。

- アカウント暗号化キーを使用して Queue storage のデータを暗号化するには、`--encryption-key-type-for-queue` オプションを指定して、その値を `Account` に設定します。
- アカウント暗号化キーを使用して Table Storage のデータを暗号化するには、`--encryption-key-type-for-table` オプションを指定して、その値を `Account` に設定します。

次の例で示すのは、LRS 用に構成されていて、Queue storage と Table Storage の両方でデータの暗号化にアカウント暗号化キーを使用する汎用 v2 ストレージ アカウントを作成する方法です。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_LRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

### <a name="templatetabtemplate"></a>[テンプレート](#tab/template)

次の JSON サンプルでは、LRS 用に構成されていて、Queue storage と Table Storage の両方でデータの暗号化にアカウント暗号化キーを使用する汎用 v2 ストレージ アカウントを作成します。 山かっこ内のプレースホルダー値は、実際の値に置き換えてください。

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_LRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "services": {
                    "queue": {
                        "keyType": "Account"
                    },
                    "table": {
                        "keyType": "Account"
                    }
                },
                "keySource": "Microsoft.Storage"
            }
        }
    }
],
```

---

アカウント暗号化キーを使用するアカウントを作成したら、次の記事のいずれかを参照して、Azure Key Vault でカスタマーマネージド キーを構成します。

- [Azure portal を使用して Azure Key Vault でカスタマーマネージド キーを構成する](storage-encryption-keys-portal.md)
- [PowerShell を使用して Azure Key Vault でカスタマーマネージド キーを構成する](storage-encryption-keys-powershell.md)
- [Azure CLI を使用して Azure Key Vault でカスタマーマネージド キーを構成する](storage-encryption-keys-cli.md)

## <a name="verify-the-account-encryption-key"></a>アカウント暗号化キーを確認する

ストレージ アカウント内のサービスでアカウント暗号化キーが使用されていることを確認するには、Azure CLI の [az storage account](/cli/azure/storage/account#az-storage-account-show) コマンドを呼び出します。 このコマンドによって、ストレージ アカウントのプロパティとその値のセットが返されます。 暗号化プロパティ内で各サービスの `keyType` フィールドを探し、`Account` に設定されていることを確認します。

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

## <a name="next-steps"></a>次のステップ

- [保存データに対する Azure Storage 暗号化](storage-service-encryption.md) 
- [Azure Key Vault とは](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
