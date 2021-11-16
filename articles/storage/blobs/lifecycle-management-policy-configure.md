---
title: ライフサイクル管理ポリシーを構成する
titleSuffix: Azure Storage
description: データのライフサイクルの間に、ホット、クール、アーカイブの各層間でデータを自動的に移動するライフサイクル管理ポリシーを構成します。
author: tamram
ms.author: tamram
ms.date: 08/18/2021
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0fc4fce280db7a2d4744fc8466ff9297699494a3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131460358"
---
# <a name="configure-a-lifecycle-management-policy"></a>ライフサイクル管理ポリシーを構成する

Azure Storage のライフサイクル管理にはルールベースのポリシーが用意されており、これを使用すると、最適なアクセス層に BLOB データを移行したり、データ ライフサイクルの最後にデータを期限切れにしたりすることができます。 ライフサイクル ポリシーは、ベース BLOB に対して機能するほか、オプションで、BLOB のバージョンやスナップショットに対して機能します。 ライフサイクル管理ポリシーの詳細については、「[データ ライフサイクルを自動管理してコストを最適化する](lifecycle-management-overview.md)」を参照してください。

ライフサイクル管理ポリシーは、満たされた条件に基づいて実行する一連のアクションを定義する 1 つ以上のルールで構成されます。 ベース BLOB の場合、次の 2 つの条件のいずれかをチェックすることを選択できます。

