<properties 
	pageTitle="チュートリアルの出力データを内部設置型 SQL Server データベースにコピーする" 
	description="このチュートリアルでは、Data Factory のチュートリアルを拡張し、内部設置型 SQL Server データベースにマーケティング キャンペーンの有効性のデータをコピーします。"
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="spelluru"/>


# チュートリアル: 内部設置型 SQL Server データベースへのキャンペーンの有効性のデータのコピー
このチュートリアルでは、パイプラインが内部設置型のデータを扱えるようにするため、その環境を設定する方法を学びます。
 
パーティション -> 強化 -> 分析のワークフローを含む最初のチュートリアルにおいて、ログ処理のシナリオの最後の手順で、マーケティング キャンペーンの有効性のアウトプットが Azure SQL データベースにコピーされました。分析のため、所属する組織内にある内部設置型の SQL Server にこのデータを移動することも可能です。
 
マーケティング キャンペーンの有効性データを Azure BLOB から内部設置型の SQL Server にコピーするため、最初のチュートリアルで導入したものと同じコマンドレット一式を使って、内部設置型のリンクされたサービス、テーブル、およびパイプラインを追加で作成する必要があります。

## 前提条件

この記事のチュートリアルを実行する前に、[Data Factory を使用したログ ファイルの移動と処理のチュートリアル][datafactorytutorial]を実行する**必要があります**。

**(推奨)** パイプラインを作成して内部設置型の SQL Server から Azure BLOB ストアにデータを移動するチュートリアルに関する、[パイプラインが内部設置型のデータを扱えるようにする][useonpremisesdatasources]記事内のチュートリアルの確認と練習をしてください。


このチュートリアルでは、次の手順に従います。

