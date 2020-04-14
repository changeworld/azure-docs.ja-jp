---
title: Azure Automation で Azure AD を使用して Azure に対して認証する
description: Azure Automation 内で Azure AD を Azure への認証プロバイダーとして使用する方法について説明します。
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 90338a1ffa79e6c2347832cb2e74633db02ec72d
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548339"
---
# <a name="use-azure-ad-in-azure-automation-to-authenticate-to-azure"></a>Azure Automation で Azure AD を使用して Azure に対して認証する

[Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) サービスを使用すると、ユーザー管理、ドメイン管理、シングル サインオン構成などの多くの管理タスクを実行できます。 この記事では、Azure Automation 内で Azure AD を Azure への認証プロバイダーとして使用する方法について説明します。 

>[!NOTE]
>この記事は、新しい Azure PowerShell Az モジュールを使用するために更新されました。 AzureRM モジュールはまだ使用でき、少なくとも 2020 年 12 月までは引き続きバグ修正が行われます。 Az モジュールと AzureRM の互換性の詳細については、「[Introducing the new Azure PowerShell Az module (新しい Azure PowerShell Az モジュールの概要)](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)」を参照してください。 Hybrid Runbook Worker での Az モジュールのインストール手順については、「[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)」を参照してください。 Automation アカウントについては、「[Azure Automation の Azure PowerShell モジュールを更新する方法](automation-update-azure-modules.md)」に従って、モジュールを最新バージョンに更新できます。

## <a name="installing-azure-ad-modules"></a>Azure AD モジュールのインストール

次の PowerShell モジュールを使用して Azure AD を有効にすることができます。

* Azure Active Directory PowerShell for Graph (AzureRM および Az モジュール)。 Azure Automation には、AzureRM モジュールとその最新のアップグレードである Az モジュールが付属しています。 Azure AD ユーザー (OrgId) の資格情報ベースの認証を使用した Azure への非対話型認証が機能に含まれています。 「[Azure AD 2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76)」を参照してください。

* Microsoft Azure Active Directory for Windows PowerShell (MSOnline モジュール)。 このモジュールを使用すると、Office 365 を含む Microsoft Online との対話が可能になります。

>[!NOTE]
>PowerShell Core は、MSOnline モジュールをサポートしていません。 モジュール コマンドレットを使用するには、それらを Windows PowerShell から実行する必要があります。 MSOnline モジュールではなく、新しい Azure Active Directory PowerShell for Graph モジュールを使用することをお勧めします。 

### <a name="preinstallation"></a>プレインストール

コンピューターに Azure AD モジュールをインストールする前に:

* AzureRM または Az モジュールおよび MSOnline モジュールの以前のバージョンがある場合はアンインストールします。 

* Microsoft Online Services サインイン アシスタントをアンインストールして、新しい PowerShell モジュールが正しく動作するようにします。  

### <a name="install-the-azurerm-and-az-modules"></a>AzureRM および Az モジュールをインストールする

>[!NOTE]
>これらのモジュールを使用するには、64 ビット バージョンの Windows に PowerShell バージョン 5.1 以降を使用する必要があります。 

1. Windows Management Framework (WMF) 5.1 をインストールします。 「[WMF 5.1 のインストールと構成](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure?view=powershell-7)」を参照してください。

2. 「[PowerShellGet を使用した Windows への Azure PowerShell のインストール](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0)」の手順に従って AzureRM または Az をインストールします。

### <a name="install-the-msonline-module"></a>MSOnline モジュールをインストールする

