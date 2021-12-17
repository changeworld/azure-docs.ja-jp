---
title: データの二重暗号化のためのインフラストラクチャ暗号化を有効にする
titleSuffix: Azure Storage
description: データのセキュリティ保護についてより高いレベルの保証が必要なお客様は、Azure Storage インフラストラクチャ レベルで 256 ビットの AES 暗号化を有効にすることもできます。 インフラストラクチャ暗号化が有効になっている場合、ストレージ アカウントまたは暗号化スコープ内のデータは、2 つの異なる暗号化アルゴリズムと 2 つの異なるキーを使用して 2 回暗号化されます。
services: storage
author: tamram
ms.service: storage
ms.date: 06/01/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: c4dca05b855666a04d1def09228c596eb931bbf5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128607009"
---
# <a name="enable-infrastructure-encryption-for-double-encryption-of-data"></a>データの二重暗号化のためのインフラストラクチャ暗号化を有効にする

Azure Storage では、使用可能なブロック暗号の中でも最強クラスの 1 つである 256 ビット AES 暗号化を使用して、サービス レベルでストレージ アカウントのすべてのデータが自動的に暗号化されています。また、FIPS 140-2 に準拠しています。 データのセキュリティ保護についてより高いレベルの保証が必要なお客様は、二重暗号化のために、Azure Storage インフラストラクチャ レベルで 256 ビットの AES 暗号化を有効にすることもできます。 Azure Storage データの二重暗号化を使用すると、暗号化アルゴリズムまたはキーのいずれかが侵害される可能性があるシナリオから保護されます。 このシナリオでは、追加の暗号化レイヤーによって引き続きデータが保護されます。

インフラストラクチャ暗号化は、ストレージ アカウント全体、またはアカウント内の暗号化スコープに対して有効にできます。 ストレージ アカウントまたは暗号化スコープに対してインフラストラクチャ暗号化が有効になっている場合、データは、2 つの異なる暗号化アルゴリズムと 2 つの異なるキーを使用して、2 回 &mdash; サービス レベルで 1 回、インフラストラクチャ レベルで 1 回 &mdash; 暗号化されます。

