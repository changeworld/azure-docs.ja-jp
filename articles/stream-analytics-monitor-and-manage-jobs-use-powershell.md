<properties title="Azure Stream Analytics monitor and manage jobs using Azure PowerShell" pageTitle="PowerShell を使用した Stream Analytics 監視および管理ジョブ | Azure" description="Azure PowerShell コマンドレットを使用して、監視し、ストリーム分析のジョブを管理する方法を学習します。" metaKeywords="" services="stream-analytics" solutions="" documentationCenter="" authors="jgao" videoId="" scriptId="" manager="paulettm" editor="cgronlun"/>

<tags ms.service="stream-analytics" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="data-services" ms.date="12/9/2014" ms.author="jgao" />


#Azure PowerShell を使用した Stream Analytics ジョブの監視および管理

Azure PowerShell を使用した Azure Stream Analytics リソースの管理方法を説明します。

##この記事の内容

- [前提条件](#prerequisites)
- [Stream Analytics PowerShell コマンドレット](#cmdlets)
	- [Get-AzureStreamAnalyticsJob](#Get-AzureStreamAnalyticsJob)
	- [Get-AzureStreamAnalyticsInput](#Get-AzureStreamAnalyticsInput)
	- [Get-AzureStreamAnalyticsOutput](#Get-AzureStreamAnalyticsOutput)
	- [Get-AzureStreamAnalyticsQuota](#Get-AzureStreamAnalyticsQuota)
	- [Get-AzureStreamAnalyticsTransformation](#Get-AzureStreamAnalyticsTransformation)
	- [New-AzureStreamAnalyticsInput](#New-AzureStreamAnalyticsInput)
	- [New-AzureStreamAnalyticsJob](#New-AzureStreamAnalyticsJob)
	- [New-AzureStreamAnalyticsOutput](#New-AzureStreamAnalyticsOutput)
	- [New-AzureStreamAnalyticsTransformation](#New-AzureStreamAnalyticsTransformation)
	- [Remove-AzureStreamAnalyticsInput](#Remove-AzureStreamAnalyticsInput)
	- [Remove-AzureStreamAnalyticsJob](#Remove-AzureStreamAnalyticsJob)
	- [Remove-AzureStreamAnalyticsOutput](#Remove-AzureStreamAnalyticsOutput)
	- [Start-AzureStreamAnalyticsJob](#Start-AzureStreamAnalyticsJob)
	- [Stop-AzureStreamAnalyticsJob](#Stop-AzureStreamAnalyticsJob)
	- [Test-AzureStreamAnalyticsInput](#Test-AzureStreamAnalyticsInput)
	- [Test-AzureStreamAnalyticsOutput](#Test-AzureStreamAnalyticsOutput)
- [関連項目](#seealso)

## <a name="prerequisites"></a>Stream Analytics PowerShell コマンドレットを実行するための前提条件

1.	Azure Powershell のインストールおよび構成

	Azure PowerShell をインストールするには、「[Azure PowerShell のインストールおよび構成方法][powershell-install]」の手順に従います。

2.	Azure モードの構成

	Azure PowerShell をインストールしたら、Stream Analytics コマンドレットにアクセスするための適切な Azure モードを設定するために、[Switch-AzureMode][msdn-switch-azuremode] コマンドレットを実行します。

		Switch-AzureMode AzureResourceManager

>[WACOM.NOTE] Azure PowerShell 介して作成した Stream Analytics ジョブでは監視を有効にできないという一時的な制限事項があります。この問題を回避するには、Azure ポータルのジョブの [監視] ページに移動して、[有効にする] ボタンをクリックします。  

##<a name="cmdlets"></a>Stream Analytics PowerShell コマンドレット
次の表は、Azure PowerShell を使用した Azure Stream Analytics ジョブの監視と管理に使用できるコマンドレットの一覧です。

###<a name="Get-AzureStreamAnalyticsJob"></a>Get-AzureStreamAnalyticsJob
Azure サブスクリプションまたは指定したリソースグループに定義されているすべての Stream Analytics ジョブを一覧表示したり、リソース グループ内の特定のジョブに関するジョブ情報を取得します。

**例 1**

	Get-AzureStreamAnalyticsJob

このコマンドは、Azure サブスクリプションのすべての Stream Analytics ジョブに関する情報を返します。

**例 2**

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US 
このコマンドは、リソース グループ StreamAnalytics-Default-West-US のすべての Stream Analytics ジョブに関する情報を返します。

**例 3**

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob
このコマンドは、リソース グループ StreamAnalytics-Default-West-US の Stream Analytics ジョブ StreamingJob に関する情報を返します。

###<a name="Get-AzureStreamAnalyticsInput"></a>Get-AzureStreamAnalyticsInput
指定した Stream Analytics ジョブに定義されたすべての入力を一覧表示したり、特定の入力に関する情報を取得します。

**例 1**

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob

このコマンドは、ジョブ StreamingJob に定義されたすべての入力に関する情報を返します。

**例 2**

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name EntryStream
このコマンドは、ジョブ StreamingJob に定義された EntryStream という名前の入力に関する情報を返します。

###<a name="Get-AzureStreamAnalyticsOutput"></a>Get-AzureStreamAnalyticsOutput
指定した Stream Analytics ジョブに定義されたすべての出力を一覧表示したり、特定の出力に関する情報を取得します。

**例 1**

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob
このコマンドは、ジョブ StreamingJob に定義されたすべての出力に関する情報を返します。

**例 2**

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name Output
このコマンドは、ジョブ StreamingJob に定義された Output という名前の出力に関する情報を返します。

###<a name="Get-AzureStreamAnalyticsQuota"></a>Get-AzureStreamAnalyticsQuota
指定したリージョンのストリーミング ユニット クォータに関する情報を取得します。

**例 1**

	Get-AzureStreamAnalyticsQuota -Location "West US" 
このコマンドは、米国西部のストリーミング ユニット クォータと使用状況に関する情報を返します。

###<a name="Get-AzureStreamAnalyticsTransformation"></a>Get-AzureStreamAnalyticsTransformation
Stream Analytics ジョブに定義されている特定の変換に関する情報を取得します。

**例 1**

	Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name StreamingJob
このコマンドは、StreamingJob ジョブ上の StreamingJob という名前の変換に関する情報を返します。

###<a name="New-AzureStreamAnalyticsInput"></a>New-AzureStreamAnalyticsInput
Stream Analytics ジョブ内に新しい入力を作成したり、指定した既存の入力を更新します。
  
入力の名前は .JSON ファイルまたはコマンド ラインで指定できます。両方が指定する場合は、コマンド ラインの名前をファイル内の名前と同じにする必要があります。

既に存在する入力を指定し、-Force パラメーターを指定しない場合は、既存の入力を置き換えるかどうかを尋ねられます。

-Force パラメーターを指定し、既存の入力名を指定すると、確認を求められることなく、入力が置き換えられます。

**例 1**

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -File "C:\Input.json" 
このコマンドは、ファイル Input.json から新しい入力を作成します。入力定義ファイル内で指定した名前と同じ名前を持つ入力が既に存在する場合は、入力を置き換えるかどうかを尋ねられます。

**例 2**
	
	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
このコマンドは EntryStream という名前のジョブに新しい入力を作成します。この名前の入力が既に定義されている場合は、入力を置き換えるかどうかを尋ねられます。

**例 3**

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
このコマンドは EntryStream という名前の既存の入力ソースの定義をファイルの定義に置き換えます。

###<a name="New-AzureStreamAnalyticsJob"></a>New-AzureStreamAnalyticsJob
Microsoft Azure に新しい Stream Analytics ジョブを作成したり、指定した既存のジョブの定義を更新します。

ジョブの名前は .JSON ファイルまたはコマンド ラインで指定できます。両方が指定する場合は、コマンド ラインの名前をファイル内の名前と同じにする必要があります。

既に存在するジョブ名を指定し、-Force パラメーターを指定しない場合は、既存のジョブを置き換えるかどうかを尋ねられます。

-Force パラメーターを指定し、既存のジョブ名を指定すると、確認を求められることなく、ジョブ定義が置き換えられます。

**例 1**

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\JobDefinition.json" 
このコマンドは JobDefinition.json の定義から新しいジョブを作成します。ジョブ定義ファイル内で指定した名前と同じ名前を持つジョブが既に存在する場合は、ジョブを置き換えるかどうかを尋ねられます。

**例 2**

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
このコマンドは StreamingJob のジョブ定義を置き換えます。

###<a name="New-AzureStreamAnalyticsOutput"></a>New-AzureStreamAnalyticsOutput
Stream Analytics ジョブ内に新しい出力を作成したり、指定した既存の出力を更新します。  

出力の名前は .JSON ファイルまたはコマンド ラインで指定できます。両方が指定する場合は、コマンド ラインの名前をファイル内の名前と同じにする必要があります。

既に存在する出力を指定し、-Force パラメーターを指定しない場合は、既存の出力を置き換えるかどうかを尋ねられます。

-Force パラメーターを指定し、既存の出力名を指定すると、確認を求められることなく、出力が置き換えられます。

**例 1**

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Output.json" -JobName StreamingJob -Name output
このコマンドは、ジョブ StreamingJob に "output" という名前の新しい出力を作成します。この名前の出力が既に定義されている場合は、出力を置き換えるかどうかを尋ねられます。

**例 2**

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
このコマンドは、ジョブ StreamingJob の "output" の定義を置き換えます。

###<a name="New-AzureStreamAnalyticsTransformation"></a>New-AzureStreamAnalyticsTransformation
Stream Analytics ジョブ内に新しい変換を作成したり、指定した既存の変換を更新します。
  
変換の名前は .JSON ファイルまたはコマンド ラインで指定できます。両方が指定する場合は、コマンド ラインの名前をファイル内の名前と同じにする必要があります。

既に存在する変換を指定し、-Force パラメーターを指定しない場合は、既存の変換を置き換えるかどうかを尋ねられます。

-Force パラメーターを指定し、既存の変換名を指定すると、確認を求められることなく、変換が置き換えられます

**例 1**

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
このコマンドは、ジョブ StreamingJob に StreamingJobTransform という名前の新しい変換を作成します。この名前と同じ名前を持つ変換が既に存在する場合は、置き換えるかどうかを尋ねられます。

**例 2**

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
 このコマンドは、ジョブ StreamingJob の StreamingJobTransform の定義を置き換えます。

###<a name="Remove-AzureStreamAnalyticsInput"></a>Remove-AzureStreamAnalyticsInput
Microsoft Azure 内の Stream Analytics ジョブの特定の入力を非同期的に削除します。  
-Force パラメーターを指定すると、確認を求められることなく、入力が削除されます。

**例 1**
	
	Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name EventStream
このコマンドは、ジョブ StreamingJob 内の入力 EventStream を削除します。  

###<a name="Remove-AzureStreamAnalyticsJob"></a>Remove-AzureStreamAnalyticsJob
Microsoft Azure 内の特定の Stream Analytics ジョブを非同期的に削除します。  
-Force パラメーターを指定すると、確認を求められることなく、ジョブが削除されます。

**例 1**

	Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob 
このコマンドはジョブ StreamingJob を削除します。  

###<a name="Remove-AzureStreamAnalyticsOutput"></a>Remove-AzureStreamAnalyticsOutput
Microsoft Azure 内の Stream Analytics ジョブの特定の出力を非同期的に削除します。  
-Force パラメーターを指定すると、確認を求められることなく、出力が削除されます。

**例 1**

	Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name Output
このコマンドは、ジョブ StreamingJob 内の出力 Output を削除します。  

###<a name="Start-AzureStreamAnalyticsJob"></a>Start-AzureStreamAnalyticsJob
Microsoft Azure に Stream Analytics ジョブを非同期的に展開し開始します。

**例 1**

	Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob 
このコマンドはジョブ StreamingJob を開始します。  

###<a name="Stop-AzureStreamAnalyticsJob"></a>Stop-AzureStreamAnalyticsJob
Microsoft Azure で実行中の Stream Analytics ジョブを非同期的に停止し、使用していたリソースの割り当てを解除します。ジョブを編集したり再開ししたりできるように、Azure ポータルと Management API を使えば、ジョブ定義とメタ データをサブスクリプション内で引き続き利用できます。停止状態のジョブに対しては課金されません。

**例 1**

	Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob 
このコマンドはジョブ StreamingJob を停止します。  

###<a name="Test-AzureStreamAnalyticsInput"></a>Test-AzureStreamAnalyticsInput
指定した入力に接続するために、Stream Analytics の機能をテストします。

**例 1**

	Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name EntryStream
これは StreamingJob 内の入力 EntryStream の接続ステータスをテストします。  

###<a name="Test-AzureStreamAnalyticsOutput"></a>Test-AzureStreamAnalyticsOutput
指定した出力に接続するために、Stream Analytics の機能をテストします。

**例 1**

	Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name Output
これは StreamingJob 内の出力 Output の接続ステータスをテストします。  


##<a name="seealso"></a>関連項目

- [Azure Stream Analytics の概要][stream.analytics.introduction]
- [Stream Analytics の使用][stream.analytics.get.started]
- [Stream Analytics プレビュー リリースの制限事項][stream.analytics.limitations]
- [Stream Analytics 向け開発者ガイド][stream.analytics.developer.guide]
- [Stream Analytics 向けクエリ言語リファレンス][stream.analytics.query.language.reference]
- [Stream Analytics 向け REST API リファレンス][stream.analytics.rest.api.reference]
 



[msdn-switch-azuremode]: http://msdn.microsoft.com/ja-jp/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/ja-jp/documentation/articles/install-configure-powershell/


[stream-analytics-introduction]: ../
[stream-analytics-get-started]
[stream-analytics-limitations]
[stream-analytics-developer-guide]
[stream-analytics-query-language-reference]
[stream-analytics-rest-api-reference]
 


[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.scale.jobs]: ../stream-analytics-scale-jobs/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!--HONumber=35.2-->
