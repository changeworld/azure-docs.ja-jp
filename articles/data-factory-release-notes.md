<properties title="Azure Data Factory - Release Notes" pageTitle="Data Factory - リリース ノート | Azure" description="データの工場出荷時のリリース ノート" metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/04/2014" ms.author="spelluru" />

# Azure Data Factory リリース ノート

## Data Factory ## の 2014 年 12 月 11 日リリース

### 新機能

- Azure Machine Learning 統合
	- Azure Data Factory サービスのこのリリースでは、Azure Data Factory と Azure Machine Learning (ML) を **AzureMLLinkedService** および **AzureMLBatchScoringActivity** を使用して統合できます。詳細については、[「Data Factory および Azure Machine Learning を使用して予測パイプラインを作成する」][adf-azure-ml]を参照してください。 
- ゲートウェイ バージョンのステータスが提供される
	- 現在インストールされているゲートウェイよりも新しいバージョンのゲートウェイを使用できる場合は、"NewVersionAvailable" ステータスが Azure プレビュー ポータルおよび Get-AzureDataFactoryGateway コマンドレットの出力に表示されます。ポータルの指示に従って新しいインストール ファイル (.msi) をダウンロードし、これを実行して最新のゲートウェイをインストールできます。追加の構成は必要ありません。

### 変更点

- HdInsightOnDemandLinkedService 内の JobsContainer が削除されました。
	- HDInsightOnDemandLinkedService の JSON 定義で、**jobsContainer** プロパティを指定する必要がありません。.オンデマンドのリンクされたサービスで指定したプロパティがある場合、そのプロパティは無視されます。リンクされたサービスの JSON 定義からそのプロパティを削除し、New-AzureDataFactoryLinkedService コマンドレットを使用してリンクされたサービスの定義を更新できます。
- HDInsightOnDemandLinkedService のオプションの構成パラメーター
	- このリリースでは、HDInsightOnDemandLinked (オンデマンドの HDInsight クラスター) 用の複数のオプションの構成パラメーターのサポートが導入されています。詳細については、「[ClusterCreateParameters プロパティ][on-demand-hdi-parameters]」を参照してください。
- ゲートウェイの場所が削除される
	- ポータルまたは PowerShell (New-AzureDataFactoryGateway) 経由で Azure Data Factory ゲートウェイを作成する場合は、ゲートウェイの場所を指定する必要がありません。Data Factory 領域は継承されます。同様に、JSON を使用して SQL Server のリンクされたサービスを構成する場合、"gatewayLocation" プロパティは必要ありません。Data Factory .NET SDK もこれらの変更点を反映して更新されます。
	- 古いバージョンの SDK および Azure PowerShell を使用している場合は、引き続き場所を設定する必要があります。
 
     

#### 重大な変更
	
- CustomActivity から DotNetActivity へ
	- **ICustomActivity** インターフェイスの名前が **IDotNetActivity** に変更されました。Data Factory NuGet パッケージを更新し、カスタム アクティビティのソース コードで、ICustomActivity を IDotNetActivity に変更する必要があります。  
	- カスタム アクティビティの JSON 定義におけるカスタム アクティビティの種類を、**CustomActivity** から **DotNetActivity** に変更する必要があります。 
	- **CustomActivity** および **CustomActivityProperties** クラスの名前は、それぞれ、**DotNetActivity** および **DotNetActivityProperties** に変更されました。

		古いバージョンの SDK および Azure PowerShell を使用している場合は、DotNetActivity の代わりに、引き続き CustomActivity を使用できます。
    
  		カスタム アクティビティを作成し、これを Azure Data Factory パイプラインで使用する方法のチュートリアルは、「[Azure Data Factory パイプラインでカスタム アクティビティを使用する][adf-custom-activities] 」を参照してください。  

[adf-azure-ml]: ../data-factory-create-predictive-pipelines
[adf-custom-activities]: ../data-factory-use-custom-activities
[on-demand-hdi-parameters]: http://msdn.microsoft.com/ja-jp/library/microsoft.windowsazure.management.hdinsight.clustercreateparameters_properties.aspx



<!--HONumber=35.2-->
