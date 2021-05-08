---
title: テーブルとキューのカスタマーマネージド キーがサポートされるアカウントを作成する
titleSuffix: Azure Storage
description: テーブルとキューのカスタマーマネージド キーの構成がサポートされるストレージ アカウントを作成する方法について説明します。 Azure CLI または Azure Resource Manager テンプレートを使って、Azure Storage 暗号化にアカウント暗号化キーを使用するストレージ アカウントを作成します。 これにより、そのアカウントのカスタマーマネージド キーを構成できるようになります。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/31/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 4c86811ee72d2713fced6320a17d1ccde1866d99
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769951"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>テーブルとキューのカスタマーマネージド キーがサポートされるアカウントを作成する

Azure Storage は、保存されているストレージ アカウント内のすべてのデータを暗号化します。 既定では、Queue storage と Table Storage で使用されるキーは、サービスに対してスコープ指定されていて、Microsoft によって管理されます。 また、カスタマーマネージド キーを使用してキューまたはテーブルのデータを暗号化することもできます。 キューとテーブルでカスタマーマネージド キーを使用するには、サービスではなくアカウントに対してスコープ指定された暗号化キーを使用するストレージ アカウントを最初に作成しなければなりません。 キューとテーブルのデータにアカウント暗号化キーを使用するアカウントを作成した後、そのストレージ アカウントのカスタマーマネージド キーを構成できます。

この記事では、アカウントに対してスコープ指定されたキーを使用するストレージ アカウントの作成方法について説明します。 アカウントが作成されると、最初は Microsoft がアカウント キーを使用してそのアカウントのデータを暗号化します。また、キーは Microsoft によって管理されます。 後からアカウントのカスタマーマネージド キーを構成することで、独自のキーの指定、キー バージョンの更新、キーのローテーション、アクセス制御の取り消しを行えるといった利点を活用できます。

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>アカウント暗号化キーを使用するアカウントを作成する

新しいストレージ アカウントの作成時に、キューとテーブルにアカウント暗号化キーを使用するようそのストレージ アカウントを構成する必要があります。 アカウントの作成後は、暗号化キーのスコープを変更することはできません。

ストレージ アカウントの種類は汎用 v2 にする必要があります。 Azure CLI または Azure Resource Manager テンプレートのいずれかを使用して、ストレージ アカウントを作成し、アカウント暗号化キーを使用するよう構成できます。

> [!NOTE]
> アカウント暗号化キーを使用してデータを暗号化するための構成を、ストレージ アカウントの作成時に任意で行えるのは、Queue storage と Table Storage だけです。 Blob Storage と Azure Files では、常にアカウント暗号化キーを使用してデータを暗号化します。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用して、アカウント暗号化キーを使用するストレージ アカウントを作成するには、Azure PowerShell モジュール バージョン 3.4.0 以降がインストールされていることを確認してください。 詳しくは、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。

次に、適切なパラメーターを指定して [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) コマンドを呼び出し、汎用 v2 ストレージ アカウントを作成します。

- アカウント暗号化キーを使用して Queue storage のデータを暗号化するには、`-EncryptionKeyTypeForQueue` オプションを指定して、その値を `Account` に設定します。
- アカウント暗号化キーを使用して Table Storage のデータを暗号化するには、`-EncryptionKeyTypeForTable` オプションを指定して、その値を `Account` に設定します。