1. [手順 1: Data Management Gateway を作成する](#OnPremStep1)。Data Management Gateway は、所属する組織内の内部設置型のデータ ソースに、クラウドからのアクセスを提供するクライアント エージェントです。このゲートウェイによって、内部設置型の SQL Server と Azure データ ストアの間でデータ転送が可能になります。	

	内部設置型の SQL Server データベースをリンクされたサービスとして Azure データ ファクトリに追加する前に、企業環境内に少なくとも 1 つのゲートウェイがインストールされており、それが Azure Data Factory に登録されている必要があります。

2. [手順 2: 内部設置型の SQL Server 用にリンクされたサービスを作成する](#OnPremStep2)。この手順では、まず内部設置型の SQL Server コンピューター上にデータベースとテーブルを作成し、その後リンクされたサービスの **OnPremSqlLinkedService** を作成します。
3. [手順 3: テーブルとパイプラインを作成する](#OnPremStep3)。この手順では、**MarketingCampaignEffectivenessOnPremSQLTable** テーブルと **EgressDataToOnPremPipeline** パイプラインを作成します。 

4. [手順 4: パイプラインを監視して結果を確認する](#OnPremStep4)。この手順では、Azure ポータルを使用して、パイプライン、テーブル、およびデータ スライスを監視します。


## <a name="OnPremStep1"></a>手順 1: Data Management Gateway を作成する

Data Management Gateway は、所属する組織内の内部設置型のデータ ソースに、クラウドからのアクセスを提供するクライアント エージェントです。このゲートウェイによって、内部設置型の SQL Server と Azure データ ストアの間でデータ転送が可能になります。
  
内部設置型の SQL Server データベースをリンクされたサービスとして Azure データ ファクトリに追加する前に、企業環境内に少なくとも 1 つのゲートウェイがインストールされており、それが Azure Data Factory に登録されている必要があります。

すでに使用できるデータ ゲートウェイがある場合は、この手順をスキップします。

1.	論理データ ゲートウェイを作成します。 **Azure プレビュー ポータル**, 、] をクリックして **関連付けられているサービス** 上、 **データ ファクトリ** ブレードします。
2.	コマンド バーの **[+ データ ゲートウェイ]** をクリックします。  
3.	**[新しいデータ ゲートウェイ]** ブレードで、**[作成]** をクリックします。
4.	**[作成]** ブレードで、データ ゲートウェイの **[名前]** に「**MyGateway**」と入力します。
5.	**[地域を選択]** をクリックし、必要があれば変更します。 
6.	**[作成]** ブレードで **[OK]** をクリックします。 
7.	**[構成]** ブレードが表示されます。 
8.	**[構成]** ブレードで、**[このコンピューターに直接インストール]** をクリックします。これによりゲートウェイのダウンロード、インストール、およびコンピューター上での構成が行われ、サービスに登録されます。すでにコンピューターにインストール済みのゲートウェイがあり、それをポータル上に作成したこの論理ゲートウェイにリンクさせたい場合、このブレード上のキーを使用して Data Management Gateway Manager (プレビュー) ツールでゲートウェイの再登録をします。

	![Data Management Gateway Configuration Manager][image-data-factory-datamanagementgateway-configuration-manager]

9. **[OK]** をクリックして **[構成]** ブレードを閉じ、**[OK]** をクリックして **[作成]** ブレードを閉じます。**[関連付けられているサービス]** ブレードの **MyGateway** の状態が **[良好]** に変わるまで待ちます。また、**Data Management Gateway Configuration Manager (プレビュー)** ツールを起動し、ゲートウェイの名前がポータル上の名前と一致すること、および**状態**が **[登録済み]** であることを確認することもできます。最新の状態を確認するため、場合によっては [リンクされたサービス] ブレードをいったん閉じて再度開く必要があります。画面が最新の状態に更新されるまで、数分かかる場合があります。

## <a name="OnPremStep2"></a>手順 2: 内部設置型の SQL Server 用にリンクされたサービスを作成する。

この手順では、まず内部設置型の SQL Server コンピューター上にデータベースとテーブルを作成し、その後リンクされたサービスを作成します。

### 内部設置型のデータベースとテーブルを準備する

最初に、SQL Server データベース、テーブル、ユーザー定義型、およびストアド プロシージャを作成する必要があります。これらは **MarketingCampaignEffectiveness** の結果を Azure BLOB から SQL Server データベースに移動するために使用されます。

1.	**エクスプローラー**で、**C:\ADFWalkthrough** (またはサンプルを展開した場所) にある **OnPremises** サブフォルダーに移動します。
2.	お好みのエディターで **prepareOnPremDatabase&Table.ps1** を開き、強調表示部分を自分の SQL Server 情報に置き換え、ファイルを保存します (**SQL 認証**の詳細を入力してください)。このチュートリアルのために、データベースの SQL 認証を有効にします。 
			
		$dbServerName = "<servername>"
		$dbUserName = "<username>"
		$dbPassword = "<password>"

3. **Azure PowerShell** で、**C:\ADFWalkthrough\OnPremises** フォルダーに移動します。
4.	**prepareOnPremDatabase&Table.ps1** を実行します **(& を二重引用符で囲むか、以下のようにします)**。
			
		& '.\prepareOnPremDatabase&Table.ps1'

5. スクリプトが正常に実行されると、以下が表示されます。
			
		PS E:\ADF\ADFWalkthrough\OnPremises> & '.\prepareOnPremDatabase&Table.ps1'
		6/10/2014 10:12:33 PM Script to create sample on-premises SQL Server Database and Table
		6/10/2014 10:12:33 PM Creating the database [MarketingCampaigns], table and stored procedure on [.]...
		6/10/2014 10:12:33 PM Connecting as user [sa]
		6/10/2014 10:12:33 PM Summary:
		6/10/2014 10:12:33 PM 1. Database 'MarketingCampaigns' created.
		6/10/2014 10:12:33 PM 2. 'MarketingCampaignEffectiveness' table and stored procedure 


### リンクされたサービスの作成

1.	 **Azure プレビュー ポータル**, 、] をクリックして **関連付けられているサービス** タイルで、 **データ ファクトリ** のブレードに **LogProcessingFactory**です。
2.	 **関連付けられているサービス** ブレードをクリックして **追加 (+) データ ストア**です。
3.	 **データ ストアの新しい** ブレードで入力 **OnPremSqlLinkedService** の **名前**です。 
4.	をクリックして **型 (必要な設定)** 選択 **SQL Server**です。表示されます、 **DATA GATEWAY**, 、**サーバー**, 、**データベース**, 、および **資格情報** の設定、 **データ ストアの新しい** ブレードのようになりました。 
5.	をクリックして **DATA GATEWAY (必要な設定を構成する)** 選択 **MyGateway** 前に作成しました。 
6.	入力 **名前** をホストするデータベース サーバーの **MarketingCampaigns** データベース。 
7.	入力 **MarketingCampaigns** データベース用です。 
8.	クリックして **資格情報**です。 
9.	 **資格情報** ブレードで] をクリックして **資格情報を安全に設定するには、ここをクリックしてください**です。
10.	最初に、1 回のクリックのアプリケーションをインストールし、起動、 **資格情報の設定 **] ダイアログ ボックス 。11.	 **資格情報の設定** ] ダイアログ ボックスに、入力 **ユーザー名** と **パスワード**, 、] をクリック **OK**です。ダイアログ ボックスが閉じるまで待ちます。 
12.	クリックして **[ok]** で、 **データ ストアの新しい** ブレードします。 
13.	 **関連付けられているサービス** ブレードでいることを確認 **OnPremSqlLinkedService** 一覧に表示され、 **状態** がリンクされているサービスの **良い**です。

