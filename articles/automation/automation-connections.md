---
title: "Azure Automation での接続資産 | Microsoft Docs"
description: "Azure Automation の接続資産には、Runbook または DSC 構成から外部サービスまたはアプリケーションに接続するために必要な情報が含まれます。 この記事では、接続の詳細およびテキスト作成とグラフィカル作成の両方で接続を使用する方法について説明します。"
services: automation
documentationcenter: 
author: bwren
manager: stevenka
editor: tysonn
ms.assetid: f0239017-5c66-4165-8cca-5dcb249b8091
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: a4b1092ec4d7f4784e8f421fc633affce7f7185e


---
# <a name="connection-assets-in-azure-automation"></a>Azure Automation での接続資産
Automation の接続資産には、Runbook または DSC 構成から外部サービスまたはアプリケーションに接続するために必要な情報が含まれます。 これには、URL やポートなどの接続情報に加えて、ユーザー名とパスワードなど認証に必要な情報も含まれる場合があります。 接続の値は、複数の変数を作成する場合とは対照的に、1 つの資産内の特定のアプリケーションに接続するためのプロパティをすべて保持します。 ユーザーは、1 つの場所で接続のための値を編集でき、1 つのパラメーターで Runbook または DSC 構成に接続の名前を渡すことができます。 Runbook または DSC 構成では、 **Get-AutomationConnection** アクティビティで接続のプロパティにアクセスできます。

