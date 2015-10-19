<properties 
   pageTitle="PowerShell ワークフローについての説明"
   description="Azure Automation の Runbook は、Windows PowerShell ワークフローが基になっています。この記事では、PowerShell に慣れている作成者を対象に、PowerShell と PowerShell ワークフローの具体的な違いについて簡単に説明します。"
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
   ms.date="10/01/2015"
   ms.author="bwren" />

# Windows PowerShell ワークフローについて

Azure Automation の Runbook は Windows PowerShell ワークフローとして実装されています。A Windows PowerShell Workflow is similar to a Windows PowerShell script but has some significant differences that can be confusing to a new user.This article is intended for users already familiar with PowerShell and briefly explains concepts that you require if you are converting a PowerShell script to a PowerShell Workflow for use in a runbook.

A workflow is a sequence of programmed, connected steps that perform long-running tasks or require the coordination of multiple steps across multiple devices or managed nodes.The benefits of a workflow over a normal script include the ability to simultaneously perform an action against multiple devices and the ability to automatically recover from failures.A Windows PowerShell Workflow is a Windows PowerShell script that leverages Windows Workflow Foundation.ワークフローは Windows PowerShell の構文で記述され、Windows PowerShell によって起動されますが、Windows Workflow Foundation によって処理されます。

この記事のトピックに関する詳細については、「[Windows PowerShell ワークフローについて](http://technet.microsoft.com/library/jj134242.aspx)」をご覧ください。

## Runbook の種類

Azure Automation の Runbook には、*テキスト*と*グラフィカル*の 2 種類があります。Runbook を作成するときに Runbook の種類を定義し、作成した後で種類を変えることはできません。

Textual runbooks are for users who prefer to work directly with the PowerShell workflow code either using the textual editor in Azure Automation or an offline editor such as PowerShell ISE.You should understand the information in this article if you are creating a textual runbook.

Graphical runbooks allow you to create a runbook using the same activities and cmdlets but using a graphical interface that hides the complexities of the underlying PowerShell workflow.チェックポイントや並列実行などのこの記事で紹介する概念は、グラフィカルな Runbook にも当てはまりますが、詳細な構文を気にする必要はありません。

## ワークフローの基本構造

PowerShell スクリプトを PowerShell ワークフローに変換する最初のステップは、スクリプトを **Workflow** キーワードで囲むことです。ワークフローは、**Workflow** キーワードで始まり、中かっこで囲まれたスクリプトの本文が後に続きます。次の構文に示すように、ワークフローの名前は **Workflow** キーワードの後に続きます。

    Workflow Test-Workflow
    {
       <Commands>
    }

ワークフローの名前は、Automation の Runbook の名前と一致する必要があります。Runbook がインポートされている場合、ファイル名はワークフロー名と一致していなければならず、末尾は .ps1 でなければなりません。

ワークフローにパラメーターを追加するには、スクリプトの場合と同様に **Param** キーワードを使用します。

## コードの変更

PowerShell ワークフローのコードは PowerShell スクリプト コードとほぼ同じですが、いくつかの重要な変更点があります。次のセクションでは、ワークフローで実行するための PowerShell スクリプトに対して行う必要がある変更について説明します。

### アクティビティ

アクティビティとは、ワークフロー内の特定のタスクです。Just as a script is composed of one or more commands, a workflow is composed of one or more activities that are carried out in a sequence.Windows PowerShell Workflow automatically converts many of the Windows PowerShell cmdlets to activities when it runs a workflow.Runbook でこれらのコマンドレットのいずれかを指定すると、対応するアクティビティは、実際には Windows Workflow Foundation で実行されます。対応するアクティビティがないコマンドレットの場合、Windows PowerShell ワークフローは [InlineScript](#inlinescript) アクティビティ内のコマンドレットを自動的に実行します。InlineScript ブロックに明示的に含めないと除外され、ワークフローでは使用できない一連のコマンドレットがあります。これらの概念の詳細については、「[スクリプト ワークフローでのアクティビティの使用](http://technet.microsoft.com/library/jj574194.aspx)」を参照してください。

ワークフロー アクティビティは、操作を構成するための一連の共通パラメーターを共有します。ワークフローの共通パラメーターの詳細については、「[about\_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx)」を参照してください。

### 位置指定パラメーター

ワークフローのアクティビティおよびコマンドレットでは、位置指定パラメーターを使用できません。All this means is that you must use parameter names.

For example, consider the following code that gets all running services.

	 Get-Service | Where-Object {$_.Status -eq "Running"}

If you try to run this same code in a workflow, you'll get a message like "Parameter set cannot be resolved using the specified named parameters." これを修正するには、次のようにパラメーター名を指定します。

	Workflow Get-RunningServices
	{
		Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
	}

### 逆シリアル化されたオブジェクト

ワークフロー内のオブジェクトは逆シリアル化されます。This means that their properties are still available, but not their methods.For example, consider the following PowerShell code that stops a service using the Stop method of the Service object.

	$Service = Get-Service -Name MyService
	$Service.Stop()

ワークフローでこれを実行しようとすると、「Windows PowerShell ワークフローでは、メソッド呼び出しはサポートされていません」というエラー メッセージが表示されます。

これに対処する 1 つの方法は、これら 2 つのコード行を [InlineScript](#InlineScript) ブロックにラップすることであり、この場合 $Service はブロック内のサービス オブジェクトです。

	Workflow Stop-Service
	{
		InlineScript {
			$Service = Get-Service -Name MyService
			$Service.Stop()
		}
	} 

もう 1 つの方法は、メソッドとして同じ機能を実行する別のコマンドレットがある場合は、それを使用することです。このサンプルの場合、Stop-Service コマンドレットは Stop メソッドと同じ機能を提供し、ワークフローでは次のように使用できます。

	Workflow Stop-MyService
	{
		$Service = Get-Service -Name MyService
		Stop-Service -Name $Service.Name
	}


## InlineScript

**InlineScript** アクティビティは、PowerShell ワークフローではなく従来の PowerShell スクリプトとして 1 つまたは複数のコマンドを実行する必要がある場合に便利です。ワークフロー内のコマンドは Windows Workflow Foundation に送信されて処理され、InlineScript ブロック内のコマンドは Windows PowerShell によって処理されます。

InlineScript uses the syntax shown below.

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

You can return output from an InlineScript by assigning the output to a variable.次の例では、サービスを停止した後、サービス名を出力しています。

	Workflow Stop-MyService
	{
		$Output = InlineScript {
			$Service = Get-Service -Name MyService
			$Service.Stop()
			$Service
		}

		$Output.Name
	}


InlineScript ブロックに値を渡すことはできますが、**$Using** スコープ修飾子を使用する必要があります。次の例は前の例と同じですが、サービス名を変数によって渡しています。

	Workflow Stop-MyService
	{
		$ServiceName = "MyService"
	
		$Output = InlineScript {
			$Service = Get-Service -Name $Using:MyService
			$Service.Stop()
			$Service
		}

		$Output.Name
	}


InlineScript アクティビティは特定のワークフローにとって重要ですが、ワークフロー コンストラクトをサポートせず、次の理由により、必要な場合にのみ使用する必要があります。

- InlineScript ブロックの内部で[チェックポイント](#Checkpoints)を使用することはできません。ブロック内でエラーが発生した場合は、ブロックの先頭から再開する必要があります。
- InlineScript ブロックの内部で[並列実行](#parallel-execution)を使用することはできません。
- InlineScript は、InlineScript ブロックの長さ全体について Windows PowerShell セッションを保持するため、ワークフローの拡張性に影響します。

InlineScript の使用の詳細については、「[ワークフローでの Windows PowerShell コマンドの実行](http://technet.microsoft.com/library/jj574197.aspx)」および「[about\_InlineScript](http://technet.microsoft.com/library/jj649082.aspx)」を参照してください。


## 並列処理

Windows PowerShell ワークフローの利点の 1 つは、一般的なスクリプトのように順番に実行するのでなく、一連のコマンドを並行して実行できることです。

**Parallel** キーワードを使用して、同時に実行される複数のコマンドを含むスクリプト ブロックを作成できます。これには、次に示す構文を使用します。In this case, Activity1 and Activity2 will start at the same time.Activity3 will start only after both Activity1 and Activity2 have completed.

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>


For example, consider the following PowerShell commands that copy multiple files to a network destination.These commands are run sequentially so that one file must finish copying before the next is started.

	$Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
	$Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
	$Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt

The following workflow runs these same commands in parallel so that they all start copying at the same time.すべてが完全にコピーされた後でのみ、完了メッセージが表示されます。

	Workflow Copy-Files
	{
		Parallel 
		{
			$Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
			$Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
			$Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
		}

		Write-Output "Files copied."
	}


**ForEach -Parallel** の構文を使用することにより、コレクション内の各項目のコマンドを同時に処理できます。コレクション内の項目は並行して処理され、スクリプト ブロック内のコマンドは順番に実行されます。This uses the syntax shown below.In this case, Activity1 will start at the same time for all items in the collection.For each item, Activity2 will start after Activity1 is complete.Activity3 will start only after both Activity1 and Activity2 have completed for all items.

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

The following example is similar to the previous example copying files in parallel.In this case, a message is displayed for each file after it copies.Only after they are all completely copied is the final completion message displayed.

	Workflow Copy-Files
	{
		$files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

		ForEach -Parallel ($File in $Files) 
		{
			$Copy-Item -Path $File -Destination \\NetworkPath
			Write-Output "$File copied."
		}
		
		Write-Output "All files copied."
	}

> [AZURE.NOTE]We do not recommend running child runbooks in parallel since this has been shown to give unreliable results.子 Runbook からの出力が表示されないことがあり、1 つの子 Runbook での設定が並列に実行されている他の子 Runbook に影響を与える可能性があります。


## チェックポイント

*チェックポイント* は、変数の現在の値と、そのポイントに生成された出力を含むワークフローの現在の状態のスナップショットです。ワークフローがエラーで終了した場合、または[中断](suspending-a-workflow)した場合、次の実行時には、ワークフローの先頭からではなく、最後のチェックポイントから開始されます。**Checkpoint-Workflow** アクティビティを使用してワークフローにチェックポイントを設定できます。

次のサンプル コードでは、Activity2 の後に例外が発生し、ワークフローが終了します。When the workflow is run again, it starts by running Activity2 since this was just after the last checkpoint set.

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

You should set checkpoints in a workflow after activities that may be prone to exception and should not be repeated if the workflow is resumed.For example, your workflow may create a virtual machine.You could set a checkpoint both before and after the commands to create the virtual machine.If the creation fails, then the commands would be repeated if the workflow is started again.If the the worfklow fails after the creation succeeds, then the virtual machine will not be created again when the workflow is resumed.

The following example copies multiple files to a network location and sets a checkpoint after each file.If the network location is lost, then the workflow will end in error.ワークフローを再び開始すると、最後のチェックポイントで再開するので、既にコピーされているファイルだけがスキップされます。

	Workflow Copy-Files
	{
		$files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

		ForEach ($File in $Files) 
		{
			$Copy-Item -Path $File -Destination \\NetworkPath
			Write-Output "$File copied."
			Checkpoint-Workflow
		}
		
		Write-Output "All files copied."
	}



チェックポイントの詳細については、「[スクリプト ワークフローへのチェックポイントの追加](http://technet.microsoft.com/library/jj574114.aspx)」を参照してください。



## 関連記事:

- [Windows PowerShell ワークフローの概要](http://technet.microsoft.com/library/jj134242.aspx) 

<!---HONumber=Oct15_HO2-->