次の例で示すのは、読み取りアクセス geo 冗長ストレージ (RA-GRS) 用に構成されていて、Queue storage と Table Storage の両方でデータの暗号化にアカウント暗号化キーを使用する汎用 v2 ストレージ アカウントを作成する方法です。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -EncryptionKeyTypeForTable Account `
    -EncryptionKeyTypeForQueue Account
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して、アカウント暗号化キーを使用するストレージ アカウントを作成するには、Azure CLI バージョン 2.0.80 以降がインストールされていることを確認してください。 詳細については、「 [Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

次に、適切なパラメーターを指定して [az storage account create](/cli/azure/storage/account#az_storage_account_create) コマンドを呼び出し、汎用 v2 ストレージ アカウントを作成します。

- アカウント暗号化キーを使用して Queue storage のデータを暗号化するには、`--encryption-key-type-for-queue` オプションを指定して、その値を `Account` に設定します。
- アカウント暗号化キーを使用して Table Storage のデータを暗号化するには、`--encryption-key-type-for-table` オプションを指定して、その値を `Account` に設定します。

次の例で示すのは、読み取りアクセス geo 冗長ストレージ (RA-GRS) 用に構成されていて、Queue storage と Table Storage の両方でデータの暗号化にアカウント暗号化キーを使用する汎用 v2 ストレージ アカウントを作成する方法です。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

# <a name="template"></a>[テンプレート](#tab/template)

次の JSON 例では、読み取りアクセス geo 冗長ストレージ (RA-GRS) 用に構成されていて、Queue storage と Table Storage の両方でデータの暗号化にアカウント暗号化キーを使用する汎用 v2 ストレージ アカウントを作成します。 山かっこ内のプレースホルダー値は、実際の値に置き換えてください。

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
            "name": "[parameters('Standard_RAGRS')]"
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

アカウントの暗号化キーに依存するアカウントを作成した後、Azure Key Vault または Key Vault Managed Hardware Security Model (HSM) (プレビュー) に格納されているカスタマーマネージド キーを構成できます。 カスタマーマネージド キーをキー コンテナーに格納する方法については、「[Azure Key Vault に格納されているカスタマー マネージド キーによる暗号化を構成する](customer-managed-keys-configure-key-vault.md)」を参照してください。 マネージド HSM にカスタマーマネージド キーを格納する方法については、「[Azure Key Vault Managed HSM (プレビュー) に格納されているカスタマー マネージド キーによる暗号化を構成する](customer-managed-keys-configure-key-vault-hsm.md)」を参照してください。

## <a name="verify-the-account-encryption-key"></a>アカウント暗号化キーを確認する

ストレージ アカウント内のサービスでアカウント暗号化キーが使用されていることを確認するには、Azure CLI の [az storage account](/cli/azure/storage/account#az_storage_account_show) コマンドを呼び出します。 このコマンドによって、ストレージ アカウントのプロパティとその値のセットが返されます。 暗号化プロパティ内で各サービスの `keyType` フィールドを探し、`Account` に設定されていることを確認します。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

ストレージ アカウント内のサービスでアカウント暗号化キーが使用されていることを確認するには、[Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) コマンドを呼び出します。 このコマンドによって、ストレージ アカウントのプロパティとその値のセットが返されます。 `Encryption` プロパティ内で各サービスの `KeyType` フィールドを探し、`Account` に設定されていることを確認します。

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

ストレージ アカウント内のサービスでアカウント暗号化キーが使用されていることを確認するには、[az storage account show](/cli/azure/storage/account#az_storage_account_show) コマンドを呼び出します。 このコマンドによって、ストレージ アカウントのプロパティとその値のセットが返されます。 暗号化プロパティ内で各サービスの `keyType` フィールドを探し、`Account` に設定されていることを確認します。

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[テンプレート](#tab/template)

該当なし

---

## <a name="pricing-and-billing"></a>価格と課金

アカウントにスコープが設定されている暗号化キーを使用するように作成されたストレージ アカウントは、テーブル ストレージの容量とトランザクションンについて、既定のサービス スコープ キーを使用するアカウントとは異なるレートで課金されます。 詳細については、「[Azure Table Storage の料金](https://azure.microsoft.com/pricing/details/storage/tables/)」をご覧ください。

## <a name="next-steps"></a>次のステップ

- [保存データに対する Azure Storage 暗号化](storage-service-encryption.md)
- [Azure Storage の暗号化のためのカスタマー マネージド キー](customer-managed-keys-overview.md)
- [Azure Key Vault とは](../../key-vault/general/overview.md)