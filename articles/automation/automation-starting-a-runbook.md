<properties 
   pageTitle="Azure Automation での Runbook を開始する"
   description="Azure Automation の Runbook を開始するために使用できる各種方法についてまとめ、Azure ポータルと Windows PowerShell の両方の詳細な使用方法について説明します。"
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
   ms.date="09/28/2015"
   ms.author="bwren;sngun"/>

# Azure Automation での Runbook を開始する

次の表は、特定のシナリオに最も適している、Azure Automation の Runbook を開始する方法を決定するときに役立ちます。この記事には、Azure ポータルと Windows PowerShell を使用して Runbook を開始する詳細情報が含まれます。他の方法の詳細情報については、以下のリンクからアクセスできる他のドキュメントに記されています。

<table>
 <tr>
  <td>方法</td>
  <td>特性</td>
 </tr>
 <tr>           
  <td><a href="#starting-a-runbook-with-the-azure-portal">Azure ポータル</a></td>
  <td>
   <ul>
    <li>対話型ユーザー インターフェイスを使用する最も簡単な方法です。</li>
    <li>単純なパラメーター値を提供するフォームです。</li>
    <li>ジョブの状態を簡単に追跡できます。</li>
    <li>アクセスを Azure ログオンで認証します。</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="https://msdn.microsoft.com/library/dn690259.aspx">Windows PowerShell</a></td>
  <td>
   <ul>
     <li>Windows PowerShell コマンドレットを使用してコマンドラインから呼び出します。</li>
     <li>複数のステップで自動化されたソリューションに含めることができます。</li>
     <li>証明書または OAuth ユーザー プリンシパル/サービス プリンシパルで要求が認証されます。</li>
     <li>単純および複雑なパラメーター値を提供します。</li>
     <li>ジョブの状態を追跡できます。</li>
     <li>PowerShell コマンドレットをサポートするために必要なクライアントです。</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://msdn.microsoft.com/library/azure/mt163849.aspx">Azure Automation API</a></td>
  <td>
   <ul>
    <li>最も柔軟性の高い方法ですが、最も複雑でもあります。</li>
    <li>HTTP 要求を発行することが可能なあらゆるカスタム コードから呼び出せます。</li>
    <li>証明書または OAuth ユーザー プリンシパル/サービス プリンシパルでの認証を要求します。</li>
    <li>単純および複雑なパラメーター値を提供します。</li>
    <li>ジョブの状態を追跡できます。</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://azure.microsoft.com/documentation/articles/automation-webhooks/">Webhook</a></td>
  <td>
   <ul>
    <li>1 つの HTTP 要求で Runbook を開始します。</li>
    <li>URL のセキュリティ トークンを使用して認証します。</li>
    <li>クライアントは Webhook の作成時に指定されたパラメーター値を上書きできません。Runbook は、HTTP 要求の詳細が含まれる 1 つのパラメーターを定義できます。</li>
    <li>Webhook URL でジョブの状態を追跡することはできません。</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://azure.microsoft.com/documentation/articles/automation-webhooks/">Azure アラートに応答する</a></td>
  <td>
   <ul>
    <li>Azure アラートに応答して Runbook を開始します。</li>
    <li>runbook 用の Webhook とアラートへのリンクを構成します。</li>
    <li>URL のセキュリティ トークンを使用して認証します。</li>
    <li>現時点ではメトリックに関するアラートのみサポートします。</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://azure.microsoft.com/documentation/articles/automation-scheduling-a-runbook">スケジュール</a></td>
  <td>
   <ul>
    <li>時間、日、週の単位のスケジュールで Runbook を自動的に開始できます。</li>
    <li>Azure ポータル、PowerShell コマンドレット、または Azure API を使用してスケジュールを設定します。</li>
    <li>スケジュールで使用するパラメーター値を提供します。</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://msdn.microsoft.com/library/azure/dn857355.aspx">別の Runbook から</a></td>
  <td>
   <ul>
    <li>別の Runbook の活動として、Runbook を使用します。</li>
    <li>複数の Runbook で使用する機能に役立ちます。</li>
    <li>子 Runbook にパラメーター値を指定し、出力を親 Runbook で使用します。</li>
   </ul>
  </td>
 </tr>
</table>
<br>



## Azure ポータルで Runbook を開始する