## <a name="OnPremStep3"></a>手順 3: テーブルとパイプラインを作成する

### 内部設置型の論理テーブルを作成する

1.	 **Azure PowerShell**, に切り替え、 **C:\ADFWalkthrough\OnPremises** フォルダーです。 
2.	コマンドレットを使用して **新規 AzureDataFactoryTable** の次のようにテーブルを作成する **MarketingCampaignEffectivenessOnPremSQLTable.json**です。

			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessOnPremSQLTable.json
	 
#### パイプラインを作成して Azure BLOB から SQL Server へデータをコピーする

1.	コマンドレットを使用して **新規 AzureDataFactoryPipeline** の次のように、パイプラインを作成する **EgressDataToOnPremPipeline.json**です。

			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\EgressDataToOnPremPipeline.json
	 
2. コマンドレットを使用して **セット AzureDataFactoryPipelineActivePeriod** アクティブな期間の指定に **EgressDataToOnPremPipeline**です。

			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name EgressDataToOnPremPipeline

	キーを押して **'Y'** を続行します。
	
## <a name="OnPremStep4"></a>手順 4: パイプラインを監視して結果を確認する

導入されたのと同じ手順を使用して今すぐ [手順 6: テーブルとパイプラインを監視する](#MainStep6) 新しいパイプラインと内部設置型の新しい ADF テーブルのデータ スライスを監視します。
 
**MarketingCampaignEffectivenessOnPremSQLTable** テーブルのスライスの状態が [準備完了] に変われば、パイプラインがスライスの実行を完了したことを意味します。結果を表示するには、SQL Server 内の **MarketingCampaigns** データベースの **MarketingCampaignEffectiveness** テーブルにクエリを実行します。
 
ご利用ありがとうございます。 内部設置型のデータ ソースを使用するためのチュートリアルを無事に終了しました。
 

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[datafactorytutorial]: data-factory-tutorial-using-powershell.md
[adfgetstarted]: data-factory-get-started.md
[adfintroduction]: data-factory-introduction.md
[useonpremisesdatasources]: data-factory-use-onpremises-datasources.md
[usepigandhive]: data-factory-pig-hive-activities.md

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx

[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial-extend-onpremises-using-powershell/DataManagementGatewayConfigurationManager.png

<!---HONumber=GIT-SubDir--> 