接続を作成するときは、 *接続の種類*を指定する必要があります。 接続の種類は、一連のプロパティを定義しているテンプレートです。 接続では、その接続の種類で定義されている各プロパティの値を定義します。 接続の種類は、統合モジュールで Azure Automation に追加されるか、または [Azure Automation API](http://msdn.microsoft.com/library/azure/mt163818.aspx)で作成されます。 接続を作成するときに使用できる接続の種類は、Automation アカウントにインストールされているものだけです。

> [!NOTE]
> Azure Automation でセキュリティ保護される資産としては、資格情報、証明書、接続、暗号化された変数などがあります。 これらの資産は、各 Automation アカウント用に生成された一意のキーを使用して暗号化され、Azure Automation に保存されます。 このキーはマスター証明書によって暗号化され、Azure Automation に保存されます。 セキュリティで保護された資産を格納する前に、Automation アカウントのキーがマスター証明書を使用して復号化され、資産の暗号化に使用されます。
> 
> 

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell コマンドレット
Windows PowerShell で Automation 接続を作成および管理するには、次の表のコマンドレットを使用します。 これらのコマンドレットは、Automation Runbook と DSC 構成に使用できる [Azure PowerShell モジュール](../powershell-install-configure.md) に付属しています。

| コマンドレット | 説明 |
|:--- |:--- |
| [Get-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921828.aspx) |接続を取得します。 接続のフィールドの値のハッシュ テーブルが含まれます。 |
| [New-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921825.aspx) |新しい接続を作成します。 |
| [Remove-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921827.aspx) |既存の接続を削除します。 |
| [Set-AzureAutomationConnectionFieldValue](http://msdn.microsoft.com/library/dn921826.aspx) |既存の接続の特定のフィールドの値を設定します。 |

## <a name="activities"></a>アクティビティ
次の表のアクティビティは、Runbook または DSC 構成で接続にアクセスするために使用されます。

| アクティビティ | 説明 |
| --- | --- |
| Get-AutomationConnection |使用する接続を取得します。 接続のプロパティのハッシュ テーブルを返します。 |

> [!NOTE]
> **Get-AutomationConnection** の –Name パラメーターには変数を使用しないでください。変数を使用すると、設計時に Runbook または DSC と接続資産間の依存関係の検出が複雑になる可能性があります。
> 
> 

## <a name="creating-a-new-connection"></a>新しい接続の作成
### <a name="to-create-a-new-connection-with-the-azure-classic-portal"></a>Azure クラシック ポータルで新しい接続を作成するには
1. Automation アカウントから、ウィンドウの上部にある **[資産]** をクリックします。
2. ウィンドウの下部にある **[設定の追加]**をクリックします。
3. **[接続の追加]**をクリックします。
4. **[接続の種類]** ドロップダウンで、作成する接続の種類を選択します。  ウィザードでその特定の種類のプロパティが表示されます。
5. ウィザードを完了し、チェック ボックスをクリックして新しい接続を保存します。

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Azure ポータルで新しい接続を作成するには
1. Automation アカウントから、**[資産]** 部分をクリックして **[資産]** ブレードを開きます。
2. **[接続]** 部分をクリックして、**[接続]** ブレードを開きます。
3. ブレード上部の **[接続の追加]** をクリックします。
4. **[種類]** ドロップダウンで、作成する接続の種類を選択します。 フォームにその特定の種類のプロパティが表示されます。
5. フォームに入力し、 **[作成]** をクリックして新しい接続を保存します。

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Windows PowerShell で新しい接続を作成するには
Windows PowerShell の [New-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921825.aspx) コマンドレットを使用して新しい接続を作成します。 このコマンドレットには、接続の種類で定義されている各プロパティの値を定義している **ハッシュ テーブル** を受け取る [ConnectionFieldValues](http://technet.microsoft.com/library/hh847780.aspx) という名前のパラメーターがあります。

次のサンプル コマンドでは、テキスト メッセージを送受信できるテレフォニー サービスである [Twilio](http://www.twilio.com) に対する新しい接続を作成します。  Twilio 接続種類を含むサンプルの統合モジュールは、 [スクリプト センター](http://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8)で入手できます。  この接続の種類では、Twilio への接続時のアカウント検証に必要なアカウント SID と承認トークンのプロパティが定義されています。  このサンプル コードが動作するには、 [このモジュールをダウンロード](http://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8) して、Automation アカウントにインストールする必要があります。

    $AccountSid = "DAf5fed830c6f8fac3235c5b9d58ed7ac5"
    $AuthToken  = "17d4dadfce74153d5853725143c52fd1"
    $FieldValues = @{"AccountSid" = $AccountSid;"AuthToken"=$AuthToken}

    New-AzureAutomationConnection -AutomationAccountName "MyAutomationAccount" -Name "TwilioConnection" -ConnectionTypeName "Twilio" -ConnectionFieldValues $FieldValues


## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Runbook または DSC 構成での接続の使用
**Get-AutomationConnection** コマンドレットを使用して、Runbook または DSC 構成の接続を取得します。  このアクティビティは、接続のさまざまなフィールド値を取得し、その値を[ハッシュ テーブル](http://go.microsoft.com/fwlink/?LinkID=324844)として返します。このハッシュ テーブルは、Runbook または DSC 構成の適切なコマンドで使用できます。

### <a name="textual-runbook-sample"></a>テキストの Runbook のサンプル
次のサンプル コマンドでは、前の例の Twilio 接続を使用して Runbook からテキスト メッセージを送信する方法を示します。  ここで使用する Send-TwilioSMS アクティビティには 2 つのパラメーター セットがあり、それぞれ異なる方法で Twilio サービスに対する認証を行います。  一方は接続オブジェクトを使用し、もう一方はアカウント SID と承認トークンに別々のパラメーターを使用します。  このサンプルでは両方の方法を示します。

    $Con = Get-AutomationConnection -Name "TwilioConnection"
    $NumTo = "14255551212"
    $NumFrom = "15625551212"
    $Body = "Text from Azure Automation."

    #Send text with connection object.
    Send-TwilioSMS -Connection $Con -From $NumFrom -To $NumTo -Body $Body

    #Send text with connection properties.
    Send-TwilioSMS -AccountSid $Con.AccountSid -AuthToken $Con.AuthToken -From $NumFrom -To $NumTo -Body $Body

### <a name="graphical-runbook-samples"></a>グラフィカルな Runbook のサンプル
グラフィカル エディターの [ライブラリ] ウィンドウで接続を右クリックして **[キャンバスに追加]** を選択することにより、**Get-AutomationConnection** アクティビティをグラフィカルな Runbook に追加します。

![](media/automation-connections/connection-add-canvas.png)

次の図は、グラフィカルな Runbook で接続を使用する例を示したものです。  これは、前に示したテキスト Runbook から Twilio を使用してテキスト メッセージを送信する例と同じものです。  この例では、サービスに対する認証に接続オブジェクトを使用する **Send-TwilioSMS** アクティビティに対して設定された **UseConnectionObject** パラメーターを使用します。  Connection パラメーターは 1 つのオブジェクトを受け取るので、ここでは [パイプライン リンク](automation-graphical-authoring-intro.md#links-and-workflow) を使用します。

**To** パラメーターの値に対して定数値ではなく PowerShell 式を使用している理由は、複数の番号に送信できるようにこのパラメーターが文字列配列値の型を受け取るためです。  PowerShell 式を使用すると、1 つの値または配列を提供できます。

![](media/automation-connections/get-connection-object.png)

次に示す図は上と同じ例ですが、認証に接続オブジェクトを使用する代わりに、AccountSid および AuthToken パラメーターを個別に指定する **SpecifyConnectionFields** パラメーター セットを使用しています。  この場合は、オブジェクト自体ではなく接続のフィールドを指定します。  

![](media/automation-connections/get-connection-properties.png)

## <a name="related-articles"></a>関連記事:
* [グラフィカル作成でのリンク](automation-graphical-authoring-intro.md#links-and-workflow)




<!--HONumber=Nov16_HO3-->


