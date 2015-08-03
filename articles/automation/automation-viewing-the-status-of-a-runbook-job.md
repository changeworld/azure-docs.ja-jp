<properties 
   pageTitle="Azure Automation で Runbook ジョブの状態を表示する方法"
   description="Azure Automation で runbook を開始するときに、ジョブが作成されます。この記事は、各ジョブを追跡し、現在の状態を表示する方法を説明します。"
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/30/2015"
   ms.author="bwren" />

# Azure Automation で Runbook ジョブの状態を表示する方法


Azure Automation で runbook を開始するときに、ジョブが作成されます。ジョブは、Runbook の単一の実行インスタンスです。単一の Runbook に対して、対応するジョブが複数個あり、Runbook のパラメーターとしてそれぞれ独自の値セットが指定されているということが可能です。1 つ以上の Runbook の特定のジョブおよびすべてのジョブの状態を確認する方法は複数あります。

## ジョブの状態

次の表には、ジョブが取り得るさまざまな状態を説明します。

| 状態| 説明|
|:---|:---|
|完了|ジョブは正常に完了しました。|
|失敗|ジョブはエラーで終了しました。|
|失敗、リソースを待機中|ジョブは [fair share](http://msdn.microsoft.com/library/azure/3179b655-ab39-407a-9169-33571f958325#fairshare) の限界に 3 回到達し、毎回、同じチェックポイントから、または Runbook の先頭から起動したために、失敗しました。|
|キューに登録済み|ジョブは Automation ワーカー上のリソースが使用できるようになるのを待機しています。そうなれば、ジョブを起動できます。|
|Starting|ジョブがワーカーに割り当てられており、システムがジョブを起動しているところです。|
|再開中|システムは、ジョブが停止された後、そのジョブを再開しているところです。|
|実行中|ジョブは実行中です。|
|実行中、リソースを待機中|ジョブは [fair share](http://msdn.microsoft.com/library/azure/3179b655-ab39-407a-9169-33571f958325#fairshare) 制限に達したためにアンロードされました。ジョブは最後のチェックポイントからすぐに再開します。|
|停止済み|ジョブは完了した後、ユーザーによって停止されました。|
|停止中|システムがジョブを停止させているところです。|
|Suspended|ユーザーか、システムか、または Runbook 内のコマンドによってジョブは中断されました。中断されているジョブは、再度起動することができ、最後のチェックポイントから、チェックポイントがない場合は Runbook の先頭から再開することになります。Runbook は、例外が発生した場合にシステムによってのみ中断されます。既定では、ErrorActionPreference は **Continue** に設定されます。これはエラー発生時にもジョブが実行を維持することを意味します。このユーザー設定変数を **Stop** に設定すると、エラー発生時にジョブは中断します。|
|中断中|ユーザーの要求を受けてシステムはジョブを中断しようとしています。Runbook は、次のチェックポイントに到達してからでないと、中断できません。それが最後のチェックポイントを既に過ぎている場合は、中断可能になる前に完了することになります。|

## Azure 管理ポータルを使用したジョブの状態の表示

### Automation Dashboard

Automation Dashboard は、特定の Automation アカウントについてすべての Runbook の概要を示します。これには、アカウントの [使用状況の概要] も含まれています。概要のグラフには、すべての Runbook について、各状態になり指定の日数または時間が経過したジョブの総数が表示されます。グラフの右上隅で時間の範囲を選択することができます。グラフの時間軸は選択した時間の範囲の種類に応じて変わります。特定の状態の行を表示するかどうかを選択するには、画面の上部で該当する状態をクリックします。

Automation Dashboard を表示するには、次の手順を使用します。

1. Azure 管理ポータルで、**[Automation]** を選択し、次に Automation アカウントの名前をクリックします。
1. **[ダッシュボード]** タブを選択します。

### Runbook Dashboard

Runbook Dashboard には、単一の Runbook の概要が表示されます。概要のグラフには、Runbook について、それぞれの状態になり指定の日数または時間が経過したジョブの総数が表示されます。グラフの右上隅で時間の範囲を選択することができます。グラフの時間軸は選択した時間の範囲の種類に応じて変わります。特定の状態の行を表示するかどうかを選択するには、画面の上部で該当する状態をクリックします。

Runbook Dashboard を表示するには、次の手順を使用します。

1. Azure 管理ポータルで、**[Automation]** を選択し、次に Automation アカウントの名前をクリックします。
1. Runbook の名前をクリックします。
1. **[ダッシュボード]** タブを選択します。

### ジョブの概要

特定の Runbook 用に作成されたすべてのジョブと、それらのジョブの最新の状態を一覧にして表示できます。この一覧は、ジョブの状態とジョブに最後に変更を加えた日付の範囲とによってフィルター処理することができます。ジョブの名前をクリックして、その詳細な情報と出力を表示します。ジョブの詳細表示には、そのジョブに指定された Runbook パラメーターの値が含まれます。

次の手順を使用して Runbook のジョブを表示します。

1. Azure 管理ポータルで、**[Automation]** を選択し、次に Automation アカウントの名前をクリックします。
1. Runbook の名前をクリックします。
1. **[ジョブ]** タブを選択します。
1. ジョブの **[作成されたジョブ]** 列をクリックして、その詳細と出力を表示します。

## Windows PowerShell を使用したジョブの状態の取得

[Get-AzureAutomationJob](http://msdn.microsoft.com/library/azure/dn690263.aspx) を使用して、Runbook 用に作成されたジョブと、特定のジョブの詳細を取得することができます。[Start-AzureAutomationRunbook](http://msdn.microsoft.com/library/azure/dn690259.aspx) を使用して、Windows PowerShell で Runbook を開始すると、結果として作成されたジョブが返されます。[Get-AzureAutomationJobOutput](http://msdn.microsoft.com/library/azure/dn690263.aspx) を使用してジョブの出力を取得します。

次のサンプル コマンドは、サンプル Runbook の最後のジョブを取得し、その状態、Runbook パラメーターに指定された値、およびジョブの出力を表示します。

	$job = (Get-AzureAutomationJob –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" | sort LastModifiedDate –desc)[0]
	$job.Status
	$job.JobParameters
	Get-AzureAutomationJobOutput –AutomationAccountName "MyAutomationAccount" -Id $job.Id –Stream Output

## 関連記事:

- [Azure Automation での Runbook を開始する](automation-starting-a-runbook.md) 

<!---HONumber=July15_HO4-->