---
title: Azure Batch PowerShell の使用 | Microsoft Docs
description: Azure Batch サービスの管理に使用できる Azure PowerShell のコマンドレットについて簡単に説明します。
services: batch
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''

ms.service: batch
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: powershell
ms.workload: big-compute
ms.date: 07/28/2016
ms.author: danlep

---
# Azure Batch PowerShell コマンドレットの使用
Batch API、Azure ポータル、Azure コマンド ライン インターフェイス (CLI) を使用して実行するタスクの多くは、Azure Batch PowerShell コマンドレットで実行したりスクリプト化したりすることができます。この記事では、Batch アカウントを管理したり、プール、ジョブ、タスクといった Batch リソースを操作したりするときに使用できるコマンドレットについて説明します。この記事は、Azure PowerShell Version 1.6.0 のコマンドレットを基にしています。

すべての Batch コマンドレットの一覧およびコマンドレットの詳細な構文については、[Azure Batch コマンドレットのリファレンス](https://msdn.microsoft.com/library/azure/mt125957.aspx)を参照してください。

## 前提条件
* **Azure PowerShell** - Azure PowerShell をダウンロードしてインストールする手順については、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」をご覧ください。
  
  * Azure Batch コマンドレットは Azure Resource Manager モジュールに付属しているので、**Login-AzureRmAccount** コマンドレットを使用してサブスクリプションに接続する必要があります。
  * 最新のサービスや機能強化を活かすためにも、Azure PowerShell は定期的に更新することをお勧めします。
* **Batch プロバイダーの名前空間に登録する (1 回限りの操作)** - Batch アカウントを利用するには、Batch プロバイダーの名前空間に登録する必要があります。この操作は、サブスクリプションごとに 1 回のみ実行する必要があります。次のコマンドレットを実行します。
  
        Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch

## Batch アカウントとキーを管理する
### Batch アカウントを作成する
**New-AzureRmBatchAccount** は、指定したリソース グループに新しい Batch アカウントを作成します。リソース グループがない場合は、[New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt603739.aspx) コマンドレットを実行して作成し、**Location** パラメーターでいずれかの Azure リージョン ("米国中央部" など) を指定します 次に例を示します。

    New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"


次に、新しい Batch アカウントをリソース グループに作成します。<*account\_name*> にアカウントの名前を指定したうえで、リソース グループの場所と名前を指定します。Batch アカウントの作成は、完了までにしばらく時間がかかる場合があります。次に例を示します。

    New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName MyBatchResourceGroup

> [!NOTE]
> Batch アカウント名は、リソース グループの Azure リージョン内で重複しないこと、文字数が 3 ～ 24 文字であること、小文字と数字のみで構成されていることが必要になります。
> 
> 

### アカウントのアクセス キーを取得する
**Get-AzureRmBatchAccountKeys** は、Azure Batch アカウントに関連付けられているアクセス キーを示します。たとえば、作成したアカウントのプライマリ キーとセカンダリ キーを取得するには、次のように実行します。

    $Account = Get-AzureRmBatchAccountKeys –AccountName <accountname>

    $Account.PrimaryAccountKey

    $Account.SecondaryAccountKey


### 新しいアクセス キーを生成する
**New-AzureRmBatchAccountKey** は、Azure Batch アカウントの新しいプライマリ アカウント キーまたはセカンダリ アカウント キーを生成します。たとえば、Batch アカウントの新しいプライマリ キーを生成するには、次のように入力します。

    New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary


> [!NOTE]
> 新しいセカンダリ キーを生成するには、**KeyType** パラメーターの "Secondary" を指定します。プライマリ キーとセカンダリ キーは個別に再生成する必要があります。
> 
> 

### Batch アカウントを削除する
**Remove-AzureRmBatchAccount** は、Batch アカウントを削除します。次に例を示します。

    Remove-AzureRmBatchAccount -AccountName <account_name>

メッセージが表示されたら、削除するアカウントを確認します。アカウントの削除は完了するまでに時間がかかる場合があります。

## BatchAccountContext オブジェクトを作成する
Batch PowerShell コマンドレットを使用して Batch のリソース (プール、ジョブ、タスクなど) を認証するにはまず、BatchAccountContext オブジェクトを作成して、アカウントの名前とキーを格納します。

    $context = Get-AzureRmBatchAccountKeys -AccountName <account_name>

BatchAccountContext オブジェクトは、**BatchContext** パラメーターを引数として受け取るコマンドレットに渡します。

> [!NOTE]
> 既定では、アカウントのプライマリ キーは認証に使用されますが、BatchAccountContext オブジェクトの **KeyInUse** プロパティ `$context.KeyInUse = "Secondary"` を変更することで使用するキーを明示的に選択できます。
> 
> 

## Batch リソースを作成および変更する
Batch アカウントでリソースを作成するには、**New-AzureBatchPool**、**New-AzureBatchJob**、**New-AzureBatchTask** などのコマンドレットを使用します。既存のリソースのプロパティを更新するための対応する **Get-** コマンドレットと **Set-** コマンドレットがあり、Batch アカウントのリソースを削除するための **Remove-** コマンドレットがあります。

以下の例を見るとわかるように、これらのコマンドレットの多くは、その使用時に、BatchContext オブジェクトを渡すことに加え、リソースの詳細な設定を含んだオブジェクトを作成するか、引数として渡す必要があります。その他の例については、各コマンドレットの詳細なヘルプを参照してください。

### Create a Batch pool
Batch プールを作成または更新する際は、コンピューティング ノードのオペレーティング システムに関してクラウド サービスの構成または仮想マシンの構成を選択します ([Batch 機能の概要](batch-api-basics.md#pool)を参照)。そのどちらを選択したかによって、コンピューティング ノードのイメージ化に、[Azure ゲスト OS のリリース](../cloud-services/cloud-services-guestos-update-matrix.md#releases)の 1 つを使用するか、Azure Marketplace でサポートされている Linux や Windows の VM イメージの 1 つを使用するかが決まります。

オペレーティング システムの設定は、**New-AzureBatchPool** を実行するときに、PSCloudServiceConfiguration オブジェクトまたは PSVirtualMachineConfiguration オブジェクトで渡します。たとえば以下のコマンドレットは、Small サイズのコンピューティング ノードで新しい Batch プールを作成しています。オペレーティング システムにはクラウド サービス構成を選び、最新バージョンであるファミリー 3 (Windows Server 2012) のイメージを使用しています。ここでは、**CloudServiceConfiguration** パラメーターに PSCloudServiceConfiguration オブジェクトとして *$configuration* 変数を指定しています。**BatchContext** パラメーターには、先ほど定義した *$context* 変数を BatchAccountContext オブジェクトとして指定しています。

    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSCloudServiceConfiguration" -ArgumentList @(3,"*")

    New-AzureBatchPool -Id "AutoScalePool" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context

新しいプール内のコンピューティング ノードの目標数は、自動スケールの式によって求められます。この例では、式は **$TargetDedicated=4** という簡単なものであり、プールのコンピューティング ノードの数が最大 4 であることを示します。

## プール、ジョブ、タスク、およびその他の詳細のクエリ
**Get-AzureBatchPool**、**Get-AzureBatchJob**、**Get-AzureBatchTask** などのコマンドレットを使用して、Batch アカウントで作成されたエンティティを照会します。

### データのクエリ
たとえば、**Get-AzureBatchPools** を使用してプールを検索します。既定では、これは、既に BatchAccountContext オブジェクトが *$context* に格納されていると仮定して、自分のアカウントのすべてのプールを照会します。

    Get-AzureBatchPool -BatchContext $context

### OData フィルターを使用する
**Filter** パラメーターを使用して OData フィルターを指定すると、関心のあるオブジェクトのみを検索できます。たとえば、ID が "myPool" で始まるすべてのプールを検索できます。

    $filter = "startswith(id,'myPool')"

    Get-AzureBatchPool -Filter $filter -BatchContext $context


このメソッドは、ローカルのパイプラインで “Where-Object” を使用するほど柔軟ではありません。ただし、クエリは Batch サービスに直接送信されるため、すべてのフィルター処理がサーバー側で行われ、インターネットの帯域幅を節約できます。

### ID パラメーターの使用
OData フィルターに代わる方法として、**ID** パラメーターを使用します。"myPool" という ID の特定のプールを照会するには:

    Get-AzureBatchPool -Id "myPool" -BatchContext $context


**ID** パラメーターは、完全 ID の検索のみをサポートし、ワイルドカードや OData 形式のフィルターはサポートしません。

### MaxCount パラメーターを使用する
既定では、各コマンドレットは最大で 1000 のオブジェクトを返します。この制限に達した場合は、オブジェクトが少なくなるようにフィルターで絞り込むか、**MaxCount** パラメーターを使用して明示的に最大値を設定してください。次に例を示します。

    Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

上限を削除するには、**MaxCount** を 0 以下に設定します。

### パイプラインを作成する
Batch コマンドレットは、コマンドレット間でデータを送信するために PowerShell パイプラインを活用できます。これは、パラメーターを指定するのと同じ効果がありますが、複数のエンティティを簡単に一覧表示できます。たとえば、次のコマンドでは、自分のアカウントのすべてのタスクを検索しています。

    Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context


## 次のステップ
* コマンドレットの詳しい構文と例については、[Azure Batch コマンドレットのリファレンス](https://msdn.microsoft.com/library/azure/mt125957.aspx)を参照してください。
* Batch に対してクエリから返される情報の項目数と種類を制限する方法の詳細については、[効率的な Batch サービスのクエリ](batch-efficient-list-queries.md)に関する記事を参照してください。

<!---HONumber=AcomDC_0803_2016-->