1. Azure ポータルで、**[Automation]** を選択し、次に Automation アカウントの名前をクリックします。
1. **[Runbook]** タブを選択します。
1. Runbook を選択し、**[開始]** をクリックします。
1. Runbook にパラメーターがある場合は、各パラメーターのテキスト ボックスに値を指定するように求めるプロンプトが表示されます。パラメーターについて詳しくは、「[Runbook のパラメーター](#Runbook-parameters)」をご覧ください。
1. **[開始]** Runbook メッセージの隣にある **[ジョブの表示]** を選択するか、Runbook の **[ジョブ]** タブを選択してその Runbook ジョブの状態を表示します。

## Azure プレビュー ポータルで Runbook を開始する

1. Automation アカウントから、**[Runbook]** 部分をクリックして **[Runbook]** ブレードを開きます。
1. Runbook をクリックすると、その **[Runbook]** ブレードが開きます。
2. **[開始]** をクリックします。
1. Runbook がパラメーターを持っていない場合は、開始するかどうかを確認するプロンプトが表示されます。Runbook にパラメーターがある場合、**[Runbook の開始]** ブレードが開くので、パラメーター値を指定できます。パラメーターについて詳しくは、「[Runbook のパラメーター](#Runbook-parameters)」をご覧ください。
3. **[ジョブ]** ブレードが開くので、ジョブの状態を追跡できます。


## Windows PowerShell で Runbook を開始する

[Start-AzureAutomationRunbook](http://msdn.microsoft.com/library/azure/dn690259.aspx) を使用して、Windows PowerShell で Runbook を開始できます。次のサンプル コードは、Test-Runbook という Runbook を開始します。

	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

Start-AzureAutomationRunbook は、Runbook の開始後にその状態を追跡するために使用できるジョブ オブジェクトを返します。その後、このジョブ オブジェクトを [Get-AzureAutomationJob](http://msdn.microsoft.com/library/azure/dn690263.aspx) と一緒に使用するとジョブの状態を判別でき、[Get-AzureAutomationJobOutput](http://msdn.microsoft.com/library/azure/dn690268.aspx) を使用すると出力を取得できます。次のサンプル コードは、Test-Runbook という Runbook を開始し、完了するまで待機してからその出力を表示します。

	$job = Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"
	
	$doLoop = $true
	While ($doLoop) {
	   $job = Get-AzureAutomationJob –AutomationAccountName "MyAutomationAccount" -Id $job.Id
	   $status = $job.Status
	   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped") 
	}
	
	Get-AzureAutomationJobOutput –AutomationAccountName "MyAutomationAccount" -Id $job.Id –Stream Output

Runbook でパラメーターが必要な場合には、それらを [Hashtable](http://technet.microsoft.com/library/hh847780.aspx) として指定する必要があります。その Hashtable のキーがパラメーター名、値がパラメーター値になります。次の例は、FirstName と LastName という 2 つの文字列パラメーターと、RepeatCount という名前の整数、および Show という名前のブール型パラメーターが含まれる Runbook を開始する方法を示します。パラメーターについて詳しくは、次に取り上げる「[Runbook のパラメーター](#Runbook-parameters)」をご覧ください。

	$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" –Parameters $params

## Runbook のパラメーター

Azure 管理ポータルまたは Windows PowerShell を使用して Runbook を開始する場合、命令は、Azure Automation Web サービスを通じて送信されます。このサービスは、複雑なデータ型を持つパラメーターをサポートしていません。複合型のパラメーター値を指定する必要がある場合、「[別の Runbook からの Runbook の開始](http://msdn.microsoft.com/library/azure/dn857355.aspx)」で説明されているように別の Runbook からインラインで呼び出す必要があります。

Azure Automation Web サービスは、次のセクションで説明されているように特定のデータ型を使用してパラメーターに対して特殊な機能を提供します。

### 名前付きの値

パラメーターのデータ型が [object] の場合、*{"Name1":Value1, "Name2":Value2, "Name3":Value3}* という JSON 形式を使用して名前付きの値の一覧を送信できます。これらの値は単純型にする必要があります。Runbook は、それぞれの名前付き値に対応するプロパティが設定された [PSCustomObject](http://msdn.microsoft.com/library/azure/system.management.automation.pscustomobject(v=vs.85).aspx) としてパラメーターを受け取ります。

user というパラメーターを受け入れる次のテスト Runbook について考慮してください。

	Workflow Test-Parameters
	{
	   param ( 
	      [Parameter(Mandatory=$true)][object]$user
	   )
	    if ($user.Show) {
	        foreach ($i in 1..$user.RepeatCount) {
	            $user.FirstName
	            $user.LastName
	        }
	    } 
	}

user パラメーターに対して、次のテキストを使用できます。

	{"FirstName":"Joe","LastName":"Smith","RepeatCount":2,"Show":true}

次の出力が生成されます。

	Joe
	Smith
	Joe
	Smith

### 配列

パラメーターが [array] や [string] などの配列の場合、*[Value1,Value2,Value3]* という JSON 形式を使用して値の一覧を送信できます。これらの値は単純型にする必要があります。

*user* というパラメーターを受け入れる次のテスト Runbook について考慮してください。

	Workflow Test-Parameters
	{
	   param ( 
	      [Parameter(Mandatory=$true)][array]$user
	   )
	    if ($user[3]) {
	        foreach ($i in 1..$user[2]) {
	            $ user[0]
	            $ user[1]
	        }
	    } 
	}

user パラメーターに対して、次のテキストを使用できます。

	["Joe","Smith",2,true]

次の出力が生成されます。

	Joe
	Smith
	Joe
	Smith

### 資格情報

パラメーターのデータ型が **PSCredential** の場合、Azure Automation の[資格情報資産](http://msdn.microsoft.com/library/azure/dn940015.aspx)の名前を指定できます。Runbook は、指定した名前を持つ資格情報を取得します。

credential というパラメーターを受け入れる次のテスト Runbook について考慮してください。

	Workflow Test-Parameters
	{
	   param ( 
	      [Parameter(Mandatory=$true)][PSCredential]$credential
	   )
	   $credential.UserName
	}

*My Credential* という資格情報資産がある場合、user パラメーターで次のテキストを使用できます。

	My Credential

資格情報のユーザー名が *jsmith* であると仮定すると、次の出力が生成されます。

	jsmith

## 関連記事:

- [別の Runbook からの Runbook の開始](http://msdn.microsoft.com/library/azure/dn857355.aspx) 

<!---HONumber=Oct15_HO3-->