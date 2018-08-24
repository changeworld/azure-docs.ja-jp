---
title: Azure Automation の証明書資産
description: 証明書を Azure Automation に安全に保存し、Runbook または DSC 構成でアクセスして Azure およびサードパーティのリソースで認証できます。  この記事では、証明書の詳細およびテキスト作成とグラフィカル作成の両方で証明書を使用する方法について説明します。
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c13da6ff7c864ffa365dbad33d6eb0cf2e35fa42
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2018
ms.locfileid: "42140544"
---
# <a name="certificate-assets-in-azure-automation"></a>Azure Automation の証明書資産

証明書を Azure Automation に安全に保存し、Azure Resource Manager リソースの **Get-AzureRmAutomationCertificate** アクティビティを使用して、Runbook または DSC 構成でアクセスできます。 この機能により、認証に証明書を使用する Runbook および DSC 構成を作成したり、それらを Azure またはサードパーティのリソースに追加したりすることができます。

>[!NOTE]
>Azure Automation でセキュリティ保護される資産としては、資格情報、証明書、接続、暗号化された変数などがあります。 これらの資産は、各 Automation アカウント用に生成された一意のキーを使って暗号化され、Azure Automation に保存されます。 このキーは Key Vault に格納されます。 セキュリティで保護された資産を保存する前に、キーが Key Vault から読み込まれ、資産の暗号化に使われます。

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell コマンドレット
AzureRM の場合、Windows PowerShell で Automation 資格情報資産を作成および管理するには、次の表のコマンドレットを使用します。 これらのコマンドレットは、Automation Runbook と DSC 構成に使用できる [AzureRM.Automation モジュール](/powershell/azure/overview) に付属しています。

|コマンドレット|説明|
|:---|:---|
|[Get-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Runbook または DSC 構成で使用する証明書についての情報を取得します。 Get-AutomationCertificate アクティビティから取得できるのは、証明書自体のみです。|
|[New-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/new-azurermautomationcertificate)|新しい証明書を Azure Automation に作成します。|
[Remove-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|証明書を Azure Automation から削除します。|新しい証明書を Azure Automation に作成します。
|[Set-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/set-azurermautomationcertificate)|証明書ファイルのアップロードや .pfx のパスワードの設定など、既存の証明書のプロパティを設定します。|
|[Add-AzureCertificate](https://msdn.microsoft.com/library/azure/dn495214.aspx)|指定されたクラウド サービスのサービス証明書をアップロードします。|

## <a name="activities"></a>アクティビティ
Runbook および DSC 構成で証明書にアクセスするために、次の表のアクティビティが使用されます。

| アクティビティ | 説明 |
|:---|:---|
|Get-AutomationCertificate|Runbook または DSC 構成で使用する証明書を取得します。 [System.Security.Cryptography.X509Certificates.X509Certificate2](https://msdn.microsoft.com/library/system.security.cryptography.x509certificates.x509certificate2.aspx) オブジェクトを返します。|

> [!NOTE] 
> Runbook または DSC 構成内で **Get-AutomationCertificate** の –Name パラメーターに変数を使用すると、設計時に Runbook または DSC 構成と Automation 変数の間の依存関係の検出が複雑になるため、使用しないようにする必要があります。

## <a name="python2-functions"></a>Python2 関数

次の表の関数を使用して、Python2 Runbook の証明書にアクセスします。

| 関数 | 説明 |
|:---|:---|
| automationassets.get_automation_certificate | 証明書の資産に関する情報を取得します。 |

> [!NOTE]
> 資産関数にアクセスするには、お使いの Python Runbook の最初の **automationassets** モジュールをインポートする必要があります。

## <a name="creating-a-new-certificate"></a>新しい証明書の作成

新しい証明書を作成するときは、cer または pfx ファイルを Azure Automation にアップロードします。 証明書をエクスポート可能とマークすると、Azure Automation の証明書ストアから転送できます。 エクスポート可能ではない場合は、Runbook または DSC 構成内での署名にのみ使用できます。 Azure Automation では、証明書のプロバイダーが **Microsoft Enhanced RSA and AES Cryptographic Provider** である必要があります。

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Azure ポータルで新しい証明書を作成するには

1. Automation アカウントから、**[資産]** タイルをクリックして **[資産]** ブレードを開きます。
1. **[証明書]** タイルをクリックして **[証明書]** ブレードを開きます。
1. ブレード上部の **[証明書の追加]** をクリックします。
1. Type a name for the certificate in the **Name** box.
1. .cer または .pfx ファイルを参照するには、**[Upload a certificate file]\(証明書ファイルのアップロード\)** の **[ファイルの選択]** をクリックします。 .pfx ファイルを選択する場合は、パスワードおよびエクスポートを許可するかどうかを指定します。
1. **[作成]** をクリックして、新しい証明書資産を保存します。

### <a name="to-create-a-new-certificate-with-windows-powershell"></a>Windows PowerShell で新しい証明書を作成するには

次の例では、新しい Automation 証明書を作成してエクスポート可能に指定する方法について説明します。 これは既存の .pfx ファイルをインポートします。

```powershell-interactive
$certName = 'MyCertificate'
$certPath = '.\MyCert.pfx'
$certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable -ResourceGroupName $ResourceGroup
```

## <a name="using-a-certificate"></a>証明書の使用

証明書を使用するには、**Get-AutomationCertificate** アクティビティを使用する必要があります。 [Get-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationcertificate?view=azurermps-6.6.0) コマンドレットは、証明書資産に関する情報は返しますが証明書自体を返さないので使用できません。

### <a name="textual-runbook-sample"></a>テキストの Runbook のサンプル

次のサンプル コードでは、Runbook でクラウド サービスに証明書を追加する方法を示します。 このサンプルでは、パスワードは暗号化された Automation 変数から取得されます。

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResouceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>グラフィカルな Runbook のサンプル

グラフィカル エディターの [ライブラリ] ウィンドウで証明書を右クリックして **[キャンバスに追加]** を選択することにより、**Get-AutomationCertificate** をグラフィカルな Runbook に追加します。

![証明書をキャンバスに追加します](media/automation-certificates/automation-certificate-add-to-canvas.png)

次の図は、グラフィカルな Runbook で証明書を使用する例を示したものです。 これは、テキストの Runbook からクラウド サービスに証明書を追加する前述の例と同じです。

![グラフィカル作成の例 ](media/automation-certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Python2 サンプル
次の例では、Python2 Runbook の証明書にアクセスする方法を示します。

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert 
```

## <a name="next-steps"></a>次の手順

- リンクを使用して、Runbook で実行するように設計されているアクティビティの論理フローを制御する方法については、「[グラフィカル作成でのリンク](automation-graphical-authoring-intro.md#links-and-workflow)」をご覧ください。 
