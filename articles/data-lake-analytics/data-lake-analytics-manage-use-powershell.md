---
title: "Azure PowerShell を使用する Azure Data Lake Analytics の管理 | Microsoft Docs"
description: "Data Lake Analytics アカウント、データ ソース、ジョブ、カタログ項目を管理する方法について説明します。 "
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/26/2017
ms.author: mahi
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 86648ebc5e13570050bb142158bf1aa356d466b3
ms.contentlocale: ja-jp
ms.lasthandoff: 06/28/2017


---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Azure PowerShell を使用する Azure Data Lake Analytics の管理
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Azure PowerShell を使用して、Azure Data Lake Analytics のアカウント、データ ソース、ジョブ、カタログ項目を管理する方法について説明します。 

## <a name="prerequisites"></a>前提条件

Data Lake Analytics アカウントを作成するには、次の項目を把握している必要があります。

* **サブスクリプション ID**: Data Lake Analytics アカウントが存在する Azure サブスクリプション ID。
* **リソース グループ**: Data Lake Analytics アカウントが含まれている Azure リソース グループの名前。
* **Data Lake Analytics アカウント名**: アカウント名には小文字と数字のみを含める必要があります。
* **既定の Data Lake Store アカウント**: 各 Data Lake Analytics アカウントには既定の Data Lake Store アカウントがあります。 これらのアカウントは同じ場所に存在する必要があります。
* **場所**: Data Lake Analytics アカウントの場所。"米国東部 2" やサポートされているその他の場所です。 サポートされている場所は、[価格ページ](https://azure.microsoft.com/pricing/details/data-lake-analytics/)で確認できます。

このチュートリアルの PowerShell スニペットでは、以下の変数を使って各情報を格納します。

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in"></a>ログイン

```powershell
Login-AzureRmAccount -SubscriptionId $subId
```

> [!NOTE]
> 今後の使用のためにログイン セッションを保存するには、``Save-AzureRmProfile`` を使用します。 もう一度ログイン セッションを読み込むには、``Select-AzureRmProfile`` を使用します。

## <a name="managing-accounts"></a>アカウントの管理

### <a name="create-a-data-lake-analytics-account"></a>Data Lake Analytics アカウントを作成する

使用する[リソース グループ](../azure-resource-manager/resource-group-overview.md#resource-groups)がまだ存在しない場合は、新たに作成します。 

```powershell
New-AzureRmResourceGroup -Name  $rg -Location $location
```

すべての Data Lake Analytics アカウントに、ログの保存を目的とした既定の Data Lake Store アカウントが必要です。 既存のアカウントを利用することも、アカウントを作成することもできます。 

```powershell
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
```

リソース グループと Data Lake Store アカウントが用意できたら、Data Lake Analytics アカウントを作成します。

```powershell
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-information-about-an-account"></a>アカウントに関する情報を取得する

アカウントの詳細を取得します。

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

特定の Data Lake Analytics アカウントの存在を確認します。 コマンドレットは、`True` または `False` のどちらかを返します。

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

### <a name="listing-accounts"></a>アカウントを一覧表示する

現在のサブスクリプション内の Data Lake Analytics アカウントを一覧表示します。

```powershell
Get-AdlAnalyticsAccount
```

特定のリソース グループ内の Data Lake Analytics アカウントを一覧表示します。

```powershell
Get-AdlAnalyticsAccount -ResourceGroupName $rg
```

## <a name="managing-firewall-rules"></a>ファイアウォール規則の管理

### <a name="add-or-remove-firewall-rules"></a>ファイアウォール規則の追加または削除

ファイアウォール規則を一覧表示します。

```powershell
Get-AdlAnalyticsFirewallRule -Account $adla
```

ファイアウォール規則を追加します。

```powershell
$ruleName = "Allow access from on-prem server"
$startIpAddress = "<start IP address>"
$endIpAddress = "<end IP address>"

Add-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

ファイアウォール規則を変更します。

```powershell
Set-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

ファイアウォール規則を削除します。

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```

### <a name="enable-or-disable-firewall-rules"></a>ファイアウォール規則の有効化または無効化

ファイアウォール規則を有効にします。

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
```

Azure の IP アドレスを許可します。

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

ファイアウォール規則を無効にします。

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```

## <a name="managing-data-sources"></a>データ ソースの管理
Azure Data Lake Analytics では現在、以下のデータ ソースがサポートされています。

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/storage-introduction.md)

Analytics アカウントを作成する際には、既定のデータ ソースとして Data Lake Store アカウントを指定する必要があります。 既定の Data Lake Store アカウントは、ジョブ メタデータとジョブ監査ログの格納に使用されます。 Data Lake Analytics アカウントを作成した後で、Data Lake Store アカウントやストレージ アカウントを追加できます。 

### <a name="find-the-default-data-lake-store-account"></a>既定の Data Lake Store アカウントの検索

```powershell
$dataLakeStoreName = (Get-AdlAnalyticsAccount -Name $adla).DefaultDataLakeAccount
```

### <a name="add-data-sources"></a>データ ソースの追加

追加のストレージ (BLOB) アカウントを追加します。

```powershell
$AzureStorageAccountName = "<AzureStorageAccountName>"
$AzureStorageAccountKey = "<AzureStorageAccountKey>"

Add-AdlAnalyticsDataSource -Account $adla -Blob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey
```

追加の Data Lake Store アカウントを追加します。

```powershell
$AzureDataLakeStoreName = "<AzureDataLakeStoreAccountName"
Add-AdlAnalyticsDataSource -Account $adla -DataLakeStore $AzureDataLakeStoreName 
```

### <a name="list-data-sources"></a>データ ソースの一覧表示

すべてのアタッチされたデータ ソースを一覧表示します。

```powershell
Get-AdlAnalyticsDataSource -Name $adla
```

アタッチされた Data Lake Store アカウントを一覧表示します。

```powershell
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "DataLakeStore"
```

アタッチされたストレージ アカウントを一覧表示します。

```powershell
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "Blob"
```

## <a name="managing-jobs"></a>ジョブの管理

### <a name="submit-a-job"></a>ジョブの送信

次の U-SQL スクリプトで、ローカルのテキスト ファイルを作成します。

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

このスクリプトを送信します。

```powershell
Submit-AdlJob -AccountName $adla –ScriptPath "<LocalPathToScriptFile>"
```

### <a name="list-jobs"></a>ジョブのリスト

アカウントに存在するすべてのジョブを一覧表示します。 出力結果には、現在実行されているジョブと最近完了したジョブが含まれます。

```powershell
Get-AdlJob -Account $adla
```

過去 7 日以内に "joe@contoso.com" によって送信されたすべての失敗したジョブを一覧表示します。

```powershell
Get-AdlJob -Account $adla `
    -Submitter "joe@contoso.com" `
    -SubmittedAfter (Get-Date).AddDays(-7) `
    -Result Failed
```

### <a name="get-information-about-a-job"></a>ジョブに関する情報の取得

特定のジョブの状態を取得します。

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

ジョブが完了するまで `Get-AdlAnalyticsJob` を繰り返す替わりに、`Wait-AdlJob` コマンドレットを使用してジョブの終了を待機できます。

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

ジョブの終了後、フォルダー内のファイルを一覧表示して、出力ファイルが存在するかどうかを確認します。

```powershell
Get-AdlStoreChildItem -Account $adls -Path "/"
```

ファイルの存在を確認します。

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

### <a name="cancel-a-job"></a>ジョブを取り消す

```powershell
Stop-AdlJob -Account $adls -JobID $jobID
```

## <a name="uploading-and-downloading"></a>アップロードとダウンロード

ファイルをアップロードします。

```powershell
Import-AdlStoreItem -AccountName $adls `
    -Path "<LocalPath>\data.tsv" `
    -Destination "/data_copy.csv" 
```

フォルダー全体をアップロードします。

```powershell
Import-AdlStoreItem -AccountName $adls `
    -Path "<LocalPath>\myData\" `
    -Destination "/myData/" `
    -Recurse
```

ファイルをダウンロードします。

```powershell
Export-AdlStoreItem -AccountName $adls `
    -Path "/data.csv" `
    -Destination "<LocalPath>\data.csv"
```

フォルダー全体をダウンロードします。

```powershell
Export-AdlStoreItem -AccountName $adls `
    -Path "/" `
    -Destination "<LocalPath>\myData\" `
    -Recurse
```

> [!NOTE]
> アップロードまたはダウンロードのプロセスが中断された場合は、``-Resume`` フラグでコマンドレットを再度実行して、プロセスの再開を試みることができます。

## <a name="manage-catalog-items"></a>カタログ項目の管理
U-SQL カタログを使用して、U-SQL スクリプトで共有できるように、データとコードを構成します。 カタログでは、Azure Data Lake のデータを使用して可能な限り最高のパフォーマンスを実現できます。 詳細については、「 [U-SQL カタログの使用](data-lake-analytics-use-u-sql-catalog.md)」を参照してください。

### <a name="list-items"></a>項目の一覧表示

U-SQL データベースを一覧表示します。

```powershell
Get-AdlCatalogItem -Account $adla -ItemType Database 
```

データベース内のテーブルを一覧表示します。

```powershell
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "master"
```

スキーマ内のテーブルを一覧表示します。

```powershell
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "master.dbo"
```

### <a name="get-information-about-a-catalog-item"></a>カタログ項目に関する情報の取得

U-SQL データベースの詳細を取得します。

```powershell
Get-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

U-SQL データベース内のテーブルの詳細を取得します。

```powershell
Get-AdlCatalogItem  -Account $adla -ItemType Table -Path "master.dbo.mytable"
```

U-SQL データベースの存在をテストします。

```powershell
Test-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

### <a name="create-catalog-items"></a>カタログ項目の作成

U-SQL データベース内で、Azure でホストされているデータベースの資格情報オブジェクトを作成します。 現時点で U-SQL 資格情報は、PowerShell を使用して作成できるカタログ項目の唯一の種類です。

```powershell
$dbName = "master"
$credentialName = "ContosoDbCreds"
$dbUri = "https://contoso.database.windows.net:8080"

New-AdlCatalogCredential -AccountName $adla `
          -DatabaseName $db `
          -CredentialName $credentialName `
          -Credential (Get-Credential) `
          -Uri $dbUri
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>テンプレートを使用して Data Lake Analytics アカウントを作成する

次の PowerShell スクリプトを使用して Azure Resource Group テンプレートを使用することもできます。

```powershell
$subId = "<Your Azure Subscription ID>"

$rg = "<New Azure Resource Group Name>"
$location = "<Location (such as East US 2)>"
$adls = "<New Data Lake Store Account Name>"
$adla = "<New Data Lake Analytics Account Name>"

$deploymentName = "MyDataLakeAnalyticsDeployment"
$armTemplateFile = "<LocalFolderPath>\azuredeploy.json"  # update the JSON template path 

# Log in to Azure
Login-AzureRmAccount -SubscriptionId $subId

# Create the resource group
New-AzureRmResourceGroup -Name $rg -Location $location

# Create the Data Lake Analytics account with the default Data Lake Store account.
$parameters = @{"adlAnalyticsName"=$adla; "adlStoreName"=$adls}
New-AzureRmResourceGroupDeployment -Name $deploymentName -ResourceGroupName $rg -TemplateFile $armTemplateFile -TemplateParameterObject $parameters 
```

詳細については、[Azure Resource Manager テンプレートを使用したアプリケーションのデプロイ](../azure-resource-manager/resource-group-template-deploy.md)に関する記事および「[Authoring Azure Resource Manager templates](../azure-resource-manager/resource-group-authoring-templates.md)」(Azure Resource Manager テンプレートの作成) を参照してください。

**テンプレートの例**

以下のテキストを `.json` ファイルとして保存し、前述の PowerShell スクリプトを使用してテンプレートを使用します。 

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adlAnalyticsName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Data Lake Analytics account to create."
      }
    },
    "adlStoreName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Data Lake Store account to create."
      }
    }
  },
  "resources": [
    {
      "name": "[parameters('adlStoreName')]",
      "type": "Microsoft.DataLakeStore/accounts",
      "location": "East US 2",
      "apiVersion": "2015-10-01-preview",
      "dependsOn": [ ],
      "tags": { }
    },
    {
      "name": "[parameters('adlAnalyticsName')]",
      "type": "Microsoft.DataLakeAnalytics/accounts",
      "location": "East US 2",
      "apiVersion": "2015-10-01-preview",
      "dependsOn": [ "[concat('Microsoft.DataLakeStore/accounts/',parameters('adlStoreName'))]" ],
      "tags": { },
      "properties": {
        "defaultDataLakeStoreAccount": "[parameters('adlStoreName')]",
        "dataLakeStoreAccounts": [
          { "name": "[parameters('adlStoreName')]" }
        ]
      }
    }
  ],
  "outputs": {
    "adlAnalyticsAccount": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.DataLakeAnalytics/accounts',parameters('adlAnalyticsName')))]"
    },
    "adlStoreAccount": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.DataLakeStore/accounts',parameters('adlStoreName')))]"
    }
  }
}
```

## <a name="next-steps"></a>次のステップ
* [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
* [Azure Portal](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI 2.0](data-lake-analytics-get-started-cli2.md) で Data Lake Analytics の使用を開始する
* [Azure Portal](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) |  [Azure Portal](data-lake-analytics-manage-use-portal.md) | [CLI](data-lake-analytics-manage-use-cli.md) で Azure Data Lake Analytics を管理する 

