---
title: "Azure Automation の証明書資産 | Microsoft Docs"
description: "証明書を Azure Automation に安全に保存し、Runbook または DSC 構成でアクセスして Azure およびサードパーティのリソースで認証できます。  この記事では、証明書の詳細およびテキスト作成とグラフィカル作成の両方で証明書を使用する方法について説明します。"
services: automation
documentationcenter: 
author: mgoedtel
manager: stevenka
editor: tysonn
ms.assetid: ac9c22ae-501f-42b9-9543-ac841cf2cc36
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: magoedte;bwren
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 1973a3523e121414dfbebf4d00cd2d4fe2005d2f


---
# <a name="certificate-assets-in-azure-automation"></a>Azure Automation の証明書資産
証明書を Azure Automation に安全に保存し、 **Get-AutomationCertificate** アクティビティを使用して Runbook または DSC 構成でアクセスできます。 そのため、認証に証明書を使用する Runbook または DSC 構成を作成したり、それらを Azure またはサードパーティのリソースに追加したりすることができます。

> [!NOTE]
> Azure Automation でセキュリティ保護される資産としては、資格情報、証明書、接続、暗号化された変数などがあります。 これらの資産は、各 Automation アカウント用に生成された一意のキーを使用して暗号化され、Azure Automation に保存されます。 このキーはマスター証明書によって暗号化され、Azure Automation に保存されます。 セキュリティで保護された資産を格納する前に、Automation アカウントのキーがマスター証明書を使用して復号化され、資産の暗号化に使用されます。
> 
> 

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell コマンドレット
Windows PowerShell で Automation 証明書資産を作成および管理するには、次の表のコマンドレットを使用します。 これらのコマンドレットは、Automation Runbook と DSC 構成に使用できる [Azure PowerShell モジュール](../powershell-install-configure.md) に付属しています。

| コマンドレット | 説明 |
|:--- |:--- |
| [Get-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913765.aspx) |証明書に関する情報を取得します。 Get-AutomationCertificate アクティビティから取得できるのは、証明書自体のみです。 |
| [New-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913764.aspx) |新しい証明書を Azure Automation にインポートします。 |
| [Remove-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913773.aspx) |証明書を Azure Automation から削除します。 |
| [Set-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913763.aspx) |証明書ファイルのアップロードや .pfx のパスワードの設定など、既存の証明書のプロパティを設定します。 |

## <a name="activities-to-access-certificates"></a>証明書にアクセスするアクティビティ
Runbook または DSC 構成で証明書にアクセスするために、次の表のアクティビティが使用されます。

| アクティビティ | 説明 |
|:--- |:--- |
| Get-AutomationCertificate |Runbook または DSC 構成で使用する証明書を取得します。 |

> [!NOTE]
> Get-AutomationCertificate の –Name パラメーターには変数を使用しないでください。変数を使用すると、設計時に、Runbook または DSC 構成と証明書資産間の依存関係の検出が複雑になる可能性があります。
> 
> 

## <a name="creating-a-new-certificate"></a>新しい証明書の作成
新しい証明書を作成するときは、cer または pfx ファイルを Azure Automation にアップロードします。 証明書をエクスポート可能とマークすると、Azure Automation の証明書ストアから転送できます。 エクスポート可能ではない場合は、Runbook または DSC 構成内での署名にのみ使用できます。

### <a name="to-create-a-new-certificate-with-the-azure-classic-portal"></a>Azure クラシック ポータルで新しい証明書を作成するには
1. Automation アカウントから、ウィンドウの上部にある **[資産]** をクリックします。
2. ウィンドウの下部にある **[設定の追加]**をクリックします。
3. Click **Add Credential**.
4. In the **Credential Type** dropdown, select **Certificate**.
5. Type a name for the certificate in the **Name** box and click the right arrow.
6. .cer または .pfx ファイルを参照します。  .pfx ファイルを選択する場合は、パスワードおよびエクスポートを許可するかどうかを指定します。
7. チェック マークをクリックして証明書ファイルをアップロードし、新しい証明書資産を保存します。

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Azure ポータルで新しい証明書を作成するには
1. Automation アカウントから、**[資産]** 部分をクリックして **[資産]** ブレードを開きます。
2. **[証明書]** 部分をクリックして **[証明書]** ブレードを開きます。
3. ブレード上部の **[証明書の追加]** をクリックします。
4. Type a name for the certificate in the **Name** box.
5. Click **Select a file** under **Upload a certificate file** to browse for a .cer or .pfx file.  .pfx ファイルを選択する場合は、パスワードおよびエクスポートを許可するかどうかを指定します。
6. **[作成]** をクリックして、新しい証明書資産を保存します。

### <a name="to-create-a-new-certificate-with-windows-powershell"></a>Windows PowerShell で新しい証明書を作成するには
次のサンプル コマンドでは、新しい Automation 証明書を作成してエクスポート可能に指定する方法について説明します。 これは既存の .pfx ファイルをインポートします。

    $certName = 'MyCertificate'
    $certPath = '.\MyCert.pfx'
    $certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force

    New-AzureAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable

## <a name="using-a-certificate"></a>証明書の使用
証明書を使用するには、 **Get-AutomationCertificate** アクティビティを使用する必要があります。 [Get-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913765.aspx) コマンドレットは、証明書資産に関する情報は返しますが証明書自体を返さないので使用できません。

### <a name="textual-runbook-sample"></a>テキストの Runbook のサンプル
次のサンプル コードでは、Runbook でクラウド サービスに証明書を追加する方法を示します。 このサンプルでは、パスワードは暗号化された Automation 変数から取得されます。

    $serviceName = 'MyCloudService'
    $cert = Get-AutomationCertificate -Name 'MyCertificate'
    $certPwd = Get-AutomationVariable –Name 'MyCertPassword'
    Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert

### <a name="graphical-runbook-sample"></a>グラフィカルな Runbook のサンプル
グラフィカル エディターの [ライブラリ] ウィンドウで証明書を右クリックして **[キャンバスに追加]** を選択することにより、**Get-AutomationCertificate** をグラフィカルな Runbook に追加します。

![](media/automation-certificates/certificate-add-canvas.png)

次の図は、グラフィカルな Runbook で証明書を使用する例を示したものです。  これは、テキストの Runbook からクラウド サービスに証明書を追加する前述の例と同じです。  

この例では、サービスに対する認証に接続オブジェクトを使用する **Send-TwilioSMS** アクティビティに対して設定された **UseConnectionObject** パラメーターを使用します。  シーケンス リンクは Connection パラメーターが予期しない単一のオブジェクトを含むコレクションを返すため、ここでは [パイプライン リンク](automation-graphical-authoring-intro.md#links-and-workflow) を使用する必要があります。

![](media/automation-certificates/add-certificate.png)

## <a name="see-also"></a>関連項目
* [グラフィカル作成でのリンク](automation-graphical-authoring-intro.md#links-and-workflow) 




<!--HONumber=Nov16_HO3-->