- BLOB が最後に変更された後の日数。
- BLOB が最後にアクセスされた後の日数。 アクション内でこの条件を使用するには、最初に、[オプションであるアクセス時間の追跡を有効にする](#optionally-enable-access-time-tracking)必要があります。

選択されている条件が true であると、管理ポリシーにより、指定されているアクションが実行されます。 たとえば、30 日間変更されていない場合に BLOB をホット層からクール層に移動するアクションを定義した場合は、その BLOB に対する最後の書き込み操作から 30 日後に、ライフサイクル管理ポリシーによって BLOB が移動されます。

BLOB のスナップショットまたはバージョンの場合、チェックされる条件は、スナップショットまたはバージョンが作成された後の日数です。

## <a name="optionally-enable-access-time-tracking"></a>オプションであるアクセス時間の追跡を有効にする

ライフサイクル管理ポリシーを構成する前に、BLOB アクセス時間の追跡を有効にすることを選択できます。 アクセス時間の追跡が有効になっていると、ライフサイクル管理ポリシーに、BLOB が読み取りまたは書き込みの操作で最後にアクセスされた時間に基づくアクションを含めることができます。

#### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal で最終アクセス時刻の追跡を有効にするには、次の手順に従います。

1. Azure Portal のストレージ アカウントに移動します。
1. **[データ管理]** セクションで、 **[アクセスの追跡を有効にする]** を選択します。

    :::image type="content" source="media/lifecycle-management-policy-configure/last-access-tracking-enable.png" alt-text="Azure portal で最後のアクセスの追跡を有効にする方法を示しているスクリーンショット":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell で最終アクセス時刻の追跡を有効にするには、次の例に示すように、[Enable-AzStorageBlobLastAccessTimeTracking](/powershell/module/az.storage/enable-azstoragebloblastaccesstimetracking) コマンドを呼び出します。 山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```powershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

Enable-AzStorageBlobLastAccessTimeTracking  -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -PassThru
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI で最終アクセス時刻の追跡を有効にするには、次の例に示すように、[az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) コマンドを呼び出します。 山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --enable-last-access-tracking true
```

# <a name="template"></a>[テンプレート](#tab/template)

Azure Resource Manager テンプレートを使用して新規または既存のストレージ アカウントの最終アクセス時刻の追跡を有効にするには、テンプレート定義に **lastAccessTimeTrackingPolicy** オブジェクトを含めます。 詳細については、[「Microsoft.Storage/storageAccounts/blobServices 2021-02-01」と Bicep および ARM テンプレートに関するリファレンス](/azure/templates/microsoft.storage/2021-02-01/storageaccounts/blobservices?tabs=json)を参照してください。 **lastAccessTimeTrackingPolicy** オブジェクトは、バージョン 2019-06-01 以降の Azure Storage リソース プロバイダー REST API で使用できます。

---

**daysAfterLastAccessTimeGreaterThan** プロパティを使用して、BLOB に対してアクションを実行する必要がある、最後のアクセスからの日数を指定します。

## <a name="create-or-manage-a-policy"></a>ポリシーを作成または管理する

ライフサイクル管理ポリシーは、Azure portal、PowerShell、Azure CLI、または Azure Resource Manager テンプレートを使用して追加、編集、削除することができます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal を通じてポリシーを追加するには、2つの方法があります。

- [リスト ビュー](#list-view)
- [[コード ビュー]](#code-view)

#### <a name="list-view"></a>リスト ビュー

1. Azure Portal のストレージ アカウントに移動します。
1. **[データ管理]** で、 **[ライフサイクル管理]** を選択してライフサイクル管理ポリシーを表示または変更します。
1. **[リスト ビュー]** タブを選択します。

1. **[ルールの追加]** を選択し、 **[詳細]** フォームでルールに名前を付けることができます。 また、 **[規則のスコープ]** 、 **[BLOB の種類]** 、 **[BLOB のサブタイプ]** の各値を設定することもできます。 次の例では、BLOB をフィルター処理するスコープを設定します。 これにより、 **[フィルター セット]** タブが追加されます。

   :::image type="content" source="media/lifecycle-management-policy-configure/lifecycle-management-details.png" alt-text="Azure portal の [ライフサイクル管理] の [ルールの追加] の [詳細] ページ":::

1. **[Base blobs]\(ベース BLOB\)** を選択して、ルールの条件を設定します。 次の例では、BLOB が 30 日間変更されない場合、BLOB はクール ストレージに移動されます。

   :::image type="content" source="media/lifecycle-management-policy-configure/lifecycle-management-base-blobs.png" alt-text="Azure portal の [ライフサイクル管理] の [Base blobs]\(ベース BLOB\) ページ":::

   **[最終アクセス]** オプションを使用できるのは、アクセス時間の追跡を有効にしている場合にのみです。 アクセスの追跡を有効にする方法については、「[オプションであるアクセス時間の追跡を有効にする](#optionally-enable-access-time-tracking)」を参照してください。

1. **[詳細]** ページで **[フィルターを使用して BLOB を制限する]** を選択した場合は、 **[フィルター セット]** を選択して省略可能なフィルターを追加します。 次の例では、*sample-container* という名前のコンテナー内の、名前が *log* で始まる BLOB にフィルターを適用しています。

   :::image type="content" source="media/lifecycle-management-policy-configure/lifecycle-management-filter-set.png" alt-text="Azure portal の [ライフサイクル管理] の [フィルター セット] ページ":::

1. **[追加]** を選択して新しいポリシーを追加します。

#### <a name="code-view"></a>[コード ビュー]

1. Azure Portal のストレージ アカウントに移動します。
1. **[データ管理]** で、 **[ライフサイクル管理]** を選択してライフサイクル管理ポリシーを表示または変更します。
1. **[コード ビュー]** タブを選択します。このタブでは、JSON でライフサイクル管理ポリシーを定義できます。

次のサンプル JSON では、BLOB の変更からの経過時間が 30 日を超えた場合に、名前が *log* で始まるブロック BLOB をクール層に移動するライフサイクル ポリシーを定義しています。

   ```json
   {
     "rules": [
       {
         "enabled": true,
         "name": "move-to-cool",
         "type": "Lifecycle",
         "definition": {
           "actions": {
             "baseBlob": {
               "tierToCool": {
                 "daysAfterModificationGreaterThan": 30
               }
             }
           },
           "filters": {
             "blobTypes": [
               "blockBlob"
             ],
             "prefixMatch": [
               "sample-container/log"
             ]
           }
         }
       }
     ]
   }
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用してライフサイクル管理ポリシーを追加するには、以下のコマンドを使用します。

- ルールを構成するアクションを定義するには、[Add-AzStorageAccountManagementPolicyAction](/powershell/module/az.storage/add-azstorageaccountmanagementpolicyaction) コマンドを呼び出します。
- ルールのために 1 つ以上のフィルターを指定するには、[New-AzStorageAccountManagementPolicyFilter](/powershell/module/az.storage/new-azstorageaccountmanagementpolicyfilter) コマンドを呼び出します。
- アクションとオプションのフィルターが含まれるポリシー ルールを作成するには、[New-AzStorageAccountManagementPolicyRule](/powershell/module/az.storage/new-azstorageaccountmanagementpolicyrule) コマンドを呼び出します。
- ストレージ アカウントに対するポリシーを作成するには、[Set-AzStorageAccountManagementPolicy](/powershell/module/az.storage/set-azstorageaccountmanagementpolicy) コマンドを呼び出します。

次の例は、これらの各コマンドを使用してライフサイクル ポリシーを作成する方法を示しています。 山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```powershell
# Initialize the following variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Create a new action object.
$action = Add-AzStorageAccountManagementPolicyAction -BaseBlobAction Delete `
    -daysAfterModificationGreaterThan 180
Add-AzStorageAccountManagementPolicyAction -InputObject $action `
    -BaseBlobAction TierToArchive `
    -daysAfterModificationGreaterThan 90
Add-AzStorageAccountManagementPolicyAction -InputObject $action `
    -BaseBlobAction TierToCool `
    -daysAfterModificationGreaterThan 30
Add-AzStorageAccountManagementPolicyAction -InputObject $action `
    -SnapshotAction Delete `
    -daysAfterCreationGreaterThan 90
Add-AzStorageAccountManagementPolicyAction -InputObject $action `
    -BlobVersionAction TierToArchive `
    -daysAfterCreationGreaterThan 90

# Create a new filter object.
$filter = New-AzStorageAccountManagementPolicyFilter -PrefixMatch ab,cd `
    -BlobType blockBlob

# Create a new rule object.
$rule1 = New-AzStorageAccountManagementPolicyRule -Name sample-rule `
    -Action $action `
    -Filter $filter

# Create the policy.
Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -Rule $rule1
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してライフサイクル管理ポリシーを追加するには、そのポリシーを JSON ファイルに書き込んでから、[az storage account management-policy create](/cli/azure/storage/account/management-policy#az_storage_account_management_policy_create) コマンドを呼び出してポリシーを作成します。

次の例は、これらの各コマンドを使用してライフサイクル ポリシーを作成する方法を示しています。 山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```azurecli
az storage account management-policy create \
    --account-name <storage-account> \
    --policy @policy.json \
    --resource-group <resource-group>
```

# <a name="template"></a>[テンプレート](#tab/template)

Azure Resource Manager テンプレートを使用してライフサイクル管理ポリシーを定義するには、テンプレートに **Microsoft.Storage/storageAccounts/managementPolicies** オブジェクトを含めます。 構成の詳細については、[「Microsoft.Storage/storageAccounts/managementPolicies 2021-02-01」と Bicep および ARM テンプレートに関するリファレンス](/azure/templates/microsoft.storage/2021-02-01/storageaccounts/managementpolicies?tabs=json)を参照してください。 **Microsoft.Storage/storageAccounts/managementPolicies** オブジェクトは、バージョン 2018-11-01 以降の Azure Storage リソース プロバイダー REST API で使用できます。

---

ライフサイクル管理ポリシーは、その全体の読み取りまたは書き込みを行う必要があります。 部分的な更新はサポートされません。

> [!NOTE]
> ストレージ アカウントのファイアウォール ルールを有効にしている場合、ライフサイクル管理要求がブロックされることがあります。 信頼できる Microsoft サービスに例外を指定することで、このような要求のブロックを解除できます。 詳細については、[ファイアウォールと仮想ネットワークの構成](../common/storage-network-security.md#exceptions)に関する記事の、**例外** についてのセクションを参照してください。

## <a name="see-also"></a>こちらもご覧ください

- [データ ライフサイクルを自動管理してコストを最適化する](lifecycle-management-overview.md)
- [BLOB データのホット、クールおよびアーカイブ アクセス層](access-tiers-overview.md)
