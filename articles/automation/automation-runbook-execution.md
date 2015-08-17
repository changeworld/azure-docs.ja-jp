<properties
   pageTitle="Azure Automation での Runbook の実行"
   description="Azure Automation で Runbook が処理される方法の詳細について説明します。"
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
   ms.date="07/22/2015"
   ms.author="bwren" />

# Azure Automation での Runbook の実行


Azure Automation で runbook を開始するときに、ジョブが作成されます。ジョブは、Runbook の単一の実行インスタンスです。各ジョブを実行する Azure Automation ワーカーが割り当てられます。ワーカーは複数の Azure アカウントで共有されるが、さまざまな Automation アカウントからのジョブは互いに分離されます。ジョブに対する要求をどのワーカーで処理するかを制御することはできません。1 つの Runbook で、複数のジョブを同時に実行することができます。Azure ポータルで Runbook の一覧を表示すると、各 Runbook に対して最後に起動されたジョブの状態が一覧表示されます。それぞれの状態を追跡するために、Runbook ごとにジョブの一覧を表示できます。ジョブのさまざまな状態の説明については、「[ジョブの状態](#job-statuses)」を参照してください。

![ジョブの状態](./media/automation-runbook-execution/job-statuses.png)


ジョブは、Azure サブスクリプションに接続することにより Azure リソースにアクセスします。データ センター内のリソースにパブリック クラウドからアクセスできる場合、ジョブはそれらのリソースにのみアクセスします。

## ジョブの状態

次の表には、ジョブが取り得るさまざまな状態を説明します。

| 状態| 説明|
|:---|:---|
|完了|ジョブは正常に完了しました。|
|失敗|ジョブはエラーで終了しました。|
|失敗、リソースを待機中|ジョブは [fair share](#fairshare) の限界に 3 回到達し、毎回、同じチェックポイントから、または Runbook の先頭から起動したために、失敗しました。|
|キューに登録済み|ジョブは Automation ワーカー上のリソースが使用できるようになるのを待機しています。そうなれば、ジョブを起動できます。|
|Starting|ジョブがワーカーに割り当てられており、システムがジョブを起動しているところです。|
|再開中|システムは、ジョブが停止された後、そのジョブを再開しているところです。|
|実行中|ジョブは実行中です。|
|実行中、リソースを待機中|ジョブは [fair share](#fairshare) 制限に達したためにアンロードされました。ジョブは最後のチェックポイントからすぐに再開します。|
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

## フェア シェア

クラウド内のすべての Runbook 間でリソースを共有するために、Azure Automation は任意のジョブが 3 時間実行された後で一時的にそのジョブをアンロードし、次にそのジョブをその最後の[チェックポイント](http://aka.ms/runbookauthor/checkpoints)から再起動します。この間、ジョブの状態は [実行中、リソースを待機中] となります。Runbook がチェックポイントを持っていないか、またはアンロードされる前にジョブがまだ最初のチェックポイントに達していない場合、ジョブは最初から再開されます。

Runbook が同じチェックポイントから、または Runbook の先頭から 3 回連続して再起動した場合、Runbook は終了し、[失敗、リソースを待機中] という状態になります。これは、Runbook が次のチェックポイントに進むことができず再度アンロードされない場合に、Runbook が完了せずに無期限に実行されるのを防ぐためのものです。この場合は、エラー発生時に次の例外を受信します。

ジョブは同じチェックポイントから繰り返し削除されましたので実行を継続できません。Runbook が、その状態を維持しないで時間のかかる操作を実行するということになっていないことを確認してください。

Runbook を作成する際には、2 つのチェックポイント間で任意のアクティビティを実行するのにかかる時間が 3 時間を超えないことを確認してください。この 3 時間制限に決して到達しないように、Runbook にチェックポイントを追加することが必要な場合があります。実行に時間のかかる処理操作を分割することが必要な場合もあります。たとえば、Runbook が大規模な SQL データベースで再インデックス化を実行する可能性があります。この単一処理がフェア シェア制限内で完了しない場合、ジョブはアンロードされ、先頭から再開されます。この場合は再インデックス化処理を複数のステップに分割します。たとえば、一度に 1 つのテーブルを再インデックス化し、各処理の後にチェックポイントを挿入します。こうすれば、最後の処理が完了した後、ジョブは再開することが可能です。



## 関連記事:

- [Azure Automation での Runbook を開始する](automation-starting-a-runbook)
- [Azure Automation で Runbook ジョブの状態を表示する方法](automation-viewing-the-status-of-a-runbook-job)
 

<!---HONumber=August15_HO6-->