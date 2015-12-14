<properties 
   pageTitle="Azure Automation での Runbook の出力および メッセージ | Microsoft Azure"
   description="Azure Automation で Runbook から出力とエラー メッセージを作成および取得する方法を説明します。"
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
   ms.date="11/24/2015"
   ms.author="bwren" />

# Azure Automation での Runbook の出力および メッセージ

Azure Automation のほとんどの Runbook では、ユーザーに対するエラー メッセージや別のワークフローで使用するための複合オブジェクトなど、いくつかの出力形式が含まれます。Windows PowerShell では、スクリプトまたはワークフローから出力を送信するための[複数のストリーム](http://blogs.technet.com/heyscriptingguy/archive/2014/03/30/understanding-streams-redirection-and-write-host-in-powershell.aspx)が提供されます。Azure Automation はこれらのストリームごとに異なる方法で対応するため、Runbook を作成する場合は、それぞれの使用法のベスト プラクティスに従ってください。

以下の表に、公開された Runbook の実行時と [Runbook のテスト](http://msdn.microsoft.com/library/azure/dn879147.aspx)時の両方の場合の、Azure 管理ポータルでの各ストリームとその動作の簡単な説明を示します。各ストリームの詳細については、後続のセクションで説明します。

| ストリーム | 説明 | 公開先 | テスト|
|:---|:---|:---|:---|
|出力|他の Runbook によって使用されることを目的とするオブジェクト。|ジョブ履歴に書き込まれます。|[テスト出力] ウィンドウに表示されます。|
|警告|ユーザー向けの警告メッセージ。|ジョブ履歴に書き込まれます。|[テスト出力] ウィンドウに表示されます。|
|エラー|ユーザー向けのエラー メッセージ例外とは異なり、既定では Runbook はエラー メッセージ発行後に実行を継続します。|ジョブ履歴に書き込まれます。|[テスト出力] ウィンドウに表示されます。|
|詳細|全般的なまたはデバッグ情報を提供するメッセージ。|Runbook の詳細ログが有効な場合のみ、ジョブ履歴に書き込まれます。|Runbook で $VerbosePreference が Continue に設定されている場合のみ、[テスト出力] ウィンドウに表示されます。|
|進捗状況|Runbook の各アクティビティの前後に自動的に生成されるレコード。Runbook では、独自の進行状況レコードを作成しないでください。これらのレコードは対話型ユーザー向けです。|Runbook の進行状況ログが有効な場合のみ、ジョブ履歴に書き込まれます。|[テスト出力] ウィンドウには表示されません。|
|デバッグ|対話型ユーザー向けのメッセージ。Runbook では使用しないでください。|ジョブ履歴には書き込まれません。|[テスト出力] ペインには書き込まれません。|

## 出力ストリーム

出力ストリームはスクリプトまたはワークフローが正しく実行された場合に作成されるオブジェクト出力のためのものです。Azure Automation では、このストリームは、[現在の Runbook を呼び出す親 Runbook](automation-child-runbooks.md) が使用するオブジェクトに主に使用されます。親 Runbook から [Runbook をインラインで呼び出す](automation-child-runbooks.md/#InlineExecution)と、出力ストリームのデータが親に返されます。ユーザーに一般情報を返すときは、その Runbook が別の Runbook から呼び出されないことがわかっている場合にのみ、出力ストリームを使用してください。ただし通常は、ベストプラクティスとして、[詳細ストリーム](#Verbose)を使用してユーザーに一般情報を伝えてください。

出力ストリームへのデータの書き込みは、[Write-Output](http://technet.microsoft.com/library/hh849921.aspx) を使用するか、Runbook の行にオブジェクトを配置することで実行できます。

	#The following lines both write an object to the output stream.
	Write-Output –InputObject $object
	$object

### 関数からの出力

Runbook に含まれている関数から出力ストリームに書き込むと、出力は Runbook に戻されます。Runbook がその出力を変数に代入する場合、出力は出力ストリームに書き込まれません。関数内から他のストリームへの書き込みは、Runbook の対応するストリームに書き込まれます。

次のサンプル Runbook で考えてみましょう。

	Workflow Test-Runbook
	{
	   Write-Verbose "Verbose outside of function"
	   Write-Output "Output outside of function"
	   $functionOutput = Test-Function
	
	   Function Test-Function
	   {
	      Write-Verbose "Verbose inside of function"
	      Write-Output "Output inside of function"
	   }
	}

Runbook ジョブの出力ストリームは次のようになります。

	Output outside of function

Runbook ジョブの詳細ストリームは次のようになります。

	Verbose outside of function
	Verbose inside of function

### 出力のデータ型の宣言

ワークフローでは、[OutputType 属性](http://technet.microsoft.com/library/hh847785.aspx)を使用して、その出力のデータ型を指定できます。この属性は実行時に影響はありませんが、設計時にRunbook 作成者に対して Runbook の予想される出力を通知します。Runbook 用のツールセットは進化し続けるため、設計時に出力のデータ型を宣言することの重要性は高まり続けます。その結果、ベスト プラクティスは、作成するすべての Runbook にこの宣言を含めることです。

次のサンプル Runbook は、文字列オブジェクトを出力し、その出力の型宣言が含まれています。Runbook が特定の型の配列を出力する場合でも、型の配列ではなく、型を指定してください。

	Workflow Test-Runbook
	{
	   [OutputType([string])]
	
	   $output = "This is some string output."
	   Write-Output $output
	}

## メッセージ ストリーム

出力ストリームとは異なり、メッセージ ストリームはユーザーに情報を伝えるためのものです。さまざまな種類の情報向けに複数のメッセージ ストリームがあり、それぞれが異なる方法で Azure Automation で処理されます。

### 警告およびエラー ストリーム

警告およびエラー ストリームは、Runbook で発生する問題をログ記録するためのものです。それらは、Runbook が実行されたときにジョブ履歴に書き込まれ、Runbook がテストされたときに Microsoft Azure 管理ポータルの [テスト出力] ウィンドウに表示されます。既定では、Runbook は警告またはエラーの後も引き続き実行されます。警告またはエラー時に Runbook を中断するように指定することができます。これを行うには、メッセージを作成する前に、Runbook の[ユーザー設定変数](#PreferenceVariables)を設定します。たとえば、Runbook を例外の場合と同様、エラーで中断するようにするには、**$ErrorActionPreference** を Stop に設定します。

警告またはエラー メッセージを作成するには、[Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) または [Write-Error](http://technet.microsoft.com/library/hh849962.aspx) コマンドレットを使用します。アクティビティによってストリームに書き込むことができる場合もあります。

	#The following lines create a warning message and then an error message that will suspend the runbook.
	
	$ErrorActionPreference = "Stop"
	Write-Warning –Message "This is a warning message."
	Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."

### 詳細ストリーム

詳細メッセージ ストリームは、Runbook の操作に関する一般情報用です。Runbook では[デバッグ ストリーム](#Debug)を使用できないため、デバッグ情報には詳細メッセージを使用します。既定では、公開された Runbook の詳細メッセージはジョブ履歴に格納されません。詳細メッセージを格納するには、Microsoft Azure 管理ポータルの Runbook の [構成] タブで、公開された Runbook を詳細レコードを記録するように構成します。ほとんどの場合、パフォーマンス上の理由から、詳細レコードを記録しないというRunbook の既定の設定を保持する必要があります。このオプションを有効にするのは、Runbook のトラブルシューティングやデバッグをする場合のみです。

[Runbook のテスト時](http://msdn.microsoft.com/library/azure/dn879147.aspx)には、Runbook が詳細レコードを記録するように構成されている場合でも、詳細メッセージは表示されません。[Runbook のテスト時](http://msdn.microsoft.com/library/azure/dn879147.aspx)に詳細メッセージを表示するには、$VerbosePreference 変数を Continue に設定する必要があります。この変数が設定されると、Azure 管理ポータルの [テスト出力] ペインに詳細メッセージが表示されます。

詳細メッセージを作成するには、[Write-Verbose](http://technet.microsoft.com/library/hh849951.aspx) コマンドレットを使用します。

	#The following line creates a verbose message.
	
	Write-Verbose –Message "This is a verbose message."

### デバッグ ストリーム

デバッグ ストリームは対話ユーザー向けに使用するものであるため、Runbook では使用しません。

## 進捗状況レコード

Runbook を (Azure 管理ポータルの Runbook の [構成] タブで) 進捗状況レコードが記録されるように構成すると、各アクティビティの実行前後にレコードがジョブ履歴に書き込まれます。ほとんどの場合、パフォーマンスを最大化するために、進行状況レコードを記録しないという Runbook の既定の設定を保持する必要があります。このオプションは、Runbook のトラブルシューティングやデバッグを行うためにのみ有効にします。Runbook のテスト時には、Runbook が進歩状況レコードを記録するように構成されている場合でも、進歩状況メッセージは表示されません。

[Write-Progress](http://technet.microsoft.com/library/hh849902.aspx) コマンドレットは、対話ユーザー向けに使用するものであるため、Runbook では無効です。

## ユーザー設定変数

Windows PowerShell では[ユーザー設定変数](http://technet.microsoft.com/library/hh847796.aspx)を使用して、異なる出力ストリームに送信されるデータへの応答方法を決定します。これらの変数を Runbook で設定することで、異なるストリームに送信されるデータへの応答方法を制御できます。

次の表は、Runbook で使用できるユーザー設定変数と、それらの有効値と既定値を示しています。この表には、Runbook で有効な値のみが含まれることに注意してください。Windows PowerShell を Azure Automation の外部で使用する場合は、ユーザー設定変数で有効なその他の値があります。

| 変数| 既定値| 有効な値|
|:---|:---|:---|
|WarningPreference|Continue|Stop<br>Continue<br>SilentlyContinue|
|ErrorActionPreference|Continue|Stop<br>Continue<br>SilentlyContinue|
|VerbosePreference|SilentlyContinue|Stop<br>Continue<br>SilentlyContinue|

次の表では、Runbook で有効なユーザー設定変数値の動作を一覧表示します。

| 値| 動作|
|:---|:---|
|Continue|メッセージを記録し、Runbook の実行を続けます。|
|SilentlyContinue|メッセージを記録せずに Runbook の実行を続けます。これはメッセージを無視する効果があります。|
|Stop|メッセージを記録し、Runbook を中断します。|

## Runbook の出力とメッセージの取得

### Azure 管理ポータル

Runbook ジョブの詳細は、Azure 管理ポータルの Runbook の [ジョブ] タブで参照できます。ジョブの [概要] には、ジョブと例外 (発生した場合) の一般情報だけでなく、入力パラメーターと[出力ストリーム](#Output)も表示されます。詳細レコードおよび進捗状況レコードを記録するように Runbook が構成されている場合は、[履歴] に、[詳細ストリーム](#Verbose)と[進捗状況レコード](#Progress)だけでなく、[出力ストリーム](#Output)と[警告およびエラー ストリーム](#WarningError)からのメッセージも含まれます。

### Windows PowerShell

Windows Powershell では、[Get-AzureAutomationJobOutput](http://msdn.microsoft.com/library/dn690268.aspx) コマンドレットを使用して、Runbook から出力とメッセージを取得できます。このコマンドレットにはジョブの ID が必要であり、返すストリームを指定する Stream と呼ばれるパラメーターがあります。[Any] を指定して、ジョブのすべてのストリームを返すことができます。

次の例は、サンプル Runbook を開始し、完了するまで待機します。完了すると、その出力ストリームがジョブから収集されます。

	$job = Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" 
	
	$doLoop = $true
	While ($doLoop) {
	   $job = Get-AzureAutomationJob –AutomationAccountName "MyAutomationAccount" -Id $job.Id
	   $status = $job.Status
	   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped") 
	}
	
	Get-AzureAutomationJobOutput –AutomationAccountName "MyAutomationAccount" -Id $job.Id –Stream Output

## 関連記事:

- [Runbook ジョブの追跡](automation-runbook-execution.md)
- [子 Runbook](http://msdn.microsoft.com/library/azure/dn857355.aspx)

<!---HONumber=AcomDC_1203_2015-->