>[!NOTE]
>MSOnline モジュールをインストールするには、Office 365 管理者ロールのメンバーである必要があります。 「[管理者ロールについて](https://docs.microsoft.com/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide)」を参照してください。

1. コンピューターで Microsoft .NET Framework 3.5.x 機能が有効であることを確認します。 コンピューターに新しいバージョンがインストールされている可能性がありますが、.NET Framework の以前のバージョンとの下位互換性を有効または無効にすることができます。 

2. [Microsoft Online Services サインイン アシスタント](https://www.microsoft.com/download/details.aspx?id=41950)の 64 ビット バージョンをインストールします。

3. 管理者特権で Windows PowerShell コマンド プロンプトを作成するには、管理者として Windows PowerShell を実行します。

4. [MSOnline 1.0](http://www.powershellgallery.com/packages/MSOnline/1.0) から Azure Active Directory をデプロイします。

5. NuGet プロバイダーのインストールを求めるメッセージが表示されたら、「Y」と入力し、Enter キーを押します。

6. [PSGallery](https://www.powershellgallery.com/) からモジュールをインストールするように求めるメッセージが表示されたら、「Y」と入力し、Enter キーを押します。

### <a name="install-support-for-pscredential"></a>PSCredential のサポートをインストールする

Azure Automation では、[PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) クラスを使用して資格情報資産を表します。 スクリプトで `Get-AutomationPSCredential` コマンドレットを使用して `PSCredential` オブジェクトを取得します。 詳細については、「[Azure Automation での資格情報資産](shared-resources/credentials.md)」を参照してください。

## <a name="assigning-a-subscription-administrator"></a>サブスクリプション管理者の割り当て

Azure サブスクリプションの管理者を割り当てる必要があります。 このユーザーには、サブスクリプション スコープの所有者のロールがあります。 「[Azure Automation におけるロールベースのアクセス制御](automation-role-based-access-control.md)」を参照してください。 

## <a name="changing-the-azure-ad-users-password"></a>Azure AD ユーザーのパスワードの変更

Azure AD ユーザーのパスワードを変更するには:

1. Azure からログ アウトします。

2. 管理者に、(ドメインを含む) 完全なユーザー名と一時的なパスワードを使用して、先ほど作成した Azure AD ユーザーとして Azure にログインしてもらいます。 

3. プロンプトが表示されたら、管理者にパスワードの変更を依頼します。

## <a name="configuring-azure-automation-to-use-the-azure-ad-user-to-manage-the-azure-subscription"></a>Azure AD ユーザーを使用して Azure サブスクリプションを管理するように Azure Automation を構成する

Azure Automation と Azure AD が通信するには、Azure の Azure AD との接続に関連付けられている資格情報を取得する必要があります。 このような資格情報の例としては、テナント ID、サブスクリプション ID などがあります。 Azure と Azure AD 間の接続の詳細については、「[Azure Active Directory に組織を接続する](https://docs.microsoft.com/azure/devops/organizations/accounts/connect-organization-to-azure-ad?view=azure-devops)」を参照してください。

## <a name="creating-a-credential-asset"></a>資格情報資産の作成

Azure AD の Azure 資格情報を使用できるようになったら、Azure Automation の資格情報資産を作成して Azure AD の資格情報を安全に格納し、Runbook と Desire State Configuration (DSC) スクリプトからアクセスできるようにします。 この処理は、Azure portal または PowerShell コマンドレットで行うことができます。

### <a name="create-the-credential-asset-in-azure-portal"></a>Azure portal で資格情報資産を作成する

Azure portal を使用して、資格情報資産を作成することができます。 この操作は、Automation アカウントから **[共有リソース]** の下にある **[資格情報]** を使用して行います。 「[Azure Automation での資格情報資産](shared-resources/credentials.md)」を参照してください。

### <a name="create-the-credential-asset-with-windows-powershell"></a>Windows PowerShell を使用して資格情報資産を作成する

Windows PowerShell で新しい資格情報資産を準備するために、スクリプトでは、まず割り当てられたユーザー名とパスワードを使用して `PSCredential` オブジェクトを作成します。 次に、このスクリプトでは、このオブジェクトを使用し、[New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) コマンドレットを呼び出して資産を作成します。 または、スクリプトで [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) コマンドレットを呼び出し、ユーザーに名前とパスワードの入力を求めることができます。 「[Azure Automation での資格情報資産](shared-resources/credentials.md)」を参照してください。 

## <a name="managing-azure-resources-from-an-azure-automation-runbook"></a>Azure Automation Runbook からの Azure リソースの管理

Azure Automation Runbook から資格情報資産を使用して Azure リソースを管理できます。 Azure サブスクリプションで仮想マシンを停止および開始するために使用する資格情報資産を収集する PowerShell Runbook の例を次に示します。 この Runbook では、まず `Get-AutomationPSCredential` を使用して、Azure への認証に使用する資格情報を取得します。 次に、[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.6.1) コマンドレットを呼び出し、資格情報を使用して Azure に接続します。 このスクリプトでは、[Select-AzureSubscription](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0) コマンドレットを使用して、使用するサブスクリプションを選択します。 

```azurepowershell
Workflow Stop-Start-AzureVM 
{ 
    Param 
    (    
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureSubscriptionId, 
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureVMList="All", 
        [Parameter(Mandatory=$true)][ValidateSet("Start","Stop")] 
        [String] 
        $Action 
    ) 
     
    $credential = Get-AutomationPSCredential -Name 'AzureCredential' 
    Connect-AzAccount -Credential $credential 
    Select-AzureSubscription -SubscriptionId $AzureSubscriptionId 
 
    if($AzureVMList -ne "All") 
    { 
        $AzureVMs = $AzureVMList.Split(",") 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
    } 
    else 
    { 
        $AzureVMs = (Get-AzVM).Name 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
 
    } 
 
    foreach($AzureVM in $AzureVMsToHandle) 
    { 
        if(!(Get-AzVM | ? {$_.Name -eq $AzureVM})) 
        { 
            throw " AzureVM : [$AzureVM] - Does not exist! - Check your inputs " 
        } 
    } 
 
    if($Action -eq "Stop") 
    { 
        Write-Output "Stopping VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Stop-AzVM -Force 
        } 
    } 
    else 
    { 
        Write-Output "Starting VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Start-AzVM 
        } 
    } 
}
```  

## <a name="next-steps"></a>次のステップ

* Automation の資格情報資産については、「[Azure Automation での資格情報資産](shared-resources/credentials.md)」を参照してください。
* Automation モジュールの使用方法については、「[Azure Automation でのモジュールの管理](shared-resources/modules.md)」を参照してください。
* Azure Automation で Runbook を開始するために使用できる方法について詳しくは、「[Azure Automation での Runbook の開始](automation-starting-a-runbook.md)」を参照してください。
* PowerShell (言語リファレンス、学習モジュールを含む) の詳細については、[PowerShell ドキュメント](https://docs.microsoft.com/powershell/scripting/overview)を参照してください。