サービスレベルの暗号化は、Azure Key Vault または Key Vault Managed Hardware Security Model (HSM) (プレビュー) での Microsoft マネージド キーまたはカスタマーマネージド キーの使用をサポートしています。 インフラストラクチャレベルの暗号化は Microsoft マネージド キーに依存し、常に別のキーが使用されます。 Azure Storage 暗号化によるキー管理の詳細については、「[暗号化キーの管理について](storage-service-encryption.md#about-encryption-key-management)」を参照してください。

データを二重に暗号化するには、インフラストラクチャ暗号化用に構成されたストレージ アカウントまたは暗号化スコープを最初に作成する必要があります。 この記事では、インフラストラクチャ暗号化を有効にする方法について説明します。

## <a name="create-an-account-with-infrastructure-encryption-enabled"></a>インフラストラクチャ暗号化を有効にしてアカウントを作成する

ストレージ アカウントに対してインフラストラクチャ暗号化を有効にするには、アカウントの作成時にインフラストラクチャ暗号化を使用するようにストレージ アカウントを構成する必要があります。 アカウントの作成後にインフラストラクチャ暗号化を有効または無効にすることはできません。 ストレージ アカウントの種類は汎用 v2 にする必要があります。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

PowerShell を使用してインフラストラクチャ暗号化が有効なストレージ アカウントを作成するには、次の手順を実行します。

1. Azure portal で、 **[ストレージ アカウント]** ページに移動します。
1. **[追加]** ボタンを選択して、新しい汎用 v2 ストレージ アカウントを追加します。
1. **[詳細]** タブで、 **[インフラストラクチャ]** の暗号化を見つけ、 **[有効]** を選択します。
1. **[確認と作成]** を選択し、ストレージ アカウントの作成を完了します。

    :::image type="content" source="media/infrastructure-encryption-enable/create-account-infrastructure-encryption-portal.png" alt-text="アカウントを作成するときにインフラストラクチャ暗号化を有効にする方法を示すスクリーンショット":::

Azure portal でストレージ アカウントのインフラストラクチャ暗号化が有効なことを確認するには、次の手順を実行します。

1. Azure Portal のストレージ アカウントに移動します。
1. **[設定]** で、 **[暗号化]** を選択します。

    :::image type="content" source="media/infrastructure-encryption-enable/verify-infrastructure-encryption-portal.png" alt-text="[アカウント] でインフラストラクチャ暗号化が有効なことを確認する方法を示すスクリーンショット":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用してインフラストラクチャ暗号化を有効にしたストレージ アカウントを作成するには、[Az.Storage PowerShell モジュール](https://www.powershellgallery.com/packages/Az.Storage) バージョン 2.2.0 以降がインストールされていることを確認します。 詳しくは、[Azure PowerShell のインストール](/powershell/azure/install-az-ps)に関する記事をご覧ください。

次に、[New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) コマンドを呼び出して、汎用 v2 ストレージ アカウントを作成します。 インフラストラクチャ暗号化を有効にするには、`-RequireInfrastructureEncryption` オプションを含めます。

次の例は、読み取りアクセス geo 冗長ストレージ (RA-GRS) 用に構成され、データの二重暗号化のためにインフラストラクチャ暗号化が有効な汎用 v2 ストレージ アカウントを作成する方法を示しています。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -RequireInfrastructureEncryption
```

ストレージ アカウントでインフラストラクチャ暗号化が有効なことを確認するには、[Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) コマンドを呼び出します。 このコマンドによって、ストレージ アカウントのプロパティとその値のセットが返されます。 `Encryption` プロパティ内の `RequireInfrastructureEncryption` フィールドを取得し、それが `True` に設定されていることを確認します。

次の例では、`RequireInfrastructureEncryption` プロパティの値を取得します。 山かっこ内のプレースホルダー値は、実際の値に置き換えてください。

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して、インフラストラクチャ暗号化が有効なストレージ アカウントを作成するには、Azure CLI バージョン 2.8.0 以降がインストールされていることを確認します。 詳細については、「 [Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

次に、[az storage account create](/cli/azure/storage/account#az_storage_account_create) コマンドを呼び出して汎用 v2 ストレージ アカウントを作成し、`--require-infrastructure-encryption option` を含めてインフラストラクチャ暗号化を有効にします。

次の例は、読み取りアクセス geo 冗長ストレージ (RA-GRS) 用に構成され、データの二重暗号化のためにインフラストラクチャ暗号化が有効な汎用 v2 ストレージ アカウントを作成する方法を示しています。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --require-infrastructure-encryption
```

ストレージ アカウントでインフラストラクチャ暗号化が有効なことを確認するには、[az storage account show](/cli/azure/storage/account#az-storage-account-show) コマンドを呼び出します。 このコマンドによって、ストレージ アカウントのプロパティとその値のセットが返されます。 `encryption` プロパティ内の `requireInfrastructureEncryption` フィールドを探し、それが `true` に設定されていることを確認します。

次の例では、`requireInfrastructureEncryption` プロパティの値を取得します。 山かっこ内のプレースホルダー値は、実際の値に置き換えてください。

```azurecli-interactive
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[テンプレート](#tab/template)

次の JSON の例では、読み取りアクセス geo 冗長ストレージ (RA-GRS) 用に構成され、データの二重暗号化のためにインフラストラクチャ暗号化が有効な汎用 v2 ストレージ アカウントを作成します。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

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
                "keySource": "Microsoft.Storage",
                "requireInfrastructureEncryption": true,
                "services": {
                    "blob": { "enabled": true },
                    "file": { "enabled": true }
              }
            }
        }
    }
],
```

---

Azure Policy には、ストレージ アカウントに対してインフラストラクチャの暗号化を有効にすることを要求する組み込みポリシーが用意されています。 詳細については、[「Azure Policy の組み込みポリシー定義」](../../governance/policy/samples/built-in-policies.md#storage)の「**ストレージ**」セクションを参照してください。

## <a name="create-an-encryption-scope-with-infrastructure-encryption-enabled"></a>インフラストラクチャ暗号化を有効にして暗号化スコープを作成する

アカウントに対してインフラストラクチャ暗号化が有効になっている場合、そのアカウントで作成された暗号化スコープでは、インフラストラクチャ暗号化が自動的に使用されます。 アカウント レベルでインフラストラクチャ暗号化が有効になっていない場合、暗号化スコープ作成時にそのスコープに対してそれを有効にするオプションがあります。 暗号化スコープに対するインフラストラクチャ暗号化の設定は、スコープ作成後には変更できません。 詳細については、「[暗号化スコープの作成](../blobs/encryption-scope-manage.md#create-an-encryption-scope)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [保存データに対する Azure Storage 暗号化](storage-service-encryption.md)
- [Azure Storage の暗号化のためのカスタマー マネージド キー](customer-managed-keys-overview.md)
- [BLOB ストレージの暗号化スコープ](../blobs/encryption-scope-overview.md)
