<properties
   pageTitle="Azure Batch PowerShell の使用 | Microsoft Azure"
   description="Azure Batch サービスの管理に使用できる Azure PowerShell のコマンドレットについて簡単に説明します。"
   services="batch"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="powershell"
   ms.workload="big-compute"
   ms.date="10/13/2015"
   ms.author="danlep"/>

# Azure Batch PowerShell コマンドレットの使用
この記事では、Batch アカウントの管理と Batch ジョブ、タスク、他の詳細に関する情報の取得に使用できる Azure PowerShell コマンドレットについて簡単に紹介します。

コマンドレット構文の詳細については、「`get-help <Cmdlet_name>`」と入力するか、[Azure Batch コマンドレット リファレンス](https://msdn.microsoft.com/library/azure/mt125957.aspx)のページを参照してください。

[AZURE.INCLUDE [powershell-preview-include](../../includes/powershell-preview-include.md)]

## 前提条件

* **Azure PowerShell** - Batch コマンドレットは Azure リソース マネージャー モジュールに付属しています。前提条件、インストール手順、基本的な使用法については、[Azure リソース マネージャーのコマンドレット](https://msdn.microsoft.com/library/azure/mt125356.aspx)に関するページを参照してください。



* **Batch プロバイダーの名前空間に登録する (1 回限りの操作)** - Batch アカウントを利用するには、Batch プロバイダーの名前空間に登録する必要があります。この操作は、サブスクリプションごとに 1 回のみ実行する必要があります。

    ```
    Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch
    ```

## Batch アカウントとキーを管理する


### Batch アカウントを作成する

**New-AzureRmBatchAccount** は、指定したリソース グループに新しい Batch アカウントを作成します。リソース グループがない場合は、[New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt603739.aspx) コマンドレットを実行して作成し、**Location** パラメーターでいずれかの Azure リージョン ("米国中央部" など) を指定します 次に例を示します。

```
New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"
```

次に、リソース グループに新しい Batch アカウントを作成し、<*account\_name*> にアカウント名と、Batch サービスが使用可能な場所を指定します。アカウントの作成は完了までに数分かかる場合があります。次に例を示します。

```
New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName MyBatchResourceGroup
```

> [AZURE.NOTE]Batch アカウント名は Azure に一意である必要があります。3 ～ 24 文字で小文字と数字のみを使用します。

### アカウントのアクセス キーを取得する
**Get-AzureRmBatchAccountKeys** は、Azure Batch アカウントに関連付けられているアクセス キーを示します。たとえば、作成したアカウントのプライマリ キーとセカンダリ キーを取得するには、次のように実行します。

```
$Account = Get-AzureBatchAccountKeys –AccountName <accountname>

$Account.PrimaryAccountKey

$Account.SecondaryAccountKey
```

### 新しいアクセス キーを生成する
**New-AzureRmBatchAccountKey** は、Azure Batch アカウントの新しいプライマリ アカウント キーまたはセカンダリ アカウント キーを生成します。たとえば、Batch アカウントの新しいプライマリ キーを生成するには、次のように入力します。

```
New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [AZURE.NOTE]新しいセカンダリ キーを生成するには、**KeyType** パラメーターの "Secondary" を指定します。プライマリ キーとセカンダリ キーは個別に再生成する必要があります。

### Batch アカウントを削除する
**Remove-AzureRmBatchAccount** は、Batch アカウントを削除します。次に例を示します。

```
Remove-AzureRmBatchAccount -AccountName <account_name>
```

メッセージが表示されたら、削除するアカウントを確認します。アカウントの削除は完了するまでに時間がかかる場合があります。

## ジョブ、タスク、およびその他の詳細のクエリ

**Get-AzureBatchJob**、**Get-AzureBatchTask**、**Get-AzureBatchPool** などのコマンドレットを使用して、Batch アカウントで作成されたエンティティを照会します。

これらのコマンドレットを使用するには、まず AzureBatchContext オブジェクトを作成してアカウント名とキーを格納する必要があります。

```
$context = Get-AzureRmBatchAccountKeys -AccountName <account_name>
```

**BatchContext** パラメーターを使用して、このコンテキストを Batch サービスと対話するコマンドレットに渡します。

> [AZURE.NOTE]既定では、アカウントのプライマリ キーは認証に使用されますが、BatchAccountContext オブジェクトの **KeyInUse** プロパティ `$context.KeyInUse = "Secondary"` を変更することで使用するキーを明示的に選択できます。


### データのクエリ

たとえば、**Get-AzureBatchPools** を使用してプールを検索します。既定では、これは、既に BatchAccountContext オブジェクトが *$context* に格納されていると仮定して、自分のアカウントのすべてのプールを照会します。

```
Get-AzureBatchPool -BatchContext $context
```
### OData フィルターを使用する

**Filter** パラメーターを使用して OData フィルターを指定すると、関心のあるオブジェクトのみを検索できます。たとえば、ID が "myPool" で始まるすべてのプールを検索できます。

```
$filter = "startswith(id,'myPool')"

Get-AzureBatchPool -Filter $filter -BatchContext $context
```

このメソッドは、ローカルのパイプラインで “Where-Object” を使用するほど柔軟ではありません。ただし、クエリは Batch サービスに直接送信されるため、すべてのフィルター処理がサーバー側で行われ、インターネットの帯域幅を節約できます。

### ID パラメーターの使用

OData フィルターに代わる方法として、**ID** パラメーターを使用します。"myPool" という ID の特定のプールを照会するには:

```
Get-AzureBatchPool -Id "myPool" -BatchContext $context

```
**ID** パラメーターは、完全 ID の検索のみをサポートし、ワイルドカードや OData 形式のフィルターはサポートしません。

### パイプラインを作成する

Batch コマンドレットは、コマンドレット間でデータを送信するために PowerShell パイプラインを活用できます。これは、パラメーターを指定するのと同じ効果がありますが、複数のエンティティを簡単に一覧表示できます。たとえば、次のコマンドでは、自分のアカウントのすべてのタスクを検索しています。

```
Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context
```

### MaxCount パラメーターを使用する

既定では、各コマンドレットは最大で 1000 のオブジェクトを返します。この制限に達した場合は、オブジェクトが少なくなるようにフィルターで絞り込むか、**MaxCount** パラメーターを使用して明示的に最大値を設定してください。次に例を示します。

```
Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

```

上限を削除するには、**MaxCount** を 0 以下に設定します。

## 関連トピック
* [Azure PowerShell のダウンロード](http://go.microsoft.com/?linkid=9811175)
* [Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)
* [Azure Batch コマンドレット リファレンス](https://msdn.microsoft.com/library/azure/mt125957.aspx)
* [効率的な Batch サービスのクエリ](batch-efficient-list-queries.md)

<!---HONumber=Oct15_HO4-->