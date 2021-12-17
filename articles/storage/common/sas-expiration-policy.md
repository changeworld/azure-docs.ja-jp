---
title: 共有アクセス署名の有効期限ポリシーを作成する
titleSuffix: Azure Storage
description: 共有アクセス署名 (SAS) が有効となる期間を定義するポリシーをストレージ アカウントに作成します。 ポリシー違反を監視してセキュリティ リスクを修復する方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/14/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: 8d9a381e80c829acc2f87aa6a856a651b19315ae
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128709059"
---
# <a name="create-an-expiration-policy-for-shared-access-signatures"></a>共有アクセス署名の有効期限ポリシーを作成する

共有アクセス署名 (SAS) を使用して、Azure Storage アカウント内のリソースへのアクセスを委任できます。 SAS トークンには、対象となるリソース、付与されるアクセス許可、およびアクセスが許可される間隔が含まれます。 ベスト プラクティスとして、SAS が侵害された場合に備えて、その間隔を制限することをお勧めします。 ストレージ アカウントに SAS の有効期限ポリシーを設定することで、ユーザーが SAS を作成するときに推奨される有効期限の上限を指定できます。

SAS の有効期限ポリシーでは、ポリシーによって推奨されている制限を超える有効期限の SAS をユーザーが作成するのを妨げることはできません。 ユーザーがポリシーに違反する SAS を作成すると、推奨される最大間隔と共に警告が表示されます。 Azure Monitor でログを記録するように診断設定を構成している場合、推奨される間隔よりも有効期限が長い SAS をユーザーが作成するたびに、Azure Storage によってログのプロパティに書き込まれます。

Shared Access Signature の詳細については、「[Shared Access Signatures (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](storage-sas-overview.md)」を参照してください。

## <a name="create-a-sas-expiration-policy"></a>SAS の有効期限ポリシーを作成する

ストレージ アカウントで SAS の有効期限ポリシーを作成すると、そのストレージ アカウントで作成できる各種類の SAS (サービス SAS、ユーザー委任 SAS、アカウント SAS など) にポリシーが適用されます。

ストレージ アカウントで SAS の有効期限ポリシーを構成するには、Azure portal、PowerShell、または Azure CLI を使用します。

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

Azure portal で SAS の有効期限ポリシーを作成するには、次の手順を実行します。

1. Azure Portal のストレージ アカウントに移動します。
1. **[設定]** の下で **[構成]** を選択します。
1. **[Allow recommended upper limit for shared access signature (SAS) expiry interval]\(共有アクセス署名 (SAS) の有効期間に推奨上限値を許可する\)** の設定を見つけて、 **[有効]** に設定します。
1. このストレージ アカウントのリソースに対して作成された新しい共有アクセス署名の推奨間隔を指定します。

    :::image type="content" source="media/sas-expiration-policy/configure-sas-expiration-policy-portal.png" alt-text="Azure portal で SAS の有効期限ポリシーを構成する方法を示すスクリーンショット":::

1. **[保存]** を選択して、変更を保存します。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

SAS の有効期限ポリシーを作成するには、[Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) コマンドを使用して、SAS が署名されてから SAS トークンをアクティブにできる日、時間、分、秒の数値を `-SasExpirationPeriod` パラメーターで設定します。 `-SasExpirationPeriod` パラメーターで指定する文字列は、`<days>.<hours>:<minutes>:<seconds>` の形式を使用します。 たとえば、SAS の有効期限を 1 日、12 時間、5 分、6 秒にしたい場合は、文字列 `1.12:05:06` を使用します。

```powershell
$account = Set-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account-name> `
    -SasExpirationPeriod <days>.<hours>:<minutes>:<seconds>
```

> [!TIP]
> SAS の有効期限ポリシーの設定は、ストレージ アカウントの作成時に、[New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) コマンドの `-SasExpirationPeriod` パラメーターの設定で行うこともできます。

ポリシーが適用されていることを確認するには、前のコマンドの `$account` 変数に返された [PSStorageAccount](/dotnet/api/microsoft.azure.commands.management.storage.models.psstorageaccount) の `SasPolicy` プロパティを使用します。 
  
```powershell
$account.SasPolicy
```

コンソールの出力に SAS の有効期限が表示されます。

> [!div class="mx-imgBorder"]
> ![SAS の有効期限](./media/storage-sas-expiration-policy/sas-policy-console-output.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

SAS の有効期限ポリシーを作成するには、[az storage account update](/cli/azure/storage/account#az_storage_account_update) コマンドを使用して、SAS が署名されてから SAS トークンをアクティブにできる日、時間、分、秒の数値を `--key-exp-days` パラメーターで設定します。 `--key-exp-days` パラメーターで指定する文字列は、`<days>.<hours>:<minutes>:<seconds>` の形式を使用します。 たとえば、SAS の有効期限を 1 日、12 時間、5 分、6 秒にしたい場合は、文字列 `1.12:05:06` を使用します。

```azurecli-interactive
az storage account update \
  --name <storage-account> \
  --resource-group <resource-group> \
  --sas-exp <days>.<hours>:<minutes>:<seconds>
```

> [!TIP]
> SAS の有効期限ポリシーの設定は、ストレージ アカウントの作成時に、[az storage account create](/cli/azure/storage/account#az_storage_account_create) コマンドの `--key-exp-days` パラメーターの設定で行うこともできます。

ポリシーが適用されていることを確認するには、[az storage account show](/cli/azure/storage/account#az_storage_account_show) コマンドを呼び出します。`-query` パラメーターには、`{SasPolicy:sasPolicy}` という文字列を使用します。
  
```azurecli-interactive
az storage account show \
  --name <storage-account> \
  --resource-group <resource-group> \
  --query "{SasPolicy:sasPolicy}"
```

コンソールの出力に SAS の有効期限が表示されます。

```json
{
  "SasPolicy": {
    "expirationAction": "Log",
    "sasExpirationPeriod": "1.12:05:06"
  }
}
```

---

## <a name="query-logs-for-policy-violations"></a>ログでポリシー違反を照会する

SAS の有効期限ポリシーが推奨するよりも長い間隔で有効な SAS の作成をログに記録するには、まず、Azure Log Analytics ワークスペースにログを送信する診断設定を作成します。 詳細については、「[Azure Log Analytics にログを送信する](../blobs/monitor-blob-storage.md#send-logs-to-azure-log-analytics)」を参照してください。

次に、Azure Monitor ログ クエリを使用して、SAS の有効期限が切れているかどうかを確認します。 Log Analytics ワークスペースで新しいクエリを作成し、次のクエリ テキストを追加して、 **[実行]** を押します。

```kusto
StorageBlobLogs | where SasExpiryStatus startswith "Policy Violated" 
```

## <a name="see-also"></a>関連項目

- [共有アクセス署名 (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](storage-sas-overview.md)
- [サービス SAS を作成する](/rest/api/storageservices/create-service-sas)
- [ユーザーの委任 SAS の作成](/rest/api/storageservices/create-user-delegation-sas)
- [アカウント SAS を作成する](/rest/api/storageservices/create-account-sas)