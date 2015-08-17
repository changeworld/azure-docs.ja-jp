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
   ms.date="07/06/2015"
   ms.author="bwren" />

# Windows PowerShell ワークフローについて

Azure Automation の Runbook は Windows PowerShell ワークフローとして実装されています。Windows PowerShell ワークフローは Windows PowerShell スクリプトと似ていますが、初めて使用するときに混乱しやすい重要な相違点がいくつかあります。この記事は、PowerShell に既に慣れているユーザー向けであり、PowerShell スクリプトを Runbook で使用するために PowerShell ワークフローに変換する場合に知っておく必要のある概念について簡単に説明します。

ワークフローとは、時間のかかるタスクを実行する手順や、複数のデバイスまたは管理されたノード間で複数の手順の調整を必要とする手順をプログラミングで連結した手順のシーケンスです。通常のスクリプトに比べて、ワークフローの利点には、複数のデバイスに対して同時に操作を実行でき、エラーから自動的に回復できることが含まれます。Windows PowerShell ワークフローは、Windows Workflow Foundation を活用する Windows PowerShell スクリプトです。ワークフローは Windows PowerShell の構文で記述され、Windows PowerShell によって起動されますが、Windows Workflow Foundation によって処理されます。

この記事のトピックに関する詳細については、「[Windows PowerShell ワークフローについて](http://technet.microsoft.com/library/jj134242.aspx)」をご覧ください。

## Runbook の種類

Azure Automation の Runbook には、*テキスト*と*グラフィカル*の 2 種類があります。Runbook を作成するときに Runbook の種類を定義し、作成した後で種類を変えることはできません。

テキスト形式の Runbook は、Azure Automation のテキスト エディターや PowerShell ISE などのオフライン エディターを使用して PowerShell ワークフローのコードを直接操作することを好むユーザーのためのものです。テキスト形式の Runbook を作成する場合は、この記事の情報を理解する必要があります。

グラフィカルな Runbook では、同じアクティビティとコマンドレットを使用して Runbook を作成できますが、基になる PowerShell ワークフローの複雑さを隠蔽するグラフィカル インターフェイスを使用ます。チェックポイントや並列実行などのこの記事で紹介する概念は、グラフィカルな Runbook にも当てはまりますが、詳細な構文を気にする必要はありません。

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

アクティビティとは、ワークフロー内の特定のタスクです。スクリプトが 1 つまたは複数のコマンドで構成されるのと同様、ワークフローはシーケンスで実行される 1 つまたは複数のアクティビティで構成されます。Windows PowerShell ワークフローは、ワークフローの実行時に多くの Windows PowerShell コマンドレットをアクティビティに自動的に変換します。Runbook でこれらのコマンドレットのいずれかを指定すると、対応するアクティビティは、実際には Windows Workflow Foundation で実行されます。対応するアクティビティがないコマンドレットの場合、Windows PowerShell ワークフローは [InlineScript](#inlinescript) アクティビティ内のコマンドレットを自動的に実行します。InlineScript ブロックに明示的に含めないと除外され、ワークフローでは使用できない一連のコマンドレットがあります。これらの概念の詳細については、「[スクリプト ワークフローでのアクティビティの使用](http://technet.microsoft.com/library/jj574194.aspx)」を参照してください。

ワークフロー アクティビティは、操作を構成するための一連の共通パラメーターを共有します。ワークフローの共通パラメーターの詳細については、「[about\_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx)」を参照してください。

### 位置指定パラメーター

ワークフローのアクティビティおよびコマンドレットでは、位置指定パラメーターを使用できません。つまり、パラメーター名を使用する必要があります。

たとえば、実行中のすべてのサービスを取得する次のようなコードについて考えます。

	 Get-Service | Where-Object {$_.Status -eq "Running"}

これと同じコードをワークフローで実行しようとすると、「指定された名前のパラメータを使用してパラメータ セットを解決できません」のようなメッセージが表示されます。 これを修正するには、次のようにパラメーター名を指定します。

	Workflow Get-RunningServices
	{
		Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
	}

### 逆シリアル化されたオブジェクト

ワークフロー内のオブジェクトは逆シリアル化されます。このため、プロパティは同じように使用できますが、メソッドは使用できません。たとえば、Service オブジェクトの Stop メソッドを使用してサービスを停止する次のような PowerShell コードがあるものとします。

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

InlineScript は、次に示す構文を使用します。

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

出力を変数に割り当てることによって、InlineScript から出力を返すことができます。次の例では、サービスを停止した後、サービス名を出力しています。

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

**Parallel** キーワードを使用して、同時に実行される複数のコマンドを含むスクリプト ブロックを作成できます。これには、次に示す構文を使用します。この場合は、Activity1 と Activity2 が同時に開始されます。Activity3 は、Activity1 と Activity2 の両方が完了した後にのみ開始されます。

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>


たとえば、ネットワークの宛先に複数のファイルをコピーする次のような PowerShell コマンドについて考えます。1 つのファイルのコピーが終了してから次のコピーが開始するように、これらのコマンドは順番に実行されます。

	$Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
	$Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
	$Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt

次のワークフローでは、同じコマンドが並列に実行されて、すべてのコピーが同時に開始します。すべてが完全にコピーされた後でのみ、完了メッセージが表示されます。

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


**ForEach -Parallel** の構文を使用することにより、コレクション内の各項目のコマンドを同時に処理できます。コレクション内の項目は並行して処理され、スクリプト ブロック内のコマンドは順番に実行されます。これには、次に示す構文を使用します。この場合、Activity1 はコレクション内のすべての項目と同時に開始されます。各項目について、Activity2 は Activity1 が完了した後に開始されます。Activity3 は、すべての項目における Activity1 と Activity2 の両方が完了した後にのみ開始されます。

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

次の例は、並列にファイルをコピーする前の例と似ています。この場合は、個々のファイルがコピーされるたびにメッセージが表示されます。すべてが完全にコピーされた後でのみ、最終完了メッセージが表示されます。

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

> [AZURE.NOTE]子 Runbook を並列に実行すると信頼できない結果になることがわかっているので、お勧めできません。子 Runbook からの出力が表示されないことがあり、1 つの子 Runbook での設定が並列に実行されている他の子 Runbook に影響を与える可能性があります。


## チェックポイント

*チェックポイント* は、変数の現在の値と、そのポイントに生成された出力を含むワークフローの現在の状態のスナップショットです。ワークフローがエラーで終了した場合、または[中断](suspending-a-workflow)した場合、次の実行時には、ワークフローの先頭からではなく、最後のチェックポイントから開始されます。**Checkpoint-Workflow** アクティビティを使用してワークフローにチェックポイントを設定できます。

次のサンプル コードでは、Activity2 の後に例外が発生し、ワークフローが終了します。これは、設定されている最後のチェックポイントのすぐ後だったため、ワークフローを再び実行されると、Activity2 の実行から開始します。

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

例外を引き起こす可能性のあるアクティビティの後にチェックポイントをワークフローに設定する必要があり、ワークフローが再開された場合は繰り返すべきではありません。たとえば、ワークフローによって仮想マシンが作成される場合があります。チェックポイントをコマンドの前後に設定して、仮想マシンを作成できます。作成が失敗した場合、ワークフローを再度開始するとコマンドが繰り返されます。作成が成功した後でワークフローが失敗した場合は、ワークフローを再開しても、仮想マシンが再び作成されることはありません。

次の例では、ネットワークの場所に複数のファイルをコピーし、各ファイルの後にチェックポイントを設定します。ネットワークの場所が失われた場合、ワークフローはエラーで終了します。ワークフローを再び開始すると、最後のチェックポイントで再開するので、既にコピーされているファイルだけがスキップされます。

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

<!---HONumber=August15_HO6-->