<properties 
   pageTitle="Azure Automation での接続資産 | Microsoft Azure"
   description="Azure Automation の接続資産には、Runbook または DSC 構成から外部サービスまたはアプリケーションに接続するために必要な情報が含まれます。この記事では、接続の詳細およびテキスト作成とグラフィカル作成の両方で接続を使用する方法について説明します。"
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
   ms.date="01/27/2016"
   ms.author="bwren" />

# Azure Automation での接続資産

Automation の接続資産には、Runbook または DSC 構成から外部サービスまたはアプリケーションに接続するために必要な情報が含まれます。This may include information required for authentication such as a username and password in addition to connection information such as a URL or a port.接続の値は、複数の変数を作成する場合とは対照的に、1 つの資産内の特定のアプリケーションに接続するためのプロパティをすべて保持します。ユーザーは、1 つの場所で接続のための値を編集でき、1 つのパラメーターで Runbook または DSC 構成に接続の名前を渡すことができます。Runbook または DSC 構成では、**Get-AutomationConnection** アクティビティで接続のプロパティにアクセスできます。

接続を作成するときは、*接続の種類*を指定する必要があります。接続の種類は、一連のプロパティを定義しているテンプレートです。接続では、その接続の種類で定義されている各プロパティの値を定義します。接続の種類は、統合モジュールで Azure Automation に追加されるか、または [Azure Automation API](http://msdn.microsoft.com/library/azure/mt163818.aspx) で作成されます。接続を作成するときに使用できる接続の種類は、オートメーション アカウントにインストールされているものだけです。

>[AZURE.NOTE] Secure assets in Azure Automation include credentials, certificates, connections, and encrypted variables.これらの資産は、各 Automation アカウント用に生成された一意のキーを使用して暗号化され、Azure Automation に保存されます。This key is encrypted by a master certificate and stored in Azure Automation.セキュリティで保護された資産を格納する前に、オートメーション アカウントのキーがマスター証明書を使用して復号化され、資産の暗号化に使用されます。

## Windows PowerShell コマンドレット

次の表に示す Windows PowerShell コマンドレットを使用して、オートメーションの接続を作成および管理します。これらは、Automation Runbook および DSC 構成で使用できる [Azure PowerShell モジュール](../powershell-install-configure.md)に付属しています。

|コマンドレット|説明|
|:---|:---|
|[Get-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921828.aspx)|接続を取得します。接続のフィールドの値のハッシュ テーブルが含まれます。|
|[New-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921825.aspx)|新しい接続を作成します。|
|[Remove-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921827.aspx)|既存の接続を削除します。|
|[Set-AzureAutomationConnectionFieldValue](http://msdn.microsoft.com/library/dn921826.aspx)|既存の接続の特定のフィールドの値を設定します。|

## アクティビティ

次の表のアクティビティは、Runbook または DSC 構成で接続にアクセスするために使用されます。

|アクティビティ|Description|
|---|---|
|Get-AutomationConnection|使用する接続を取得します。接続のプロパティのハッシュ テーブルを返します。|

>[AZURE.NOTE] **Get-AutomationConnection** の –Name パラメーターには変数を使用しないでください。変数を使用すると、設計時に Runbook または DSC と接続資産間の依存関係の検出が複雑になる可能性があります。

## 新しい接続の作成

### Azure ポータルで新しい接続を作成するには

1. オートメーション アカウントから、ウィンドウの上部にある **[資産]** をクリックします。
1. ウィンドウの下部にある **[設定の追加]** をクリックします。
1. **[接続の追加]** をクリックします。
2. **[接続の種類]** ドロップダウンで、作成する接続の種類を選択します。ウィザードでその特定の種類のプロパティが表示されます。
1. ウィザードを完了し、チェック ボックスをクリックして新しい接続を保存します。


### Azure プレビュー ポータルで新しい接続を作成するには

1. オートメーション アカウントから、**[資産]** 部分をクリックして **[資産]** ブレードを開きます。
1. **[接続]** 部分をクリックして、**[接続]** ブレードを開きます。
1. ブレード上部の **[接続の追加]** をクリックします。
2. **[種類]** ドロップダウンで、作成する接続の種類を選択します。フォームにその特定の種類のプロパティが表示されます。
1. フォームに入力し、**[作成]** をクリックして新しい接続を保存します。



### Windows PowerShell で新しい接続を作成するには

Windows PowerShell の [New-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921825.aspx) コマンドレットを使用して新しい接続を作成します。このコマンドレットには、接続の種類で定義されている各プロパティの値を定義している[ハッシュ テーブル](http://technet.microsoft.com/library/hh847780.aspx)を受け取る **ConnectionFieldValues** という名前のパラメーターがあります。


次のサンプル コマンドでは、テキスト メッセージを送受信できるテレフォニー サービスである [Twilio](http://www.twilio.com) に対する新しい接続を作成します。Twilio 接続種類を含むサンプルの統合モジュールは、[スクリプト センター](http://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8)で入手できます。この接続の種類では、Twilio への接続時のアカウント検証に必要なアカウント SID と承認トークンのプロパティが定義されています。このサンプル コードが動作するには、[このモジュールをダウンロード](http://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8)して、オートメーション アカウントにインストールする必要があります。

	$AccountSid = "DAf5fed830c6f8fac3235c5b9d58ed7ac5"
	$AuthToken  = "17d4dadfce74153d5853725143c52fd1"
	$FieldValues = @{"AccountSid" = $AccountSid;"AuthToken"=$AuthToken}

	New-AzureAutomationConnection -AutomationAccountName "MyAutomationAccount" -Name "TwilioConnection" -ConnectionTypeName "Twilio" -ConnectionFieldValues $FieldValues


## Runbook または DSC 構成での接続の使用

**Get-AutomationConnection** コマンドレットを使用して、Runbook または DSC 構成の接続を取得します。このアクティビティは、接続のさまざまなフィールド値を取得し、その値を[ハッシュ テーブル](http://go.microsoft.com/fwlink/?LinkID=324844)として返します。このハッシュ テーブルは、Runbook または DSC 構成の適切なコマンドで使用できます。

### テキストの Runbook のサンプル
次のサンプル コマンドでは、前の例の Twilio 接続を使用して Runbook からテキスト メッセージを送信する方法を示します。ここで使用する Send-TwilioSMS アクティビティには 2 つのパラメーター セットがあり、それぞれ異なる方法で Twilio サービスに対する認証を行います。One uses a connection object and another uses individual parameters for the Account SID and Authorization Token.このサンプルでは両方の方法を示します。

	$Con = Get-AutomationConnection -Name "TwilioConnection"
	$NumTo = "14255551212"
	$NumFrom = "15625551212"
	$Body = "Text from Azure Automation."

	#Send text with connection object.
	Send-TwilioSMS -Connection $Con -From $NumFrom -To $NumTo -Body $Body

	#Send text with connection properties.
	Send-TwilioSMS -AccountSid $Con.AccountSid -AuthToken $Con.AuthToken $Con -From $NumFrom -To $NumTo -Body $Body

### グラフィカルな Runbook のサンプル

グラフィカル エディターの [ライブラリ] ウィンドウで接続を右クリックして **[キャンバスに追加]** を選択することにより、**Get-AutomationConnection** アクティビティをグラフィカルな Runbook に追加します。

![](media/automation-connections/connection-add-canvas.png)

次の図は、グラフィカルな Runbook で接続を使用する例を示したものです。これは、前に示したテキスト Runbook から Twilio を使用してテキスト メッセージを送信する例と同じものです。この例では、サービスに対する認証に接続オブジェクトを使用する **Send-TwilioSMS** アクティビティに対して設定された **UseConnectionObject** パラメーターを使用します。Connection パラメーターは 1 つのオブジェクトを受け取るので、ここでは[パイプライン リンク](automation-graphical-authoring-intro.md#links-and-workflow)を使用します。

**To** パラメーターの値に対して定数値ではなく PowerShell 式を使用している理由は、複数の番号に送信できるようにこのパラメーターが文字列配列値の型を受け取るためです。PowerShell 式を使用すると、1 つの値または配列を提供できます。

![](media/automation-connections/get-connection-object.png)

次に示す図は上と同じ例ですが、認証に接続オブジェクトを使用する代わりに、AccountSid および AuthToken パラメーターを個別に指定する **SpecifyConnectionFields** パラメーター セットを使用しています。この場合は、オブジェクト自体ではなく接続のフィールドを指定します。

![](media/automation-connections/get-connection-properties.png)



## 関連記事:

- [グラフィカル作成でのリンク](automation-graphical-authoring-intro.md#links-and-workflow)
 

<!---HONumber=AcomDC_0128_2016-->