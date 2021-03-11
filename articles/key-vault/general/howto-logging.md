---
title: Azure Key Vault のログ記録を有効にする方法
description: Azure Key Vault のログ記録を有効にする方法。これにより、指定した Azure ストレージ アカウントに情報が保存されます。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: 7b71fc2f3afb67d766bfe267888674b55af6a3a5
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503915"
---
# <a name="how-to-enable-key-vault-logging"></a>Key Vault のログ記録を有効にする方法

1 つまたは複数のキー コンテナーを作成したら、いつ、どのように、誰によってキー コンテナーがアクセスされるのかを監視するのが一般的です。 機能の詳細については、[Key Vault のログ記録](logging.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには次の準備が必要です。

* 使用している既存の Key Vault。  
* [Azure Cloud Shell](https://shell.azure.com) - Bash 環境
* Azure 上に確保された Key Vault のログを格納するための十分なストレージ。

このガイド コマンドは、Bash を環境として [Cloud Shell](https://shell.azure.com) 用にフォーマットされています。

## <a name="connect-to-your-key-vault-subscription"></a>Key Vault サブスクリプションに接続する

キーのログ記録を設定する最初の手順は、キー コンテナーを含むサブスクリプションに接続することです。 これは、アカウントに複数のサブスクリプションが関連付けられている場合に特に重要です。

Azure CLI では、[az account list](/cli/azure/account#az_account_list) コマンドを使用してすべてのサブスクリプションを表示してから、[az account set](/cli/azure/account#az_account_set) を使用してその 1 つに接続できます。

```azurecli-interactive
az account list

az account set --subscription "<subscriptionID>"
```

Azure PowerShell では、最初に [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) コマンドレットを使用してサブスクリプションを一覧表示してから、[Set-AzContext](/powershell/module/az.accounts/set-azcontext) コマンドレットを使用してその 1 つに接続できます。 

```powershell-interactive
Get-AzSubscription

Set-AzContext -SubscriptionId "<subscriptionID>"
```

## <a name="create-a-storage-account-for-your-logs"></a>ログ用のストレージ アカウントを作成する

既存のストレージ アカウントをログのために使用できますが、ここでは Key Vault のログ専用に新しいストレージ アカウントを作成します。 

また、管理を容易にするために、キー コンテナーが含まれているリソース グループと同じリソース グループを使用します。 [Azure CLI のクイックスタート](quick-create-cli.md)と [Azure PowerShell のクイックスタート](quick-create-powershell.md)では、このリソース グループの名前は **myResourceGroup** で、場所は *eastus* になっています。 これらの値は、必要に応じて実際の値に置き換えてください。 

ストレージ アカウント名を指定する必要もあります。 ストレージ アカウント名は、一意であり、長さが 3 ～ 24 文字で、数字と小文字のみを使用する必要があります。  最後に、"Standard_LRS" SKU のストレージ アカウントを作成します。

Azure CLI では、[az storage account create](/cli/azure/storage/account#az_storage_account_create) コマンドを使用します。

```azurecli-interactive
az storage account create --name "<your-unique-storage-account-name>" -g "myResourceGroup" --sku "Standard_LRS"
```

Azure PowerShell では、[New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) コマンドレットを使用します。 リソース グループに対応する場所を指定する必要があります。

```powershell
 New-AzStorageAccount -ResourceGroupName myResourceGroup -Name "<your-unique-storage-account-name>" -Type "Standard_LRS" -Location "eastus"
```

どちらの場合も、ストレージ アカウントの "id" を書き留めます。 Azure CLI の操作では、出力で "id" が返されます。 Azure PowerShell で "id" を取得するには、[Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) を使用して、その出力を変数 $sa に割り当てます。 この後は、$sa.id によってストレージ アカウントを参照できます。(この記事の後方では "$Sa.Context "プロパティも使用されます。)

```powershell-interactive
$sa = Get-AzStorageAccount -Name "<your-unique-storage-account-name>" -ResourceGroup "myResourceGroup"
$sa.id
```

ストレージ アカウントの "id" は、"/subscriptions/<お使いのサブスクリプション ID>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/<お使いの一意のストレージ アカウント名>" という形式になります。

> [!NOTE]
> 既存のストレージ アカウントを使用する場合は、それが、目的のキー コンテナーと同じサブスクリプションを使用している必要があり、クラシック デプロイ モデルではなく Azure Resource Manager デプロイ モデルを使用する必要があります。

## <a name="obtain-your-key-vault-resource-id"></a>キー コンテナーのリソース ID を取得する

[CLI のクイックスタート](quick-create-cli.md)と [PowerShell のクイックスタート](quick-create-powershell.md)では、一意の名前を持つキーを作成しました。  以下の手順では、再度その名前を使用します。  キー コンテナーの名前を覚えていない場合は、Azure CLI の [az keyvault list](/cli/azure/keyvault#az_keyvault_list) コマンドまたは Azure PowerShell の [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault) コマンドレットを使用して名前を一覧表示できます。

キー コンテナーの名前を使用して、それのリソース ID を見つけます。  Azure CLI では、[az keyvault show](/cli/azure/keyvault#az_keyvault_show) コマンドを使用します。

```azurecli-interactive
az keyvault show --name "<your-unique-keyvault-name>"
```

Azure PowerShell では、[Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault) コマンドレットを使用します。

```powershell-interactive
Get-AzKeyVault -VaultName "<your-unique-keyvault-name>"
```

キー コンテナーのリソース ID は、"/subscriptions/<お使いのサブスクリプション ID>/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/<お使いの一意のキー コンテナー名>" という形式になります。 次の手順のためにそれを書き留めます。

## <a name="enable-logging-using-azure-powershell"></a>Azure PowerShell を使用してログ記録を有効にする

Key Vault のログ記録を有効にするには、Azure CLI の [az monitor diagnostics-settings create](/cli/azure/monitor/diagnostic-settings) コマンドまたは [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) コマンドレットを、ストレージ アカウント ID とキー コンテナーのリソース ID と共に使用します。

```azurecli-interactive
az monitor diagnostic-settings create --storage-account "<storage-account-id>" --resource "<key-vault-resource-id>" --name "Key vault logs" --logs '[{"category": "AuditEvent","enabled": true}]' --metrics '[{"category": "AllMetrics","enabled": true}]'
```

Azure PowerShell では、[Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) コマンドレットを使用し、 **-Enabled** フラグを **$true** に設定して、カテゴリを `AuditEvent` に設定します (Key Vault ログ記録の唯一のカテゴリ)。

```powershell-interactive
Set-AzDiagnosticSetting -ResourceId "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category "AuditEvent"
```

必要に応じてログのアイテム保持ポリシーを設定できます。そうすることで、指定した期間の後に古いログが自動的に削除されます。 たとえば、90 日より古いログを自動的に削除するアイテム保持ポリシーを設定できます。

<!-- With the Azure CLI, use the [az monitor diagnostic-settings update](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_update) command. 

```azurecli-interactive
az monitor diagnostic-settings update 
```
-->

Azure PowerShell では、[Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) コマンドレットを使用します。 

```powershell-interactive
Set-AzDiagnosticSetting "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

ログに記録される内容:

* 認証されたすべての REST API 要求。これには、アクセス許可がないため、システム エラーのため、または不正な要求の結果として、失敗した要求が含まれます。
* キー コンテナー自体に関する操作。これには、作成、削除、キー コンテナーのアクセス ポリシーの設定、キー コンテナー属性 (タグなど) の更新が含まれます。
* 次の操作を含む、キー コンテナーのキーとシークレットに関する操作。
  * これらのキーまたはシークレットの作成、変更、または削除。
  * 署名、確認、暗号化、復号化、キーのラップとラップ解除、シークレットの取得、およびキーとシークレット (およびそのバージョン) の一覧表示。
* 結果として 401 応答が発生する、認証されていない要求。 たとえば、ベアラー トークンを持たない要求、形式が正しくない要求、有効期限切れの要求、または無効なトークンを持つ要求です。  
* 期限切れ間近、期限切れ、コンテナーのアクセス ポリシーが変更された場合の Event Grid の通知イベント (新しいバージョンのイベントはログに記録されません)。 キー コンテナーに作成されたイベント サブスクリプションがあるかどうかに関係なく、イベントはログに記録されます。 詳細については、[Key Vault 用の Event Grid イベント スキーマ](../../event-grid/event-schema-key-vault.md)に関するページを参照してください。

## <a name="access-your-logs"></a>ログへのアクセス

Key Vault のログは、指定したストレージ アカウント内の "insights-logs-auditevent" コンテナーに格納されます。 ログを表示するには、BLOB をダウンロードする必要があります。

最初に、コンテナー内のすべての BLOB を一覧表示します。  Azure CLI では、[az storage blob list](/cli/azure/storage/blob#az_storage_blob_list) コマンドを使用します。

```azurecli-interactive
az storage blob list --account-name "<your-unique-storage-account-name>" --container-name "insights-logs-auditevent"
```

Azure PowerShell では、[Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob) を使用して、このコンテナー内のすべての BLOB を一覧表示します。次のように入力します。

```powershell
Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context
```

Azure CLI コマンドまたは Azure PowerShell コマンドレットいずれかの出力からわかるように、BLOB の名前は `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json` という形式になっています。 日付と時刻の値には UTC が使用されます。

同じストレージ アカウントを使用して複数のリソースのログを収集することができるので、必要な BLOB のみにアクセスしたり、ダウンロードしたりする場合には、BLOB 名に完全なリソース ID を使用すると便利です。 その前に、すべての BLOB をダウンロードする方法を説明します。

Azure CLI では、[az storage blob download](/cli/azure/storage/blob#az_storage_blob_download) コマンドを使用して、それに BLOB の名前と、結果を保存するファイルへのパスを渡します。

```azurecli-interactive
az storage blob download --container-name "insights-logs-auditevent" --file <path-to-file> --name "<blob-name>" --account-name "<your-unique-storage-account-name>"
```

Azure PowerShell では、[Gt-AzStorageBlobs](/powershell/module/az.storage/get-azstorageblob) コマンドレットを使用して BLOB の一覧を取得してから、パイプを使用してそれを [Get-AzStorageBlobContent](/powershell/module/az.storage/get-azstorageblobcontent) コマンドレットに渡し、選択したパスにログをダウンロードします。

```powershell-interactive
$blobs = Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context | Get-AzStorageBlobContent -Destination "<path-to-file>"
```

この 2 番目のコマンドレットを PowerShell で実行すると、BLOB 名に含まれる **/** 区切り記号によって、宛先フォルダーの下にフォルダー構造全体が作成されます。 この構造は、BLOB をファイルとしてダウンロードし、保存するために使用します。

BLOB を選択的にダウンロードするには、ワイルドカードを使用します。 次に例を示します。

* 複数の Key Vault を持っている場合に、CONTOSOKEYVAULT3 という名前の Key Vault のみについてログをダウンロードするには、次のようにします。

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* 複数のリソース グループを持っている場合、1 つのリソース グループのみについてログをダウンロードするには、次のように `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`を使用します。

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* 2019 年 1 月のすべてのログをダウンロードする場合は、次のように `-Blob '*/year=2019/m=01/*'` を使用します。

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

これで、ログの内容を検討する準備が整いました。 ただし、検討に移る前に、他の 2 つのコマンドを知っておく必要があります。

ログの読み方の詳細については、[Key Vault のログ記録に関するページの「Key Vault のログを解釈する」](logging.md#interpret-your-key-vault-logs)を参照してください

## <a name="use-azure-monitor-logs"></a>Azure Monitor ログの使用

Azure Monitor ログの Key Vault ソリューションを使用して、Key Vault の `AuditEvent` ログを調査することができます。 Azure Monitor ログでは、ログ クエリを使用してデータを分析し、必要な情報を取得します。

この設定方法などの詳細については、[Azure Monitor の Azure Key Vault](../../azure-monitor/insights/key-vault-insights-overview.md) に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

- 概念的な情報については、Key Vault のログを解釈する方法を含め、[Key Vault のログ記録](logging.md)に関するページを参照してください
- .NET Web アプリケーションでの Azure Key Vault の使用方法に関するチュートリアルについては、「[Web アプリケーションからの Azure Key Vault の使用](tutorial-net-create-vault-azure-web-app.md)」を参照してください。
- プログラミング リファレンスについては、「 [Azure Key Vault 開発者ガイド](developers-guide.md)」を参照してください。
