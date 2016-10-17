<properties
	pageTitle="Machine Learning Management PowerShell コマンドレットを使用した新しい Web サービスの再トレーニング | Microsoft Azure"
	description="Azure Machine Learning で Machine Learning Management PowerShell コマンドレットを使用してプログラムによるモデルの再トレーニングをしてWeb サービスを更新し、新しくトレーニングを行ったモデルを使用する方法について説明します。"
	services="machine-learning"
	documentationCenter=""
	authors="vDonGlover"
	manager="raymondlaghaeian"
	editor=""/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/27/2016"
	ms.author="v-donglo"/>

# Machine Learning Management PowerShell コマンドレットを使用した新しい Web サービスの再トレーニング

新しい Web サービスを再トレーニングする場合は、新しいトレーニング済みのモデルを参照するよう予測 Web サービス定義を更新します。

### 前提条件

「プログラムによる Machine Learning のモデルの再トレーニング」に示されているようにトレーニング実験と予測実験が設定されているものとします。トレーニング実験および予測実験の作成方法については、「[プログラムによる Machine Learning のモデルの再トレーニング](Retrain Machine Learning models programmatically.md)」をご覧ください。

このプロセスでは、Azure Machine Learning コマンドレットがインストールされていることが必要です。Machine Learning コマンドレットのインストール方法については、MSDN の [Azure Machine Learning コマンドレット](https://msdn.microsoft.com/library/azure/mt767952.aspx) リファレンスをご覧ください。
 
再トレーニングの出力から次の情報がコピーされている必要があります。

* BaseLocation
* RelativeLocation

これを実行する手順は次のとおりです。
1.	Azure Resource Manager アカウントにサインインします。
2.	Web サービス定義を取得します。
3.	Web サービス定義を JSON としてエクスポートします。
4.	JSON で ilearner BLOB への参照を更新します。
5.	JSON を Web サービス定義にインポートします。
6.	Web サービスを新しい Web サービス定義で更新します。

### Azure Resource Manager アカウントへのサインイン 

最初に [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) コマンドレットを使用して、PowerShell 環境から Azure アカウントにサインインする必要があります。

### Web サービス定義の取得

[Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) コマンドレットを呼び出して Web サービスを取得します。Web サービス定義は Web サービスのトレーニング済みのモデルの内部的な表現であり、直接変更できるものではありません。(トレーニング実験ではなく) 予測実験の Web サービス定義を取得してください。

	$wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

既存の Web サービスのリソース グループ名を決定するには、パラメーターを指定しないで Get-AzureRmMlWebService コマンドレットを実行しサブスクリプションの Web サービスを表示します。Web サービスを見つけて、その Web サービス ID を確認します。リソース グループの名前は ID の 4 番目の要素で、"*resourceGroups*" 要素の後にあります。次の例では、リソース グループ名は Default-MachineLearning-SouthCentralUS です。

	Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph 
	Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237 
	Name : RetrainSamplePre.2016.8.17.0.3.51.237 
	Location : South Central US 
	Type : Microsoft.MachineLearning/webServices 
	Tags : {} 

または、既存の Web サービスのリソース グループ名を判断するには、Microsoft Azure Machine Learning Web サービス ポータルにログインします。Web サービスを選択します。リソース グループ名は Web サービスの URL の 5 番目の要素で、"*resourceGroups*" 要素の直後にあります。次の例では、リソース グループ名は Default-MachineLearning-SouthCentralUS です。

	https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237 


### Web サービス定義を JSON としてエクスポートします。

新しいトレーニング済みモデルを使用するようにトレーニング済みモデルの定義を変更するには、最初にこれを [Export-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx) コマンドレットを使用して、JSON フォーマット ファイルにインポートする必要があります。
  
	Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

### JSON で ilearner BLOB への参照を更新します。

資産で、[<トレーニング済みモデル>] を見つけ、ilearner BLOB の URI で *locationInfo* の *uri* の値を更新します。URI は BES 再トレーニング呼び出しの出力からの "*BaseLocation* と *RelativeLocation* を組み合わせて作成します。

     "asset3": {
        "name": "Retrain Samp.le [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

### JSON を Web サービス定義にインポートします。

[Import-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) コマンドレットを使用して、変更された JSON ファイルを予測実験の更新に使用できる Web サービス定義に変換します。

	$wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


### Web サービスを新しい Web サービス定義で更新します。

最後に、[Update-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) コマンドレットを使用して、予測実験を更新します。

	Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'  -ServiceUpdates $wsd 

##概要

Machine Learning PowerShell 管理コマンドレットを使用して、予測 Web サービスのトレーニング済みモデルを更新すると、次のようなシナリオを有効にできます。

* 新しいデータでの定期的なモデルの再トレーニング。
* 自身のデータでモデルを再トレーニングすることを目標としている顧客へのモデルの配布。

<!---HONumber=AcomDC_0928_2016-->