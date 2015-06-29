<properties
   pageTitle="Azure Batch PowerShell コマンドレットの使用 | Microsoft Azure"
   description="Azure Batch サービスの管理に使用される Azure PowerShell コマンドレットを紹介します。"
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
   ms.date="05/29/2015"
   ms.author="danlep"/>

# Azure Batch PowerShell コマンドレットの使用
この記事では、Batch アカウントの管理と Batch 作業項目、ジョブ、タスクに関する情報の取得に使用できる Azure PowerShell コマンドレットについて簡単に紹介します。

コマンドレット構文の詳細については、「`get-help <Cmdlet_name>`」と入力するか、または[Azure Batch コマンドレット リファレンス](https://msdn.microsoft.com/library/azure/mt125957.aspx)のページを参照してください。


## 前提条件

* **Batch プレビュー** - [Batch プレビュー](https://account.windowsazure.com/PreviewFeatures)にサインアップし (まだの場合)、サービスを使用します。
* **Azure PowerShell** - 前提条件、ダウンロード手順、インストール手順については、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」をご覧ください。Batch コマンドレットは、0.8.10 以降のバージョンで導入されました。

## Batch コマンドレットを使用する

標準的な手順を使用して Azure PowerShell を起動し、[Azure サブスクリプションに接続します](../powershell-install-configure.md#Connect)。さらに次の操作を実行します。

* **Azure サブスクリプションを選択する** - サブスクリプションが複数がある場合は、Batch プレビュー機能を追加したサブスクリプションを選択します。

    ```
    Select-AzureSubscription -SubscriptionName <SubscriptionName>
    ```

* **AzureResourceManage モードに切り替える** - Batch コマンドレットは Azure Resource Manager モジュールに付属しています。詳細については、「[リソース マネージャーでの Windows PowerShell の使用](../powershell-azure-resource-manager.md)」をご覧ください。このモジュールを使用するには、[Switch-azuremode](https://msdn.microsoft.com/library/dn722470.aspx) コマンドレットを実行します。

    ```
    Switch-AzureMode -Name AzureResourceManager
    ```

## Batch アカウントとキーを管理する

Azure PowerShell コマンドレットを使用すると、Batch アカウントとキーを作成して管理できます。

### Batch アカウントを作成する

**New-AzureBatchAccount** は、指定したリソース グループに新しい Batch アカウントを作成します。リソース グループがない場合は、[New-azureresourcegroup](https://msdn.microsoft.com/library/dn654594.aspx) コマンドレットを実行して作成し、**Location** パラメーターでいずれかの Azure リージョンを指定します ([Get-azurelocation](https://msdn.microsoft.com/library/dn654582.aspx) を実行して、別の Azure リソースの利用可能なリージョンを検索できます)。 次に例を示します。

```
New-AzureResourceGroup –Name MyBatchResourceGroup –location "Central US"
```

次に、リソース グループに新しいBatch アカウントを作成し、<*account_name*> にアカウント名と、Batch サービスが使用可能な場所を指定します。アカウントの作成は完了までに数分かかる場合があります。次に例を示します。

```
New-AzureBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName MyBatchResourceGroup
```

> [AZURE.NOTE]Batch アカウント名は Azure に一意である必要があります。3 ～ 24 文字で小文字と数字のみを使用します。

### アカウントのアクセス キーを取得する
**Get AzureBatchAccountKeys** は、Azure Batch アカウントに関連付けられているアクセス キーを示します。たとえば、作成したアカウントのプライマリ キーとセカンダリ キーを取得するには、次のように実行します。

```
$Account = Get-AzureBatchAccountKeys –AccountName <accountname>
$Account.PrimaryAccountKey
$Account.SecondaryAccountKey
```

### 新しいアクセス キーを生成する
**New-AzureBatchAccountKey** は、Azure Batch アカウントの新しいプライマリ アカウント キーまたはセカンダリ アカウント キーを生成します。たとえば、Batch アカウントの新しいプライマリ キーを生成するには、次のように入力します。

```
New-AzureBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [AZURE.NOTE]新しいセカンダリ キーを生成するには、**KeyType** パラメーターの "Secondary" を指定します。プライマリ キーとセカンダリ キーは個別に再生成する必要があります。

### Batch アカウントを削除する
**Remove-AzureBatchAccount** は、Batch アカウントを削除します。次に例を示します。

```
Remove-AzureBatchAccount -AccountName <account_name>
```

メッセージが表示されたら、削除するアカウントを確認します。アカウントの削除は完了するまでに時間がかかる場合があります。

## 作業項目、ジョブ、タスクのクエリ

**Get AzureBatchWorkItem**、**Get AzureBatchJob**、**Get AzureBatchTask**、**Get AzureBatchPool** などのコマンドレットを使用して、Batch アカウントで作成されたエンティティを照会します。

これらのコマンドレットを使用するには、まず AzureBatchContext オブジェクトを作成してアカウント名とキーを格納する必要があります。

```
$context = Get-AzureBatchAccountKeys "<account_name>"
```

**BatchContext** パラメーターを使用して、このコンテキストを Batch サービスと対話するコマンドレットに渡します。

> [AZURE.NOTE]既定では、アカウントのプライマリ キーは認証に使用されますが、BatchAccountContext オブジェクトの **KeyInUse** プロパティ ```$context.KeyInUse = "Secondary"``` を変更することで使用するキーを明示的に選択できます。


### データのクエリ

たとえば、作業項目を検索するには、**Get AzureBatchWorkItem** を使用します。既定では、これは、既に BatchAccountContext オブジェクトが *$context* に格納されていると仮定して、自分のアカウントのすべての作業項目を照会します。

```
Get-AzureBatchWorkItem -BatchContext $context
```

同じことをプールなどの他のエンティティで実行できます。

```
Get-AzureBatchPool -BatchContext $context
```
### OData フィルターを使用する

**Filter** パラメーターを使用して OData フィルターを指定すると、関心のあるオブジェクトのみを検索できます。次の例では、"myWork" で始まる名前のすべての作業項目を検索できます。

```
$filter = "startswith(name,'myWork') and state eq 'active'"
Get-AzureBatchWorkItem -Filter $filter -BatchContext $context
```

このメソッドは、ローカルのパイプラインで “Where-Object” を使用するほど柔軟ではありません。ただし、クエリは Batch サービスに直接送信されるため、すべてのフィルター処理がサーバー側で行われ、インターネットの帯域幅を節約できます。

### Name パラメーターを使用する

OData フィルターに代わる方法は、**Name** パラメーターを使用することです。"MyWorkItem" という名前の特定の作業項目を照会するには、次のように実行します。

```
Get-AzureBatchWorkItem -Name "myWorkItem" -BatchContext $context

```
**Name** パラメーターは、完全名の検索のみをサポートし、ワイルドカードや OData 形式のフィルターはサポートしません。

### パイプラインを使用する

Batch コマンドレットは、コマンドレット間でデータを送信するために PowerShell パイプラインを活用できます。これは、パラメーターを指定するのと同じ効果がありますが、複数のエンティティを簡単に一覧表示できます。たとえば、自分のアカウントのすべてのタスクを表示できます。

```
Get-AzureBatchWorkItem -BatchContext $context | Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context
```

### MaxCount パラメーターを使用する

既定では、各コマンドレットは最大で 1000 のオブジェクトを返します。この制限に達した場合は、オブジェクトが少なくなるようにフィルターで絞り込むか、**MaxCount** パラメーターを使用して明示的に最大値を設定できます。次に例を示します。

```
Get-AzureBatchWorkItem -MaxCount 2500 -BatchContext $context

```

上限を削除するには、**MaxCount** を 0 以下に設定します。

## 関連トピック
* [Batch の技術概要](batch-technical-overview.md)
* [Azure PowerShell のダウンロード](http://go.microsoft.com/p/?linkid=9811175)
* [Azure Batch コマンドレット リファレンス](https://msdn.microsoft.com/library/azure/mt125957.aspx)
* [効率的なリスト クエリ](batch-efficient-list-queries.md)

<!---HONumber=58_postMigration-->