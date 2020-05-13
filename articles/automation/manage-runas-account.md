---
title: Azure Automation の実行アカウントを管理する
description: この記事では、PowerShell またはポータルを使用して、実行アカウントを管理する方法について説明します。
services: automation
ms.subservice: shared-capabilities
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: d77fc756530115ff828c79a3b444c1152ffe5c5a
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2020
ms.locfileid: "82608679"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Azure Automation の実行アカウントを管理する

Azure Automation の実行アカウントでは、Azure コマンドレットを使用して Azure のリソースを管理するための認証が提供されます。 実行アカウントを作成すると、新しいサービス プリンシパル ユーザーが Azure Active Directory (AD) に作成され、サブスクリプション レベルでこのユーザーに共同作成者ロールが割り当てられます。

## <a name="types-of-run-as-accounts"></a>実行アカウントの種類

Azure Automation では、次の 2 種類の実行アカウントが使用されます。

* Azure 実行アカウント
* Azure クラシック実行アカウント

>[!NOTE]
>Azure Cloud Solution Provider (CSP) サブスクリプションでは、Azure Resource Manager モデルのみがサポートされます。 Azure Resource Manager 以外のサービスは、プログラムでは使用できません。 CSP サブスクリプションを使用する場合、Azure クラシック実行アカウントは作成されませんが、Azure 実行アカウントは作成されます。 CSP サブスクリプションの詳細については、[CSP サブスクリプションで利用可能なサービス](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)に関するページを参照してください。

実行アカウントのサービス プリンシパルには、既定では、Azure AD を読み取るためのアクセス許可はありません。 Azure AD の読み取り、または管理を行うためのアクセス許可を追加する場合は、 **[API のアクセス許可]** の下でサービス プリンシパルにそのアクセス許可を付与する必要があります。 詳細については、「[Web API にアクセスするためのアクセス許可を追加する](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)」を参照してください。

### <a name="run-as-account"></a>実行アカウント

実行アカウントでは、[Resource Manager デプロイ モデル](../azure-resource-manager/management/deployment-models.md) リソースを管理します。 次のタスクが行われます。

* 自己署名証明書を含む Azure AD アプリケーションを作成し、このアプリケーションの Azure AD におけるサービス プリンシパル アカウントを作成します。また、現在のサブスクリプションにおける、このアカウントの共同作成者ロールが割り当てられます。 証明書の設定は、所有者や他の任意のロールに変更できます。 詳細については、「[Azure Automation におけるロールベースのアクセス制御](automation-role-based-access-control.md)」を参照してください。
  
* 指定された Automation アカウントに `AzureRunAsCertificate` という名前の Automation 証明書資産を作成します。 証明書資産には、Azure AD アプリケーションで使用される証明書の秘密キーが保持されます。
  
* 指定された Automation アカウントに `AzureRunAsConnection` という名前の Automation 接続資産を作成します。 この接続資産には、アプリケーション ID、テナント ID、サブスクリプション ID、証明書の拇印が格納されます。

### <a name="azure-classic-run-as-account"></a>Azure クラシック実行アカウント

Azure クラシック実行アカウントでは、[クラシック デプロイ モデル](../azure-resource-manager/management/deployment-models.md) リソースを管理します。 この種類のアカウントを作成または更新するには、サブスクリプションの共同管理者である必要があります。

Azure クラシック実行アカウントでは、次のタスクが行われます。

  * サブスクリプションに管理証明書を作成します。

  * 指定された Automation アカウントに `AzureClassicRunAsCertificate` という名前の Automation 証明書資産を作成します。 この証明書資産には、管理証明書によって使用される証明書の秘密キーが格納されます。

  * 指定された Automation アカウントに `AzureClassicRunAsConnection` という名前の Automation 接続資産を作成します。 この接続資産には、サブスクリプション名、サブスクリプション ID、および証明書資産名が保持されます。

>[!NOTE]
>Automation アカウントを作成するとき、Azure クラシック実行アカウントは既定では同時に作成されません。 このアカウントは、この記事の後半で説明する手順に従って個別に作成します。

## <a name="run-as-account-permissions"></a><a name="permissions"></a>実行アカウントのアクセス許可

このセクションでは、通常の実行アカウントとクラシック実行アカウントの両方に対するアクセス許可を定義します。

### <a name="permissions-to-configure-run-as-accounts"></a>実行アカウントを構成するためのアクセス許可

実行アカウントを作成または更新するには、特定の特権およびアクセス許可が必要です。 Azure Active Directory のアプリケーション管理者とサブスクリプションの所有者は、すべてのタスクを完了できます。 職務が分離されている状況について、次の表にタスク、同等のコマンドレット、必要なアクセス許可の一覧を示します。

|タスク|コマンドレット  |最小限のアクセス許可  |アクセス許可を設定する場所|
|---|---------|---------|---|
|Azure AD アプリケーションを作成する|[New-AzADApplication](https://docs.microsoft.com/powershell/module/az.resources/new-azadapplication)     | アプリケーション開発者ロール<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>[ホーム] > [Azure AD] > [アプリの登録] |
|資格情報をアプリケーションに追加します。|[New-AzADAppCredential](https://docs.microsoft.com/powershell/module/az.resources/new-azadappcredential)     | アプリケーション管理者または全体管理者<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>[ホーム] > [Azure AD] > [アプリの登録]|
|Azure AD サービス プリンシパルを作成および取得する|[New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal)     | アプリケーション管理者または全体管理者<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>[ホーム] > [Azure AD] > [アプリの登録]|
|指定されたプリンシパルの RBAC ロールを割り当てるまたは取得する|[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleAssignment)      | ユーザー アクセス管理者または所有者、あるいは次のアクセス許可がある:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [サブスクリプション](../role-based-access-control/role-assignments-portal.md)</br>[ホーム] > [サブスクリプション] > [\<サブスクリプション名\>-アクセス制御 (IAM)]|
|Automation の証明書を作成または削除する|[New-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationcertificate)     | リソース グループの共同作成者         |Automation アカウント リソース グループ|
|Automation の接続を作成または削除する|[New-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationconnection)|リソース グループの共同作成者 |Automation アカウント リソース グループ|

<sup>1</sup>[ユーザー設定] ページで Azure AD テナントの **[ユーザーはアプリケーションを登録できる]** オプションが **[はい]** に設定されている場合は、Azure AD テナントの管理者以外のユーザーが [AD アプリケーションを登録する](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)ことができます。 アプリケーション登録設定が **[いいえ]** の場合は、この操作を行うユーザーがこの表で定義されている必要があります。

サブスクリプションの Active Directory インスタンスのメンバーになっていない状態で、サブスクリプションの全体管理者ロールに追加された場合は、ゲストとして追加されます。 このような状況では、[Automation アカウントの追加] ページに `You do not have permissions to create…` 警告が表示されます。 

全体管理者ロールが割り当てられたときにサブスクリプションの Active Directory インスタンスのメンバーである場合でも、[Automation アカウントの追加] ページに `You do not have permissions to create…` 警告が表示されることがあります。 この場合は、サブスクリプションの Active Directory インスタンスからの削除を要求してから、再追加するように要求し、Active Directory で完全なユーザーになるようにすることができます。

エラー メッセージが生成される状況が解決されたことを確認するには、次のようにします。

1. Azure portal の Azure Active Directory ペインで、 **[ユーザーとグループ]** を選択します。 
2. **[すべてのユーザー]** を選択します。
3. ご自分の名前を選んでから、 **[プロファイル]** を選択します。 
4. ユーザーのプロファイルの下にある **[ユーザーの種類]** 属性の値が、確実に **[ゲスト]** には設定されていないようにします。

### <a name="permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>実行アカウントを構成するためのアクセス許可

クラシック実行アカウントを構成または更新するには、サブスクリプション レベルの共同管理者ロールが必要です。 クラシック サブスクリプション アクセス許可の詳細については、「[Azure の従来のサブスクリプション管理者](../role-based-access-control/classic-administrators.md#add-a-co-administrator)」を参照してください。

## <a name="creating-a-run-as-account-in-azure-portal"></a>Azure portal での実行アカウントの作成

以下の手順を行って、Azure portal で Azure Automation アカウントを更新します。 実行およびクラシック実行アカウントを個別に作成します。 クラシック リソースを管理する必要がない場合は、Azure 実行アカウントのみを作成できます。

1. サブスクリプション管理ロールのメンバーかつサブスクリプションの共同管理者であるアカウントを使用して、Azure ポータルにログインします。
2. **Automation アカウント**を検索して選択します。
3. [Automation アカウント] ページで、一覧から Automation アカウントを選択します。
4. 左ペインで、アカウントの設定セクションの **[実行アカウント]** を選択します。
5. 必要なアカウントに応じて、 **[Azure 実行アカウント]** または **[Azure クラシック実行アカウント]** を選択します。 
6. 関心のあるアカウントに応じて、 **[Azure 実行アカウントを追加する]** または **[Azure クラシック実行アカウントを追加する]** ペインを使用します。 概要情報を確認した後、 **[作成]** クリックします。
6. Azure によって実行アカウントが作成されている間、メニューの **[通知]** で進行状況を追跡できます。 アカウントが作成されていることを示すバナーも表示されます。 プロセスが完了するまでに数分かかることがあります。

## <a name="creating-a-run-as-account-using-powershell"></a>PowerShell を使用する実行アカウントの作成

次の一覧に、PowerShell で実行アカウントを作成するための要件を示します。 これらの要件は、両方の種類の実行アカウントに適用されます。

* Azure Resource Manager モジュール 3.4.1 以降がインストールされた Windows 10 または Windows Server 2016。 PowerShell スクリプトでは、以前のバージョンの Windows はサポートされていません。
* Azure PowerShell 1.0 以降。 PowerShell 1.0 リリースについては、[Azure PowerShell のインストールと構成方法](/powershell/azureps-cmdlets-docs)に関するページを参照してください。
* Automation アカウント。`AutomationAccountName` および `ApplicationDisplayName` パラメーターの値として参照されます。
* [実行アカウントを構成するために必要なアクセス許可](#permissions)に関する記述で一覧表示されているものに相当するアクセス許可。

PowerShell スクリプトの必須パラメーターである `SubscriptionId`および `ResourceGroupName` の値を取得するには、次の手順を行います。

1. Azure portal で、 **[Automation アカウント]** を選択します。
1. [Automation アカウント] ページで、ご自分の Automation アカウントを選択します。
1. アカウント設定セクションで、 **[プロパティ]** を選択します。
1. [プロパティ] ページの **[名前]** 、 **[サブスクリプション ID]** 、および **[リソース グループ]** の値をメモします。 これらの値は、それぞれ `AutomationAccountName`、`SubscriptionId`、`ResourceGroupName` PowerShell スクリプト パラメーターの値に対応します。

   ![Automation アカウントの [プロパティ] ページ](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>実行アカウントを作成する PowerShell スクリプト

このセクションでは、実行アカウントを作成する PowerShell スクリプトについて説明します。 このスクリプトには、いくつかの構成のサポートが含まれています。

* 自己署名証明書を使用して実行アカウントを作成する。
* 自己署名証明書を使用して実行アカウントとクラシック実行アカウントを作成する。
* エンタープライズ証明機関 (CA) によって発行された証明書を使用して実行アカウントとクラシック実行アカウントを作成する。
* Azure Government クラウドで自己署名証明書を使用して実行アカウントとクラシック実行アカウントを作成する。

スクリプトでは、複数の Azure Resource Manager コマンドレットを使用してリソースを作成します。 コマンドレットおよび必要なアクセス許可については、「[実行アカウントを構成するためのアクセス許可](#permissions-to-configure-run-as-accounts)」を参照してください。

ファイル名の **New-RunAsAccount.ps1** を使用して、スクリプトをご自分のコンピューターに保存します。

```powershell
#Requires -RunAsAdministrator
Param (
    [Parameter(Mandatory = $true)]
    [String] $ResourceGroup,

    [Parameter(Mandatory = $true)]
    [String] $AutomationAccountName,

    [Parameter(Mandatory = $true)]
    [String] $ApplicationDisplayName,

    [Parameter(Mandatory = $true)]
    [String] $SubscriptionId,

    [Parameter(Mandatory = $true)]
    [Boolean] $CreateClassicRunAsAccount,

    [Parameter(Mandatory = $true)]
    [String] $SelfSignedCertPlainPassword,

    [Parameter(Mandatory = $false)]
    [string] $EnterpriseCertPathForRunAsAccount,

    [Parameter(Mandatory = $false)]
    [String] $EnterpriseCertPlainPasswordForRunAsAccount,

    [Parameter(Mandatory = $false)]
    [String] $EnterpriseCertPathForClassicRunAsAccount,

    [Parameter(Mandatory = $false)]
    [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

    [Parameter(Mandatory = $false)]
    [ValidateSet("AzureCloud", "AzureUSGovernment")]
    [string]$EnvironmentName = "AzureCloud",

    [Parameter(Mandatory = $false)]
    [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
)

function CreateSelfSignedCertificate([string] $certificateName, [string] $selfSignedCertPlainPassword,
    [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
    $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
        -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
        -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired) -HashAlgorithm SHA256

    $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
    Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
    Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
}

function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {
    $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
    $keyId = (New-Guid).Guid

    # Create an Azure AD application, AD App Credential, AD ServicePrincipal

    # Requires Application Developer Role, but works with Application administrator or GLOBAL ADMIN
    $Application = New-AzADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId)
    # Requires Application administrator or GLOBAL ADMIN
    $ApplicationCredential = New-AzADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $PfxCert.NotBefore -EndDate $PfxCert.NotAfter
    # Requires Application administrator or GLOBAL ADMIN
    $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $Application.ApplicationId
    $GetServicePrincipal = Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id

    # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
    Sleep -s 15
    # Requires User Access Administrator or Owner.
    $NewRole = New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
    $Retries = 0;
    While ($NewRole -eq $null -and $Retries -le 6) {
        Sleep -s 10
        New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
        $NewRole = Get-AzRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries++;
    }
    return $Application.ApplicationId.ToString();
}

function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
    $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force
    Remove-AzAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
    New-AzAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
}

function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
    Remove-AzAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
    New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
}

Import-Module AzureRm.Profile
Import-Module AzureRm.Resources

$AureRmProfileVersion = (Get-Module AzureRm.Profile).Version
if (!(($AzureRmProfileVersion.Major -ge 3 -and $AzureRmProfileVersion.Minor -ge 4) -or ($AzureRmProfileVersion.Major -gt 3))) {
    Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
    return
}

# To use the new Az modules to create your Run As accounts, please uncomment the following lines and ensure you comment out the previous 8 lines that import the AzureRM modules to avoid any issues. To learn about about using Az modules in your Automation account see https://docs.microsoft.com/azure/automation/az-modules.

# Import-Module Az.Automation
# Enable-AzureRmAlias


Connect-AzAccount -Environment $EnvironmentName
$Subscription = Get-AzSubscription -SubscriptionId $SubscriptionId

# Create a Run As account by using a service principal
$CertifcateAssetName = "AzureRunAsCertificate"
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionTypeName = "AzureServicePrincipal"

if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
    $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
    $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
}
else {
    $CertificateName = $AutomationAccountName + $CertifcateAssetName
    $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
    $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
    $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
    CreateSelfSignedCertificate $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
}

# Create a service principal
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
$ApplicationId = CreateServicePrincipal $PfxCert $ApplicationDisplayName

# Create the Automation certificate asset
CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

# Populate the ConnectionFieldValues
$SubscriptionInfo = Get-AzSubscription -SubscriptionId $SubscriptionId
$TenantID = $SubscriptionInfo | Select TenantId -First 1
$Thumbprint = $PfxCert.Thumbprint
$ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}

# Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

if ($CreateClassicRunAsAccount) {
    # Create a Run As account by using a service principal
    $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
    $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
    $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
    $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
    "Log in to the Microsoft Azure portal (https://portal.azure.com) and select Subscriptions -> Management Certificates." + [Environment]::NewLine +
    "Then click Upload and upload the .cer format of #CERT#"

    if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
        $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
        $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
        $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
    }
    else {
        $ClassicRunAsAccountCertificateName = $AutomationAccountName + $ClassicRunAsAccountCertifcateAssetName
        $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
        $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
        $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
        $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
        CreateSelfSignedCertificate $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
    }
    
    # Create the Automation certificate asset
    CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

    # Populate the ConnectionFieldValues
    $SubscriptionName = $subscription.Subscription.Name
    $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

    # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName   $ClassicRunAsAccountConnectionFieldValues

    Write-Host -ForegroundColor red       $UploadMessage
}
```

>[!NOTE]
>`Add-AzAccount` と `Add-AzureRMAccount` は [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) の別名です。 これらのコマンドレットを使用するか、Automation アカウントの[モジュール最新バージョンに更新](automation-update-azure-modules.md)することができます。 Automation アカウントを作成したばかりのときでも、モジュールを更新する必要がある場合があります。

### <a name="execute-the-powershell-script"></a>PowerShell スクリプトを実行する

1. コンピューターの**スタート**画面から、昇格されたユーザー権限で **Windows PowerShell** を起動します。
1. 管理者特権のコマンド ライン シェルから、スクリプトが含まれているフォルダーに移動します。
1. 必要な構成のパラメーター値を使用して、スクリプトを実行します。
1. クラシック実行アカウントを作成する場合は、スクリプトの実行後に、Automation アカウントが作成されているサブスクリプションの管理ストアに公開証明書 ( **.cer** ファイル名拡張子) をアップロードします。

スクリプトの実行後に、Azure での認証が求められます。 サブスクリプション管理者ロールのメンバーかつサブスクリプションの共同管理者であるアカウントを使用して、サインインします。

#### <a name="create-a-run-as-account-by-using-a-self-signed-certificate"></a>自己署名証明書を使用して実行アカウントを作成する

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate"></a>自己署名証明書を使用して実行アカウントとクラシック実行アカウントを作成する

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-an-enterprise-certificate"></a>エンタープライズ証明書を使用して実行アカウントとクラシック実行アカウントを作成する

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
```

エンタープライズ公開証明書 ( **.cer** ファイル) を使ってクラシック実行アカウントを作成した場合は、この証明書を使用します。 [Azure portal への管理 API 証明書のアップロード](../azure-api-management-certs.md)に関するページを参照してください。

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate-in-the-azure-government-cloud"></a>Azure Government クラウドで自己署名証明書を使用して実行アカウントとクラシック実行アカウントを作成する

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
```

自己署名公開証明書 ( **.cer** ファイル) を使用してクラシック実行アカウントを作成した場合、スクリプトによってそれが作成され、コンピューター上の一時ファイル フォルダーに保存されます。 これは、PowerShell セッションの実行に使用した、ユーザープロファイル `%USERPROFILE%\AppData\Local\Temp` にあります。

## <a name="deleting-a-run-as-or-classic-run-as-account"></a>実行またはクラシック実行アカウントの削除

このセクションでは、実行またはクラシック実行アカウントを削除する方法について説明します。 この操作を実行するとき、Automation アカウントが保持されます。 アカウントを削除した後、Azure portal でそれを再作成することができます。

1. Azure Portal で Automation アカウントを開きます。

2. 左ペインで、アカウントの設定セクションの **[実行アカウント]** を選択します。

3. 実行アカウントのプロパティ ページで、削除する実行アカウントまたはクラシック実行アカウントを選択します。 

4. 選択したアカウントの [プロパティ] ペインで、 **[削除]** をクリックします。

   ![Azure 実行アカウントを削除する](media/manage-runas-account/automation-account-delete-runas.png)

5. アカウントが削除されている間、メニューの **[通知]** で進行状況を追跡できます。

6. アカウントが削除された後、実行アカウントのプロパティ ページでそれを再作成することができます。その場合、作成オプションの **[Azure 実行アカウント]** を選択します。

   ![Automation 実行アカウントの再作成](media/manage-runas-account/automation-account-create-runas.png)

## <a name="renewing-a-self-signed-certificate"></a><a name="cert-renewal"></a>自己署名証明書の更新

実行アカウント用に作成した自己署名証明書は、作成日から 1 年後に有効期限が切れます。 実行アカウントの有効期限が切れる前のある時点で、証明書を更新する必要があります。 有効期限が切れる前にいつでも更新することができます。 

自己署名証明書を更新するときに、現在有効な証明書は保持されます。これにより、キューに登録されているかアクティブに実行されていて、実行アカウントで認証される Runbook は確実に悪影響を受けることがなくなります。 証明書は、有効期限日を迎えるまで存在し続けます。

>[!NOTE]
>実行アカウントが侵害されていると思われる場合は、自己署名証明書を削除してから再作成することができます。

>[!NOTE]
>エンタープライズ証明機関によって発行された証明書を使用するように実行アカウントを構成している場合に、自己署名証明書を更新するオプションを使用すると、エンタープライズ証明書は自己署名証明書に置き換えられます。

自己署名証明書を更新するには、次の手順を使用します。

1. Azure Portal で Automation アカウントを開きます。

1. アカウントの設定セクションで、 **[実行アカウント]** を選択します。

    ![Automation アカウントのプロパティ ウィンドウ](media/manage-runas-account/automation-account-properties-pane.png)

1. 実行アカウントのプロパティ ページで、証明書を更新する実行アカウントまたはクラシック実行アカウントを選択します。

1. 選択したアカウントのプロパティ ペインで、 **[証明書の更新]** をクリックします。

    ![実行アカウントの証明書を書き換える](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. 証明書が書き換えられている間、メニューの **[通知]** で進行状況を追跡できます。

## <a name="setting-up-automatic-certificate-renewal-with-an-automation-runbook"></a><a name="auto-cert-renewal"></a>Automation Runbook を使用した証明書の自動更新の設定

証明書を自動的に更新するには、Automation Runbook を使用します。 [GitHub](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) のこのスクリプトを使用すると、Automation アカウントでこの機能が有効になります。

>[!NOTE]
>スクリプトを実行するには、Azure AD の全体管理者または社内管理者である必要があります。

このスクリプトでは、実行アカウント証明書を更新する週単位のスケジュールを作成します。 **Update-AutomationRunAsCredential** Runbook を Automation アカウントに追加します。 Runbook のコードは、GitHub のスクリプト [Update-AutomationRunAsCredential.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1) で確認できます。 ファイルで PowerShell コードを使用して、必要に応じて、証明書を手動で更新することができます。

更新プロセスをすぐにテストするには、次の手順を使用します。

1. **AutomationRunAsCredential** Runbook を編集し、122 行目の **Exit (1)** コマンドの前に、コメント文字 (#) を配置します。

   ```powershell
   #Exit(1)
   ```

2. Runbook を発行します。
3. Runbook を開始します。
4. 次のコードを使用して、正常に更新されたことを確認します。

   ```powershell
   (Get-AzAutomationCertificate -AutomationAccountName TestAA
                                -Name AzureRunAsCertificate
                                -ResourceGroupName TestAutomation).ExpiryTime.DateTime
   ```
    出力:

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. テストの後、Runbook を編集し、手順 1. で追加したコメント文字を削除します。
6. Runbook を発行します。

## <a name="limiting-run-as-account-permissions"></a><a name="limiting-run-as-account-permissions"></a>実行アカウントのアクセス許可の制限

Azure のリソースに対する Automation のターゲット設定を制御するために、[Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) スクリプトを実行できます。 このスクリプトでは、カスタム ロールの定義を作成して使用するために、既存の実行アカウント サービス プリンシパルを変更します。 このロールには、[Key Vault](https://docs.microsoft.com/azure/key-vault/) を除くすべてのリソースへのアクセス許可があります。

>[!IMPORTANT]
>**Update-AutomationRunAsAccountRoleAssignments.ps1** スクリプトを実行した後、実行アカウントを使用して Key Vault にアクセスする Runbook が動作しなくなります。 スクリプトを実行する前に、アカウント内の Runbook で Azure Key Vault の呼び出しを確認する必要があります。 Azure Automation Runbook から Key Vault へのアクセスを有効にするには、[実行アカウントを Key Vault のアクセス許可に追加する](#add-permissions-to-key-vault)必要があります。

実行サービス プリンシパルで実行できる内容をさらに制限する必要がある場合は、カスタム ロール定義の `NotActions` 要素に他のリソースの種類を追加できます。 次の例では、`Microsoft.Compute/*` へのアクセスが制限されます。 このリソースの種類をロール定義の `NotActions` に追加すると、そのロールではどのコンピューティング リソースにもアクセスできなくなります。 ロール定義の詳細については、「[Azure リソースのロール定義の概要](../role-based-access-control/role-definitions.md)」を参照してください。

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

実行アカウントで使用されるサービス プリンシパルが、共同作成者とカスタムのどちらのロールの定義にあるかを確認できます。 

1. Automation アカウントに移動し、アカウントの設定セクションで **[実行アカウント]** を選択します。
2. **[Azure 実行アカウント]** を選択します。 
3. **[ロール]** を選択し、使用されているロールの定義を見つけます。

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

また、複数のサブスクリプションまたは Automation アカウントの実行アカウントで使用されるロールの定義を確認することができます。 これを行うには、PowerShell ギャラリーの [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) スクリプトを使用します。

### <a name="add-permissions-to-key-vault"></a>Key Vault へのアクセス許可を追加する

Key Vault と実行アカウントのサービス プリンシパルでカスタム ロールの定義が使用されているかどうかを、Azure Automation で確認できるようにすることができます。 次の手順が必要です。

* Key Vault にアクセス許可を付与します。
* アクセス ポリシーを設定します。

PowerShell ギャラリーの [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) スクリプトを使用して、実行アカウントに Key Vault へのアクセス許可を付与することができます。 Key Vault へのアクセス許可の設定について詳しくは、[アプリケーションに対する Key Vault へのアクセス許可の付与](../key-vault/general/group-permissions-for-apps.md)に関するページを参照してください。

## <a name="resolving-misconfiguration-issues-for-run-as-accounts"></a>実行アカウントの間違った構成に関する問題の解決

実行またはクラシック実行アカウントに必要な構成項目の一部が削除されたか、初期セットアップ中に正しく作成されなかった可能性があります。 構成が間違っている可能性のあるインスタンスは、次のとおりです。

* 証明書資産
* 接続資産
* 共同作成者ロールから削除された実行アカウント
* Azure AD のサービス プリンシパルまたはアプリケーション

このような構成が間違っているインスタンスについては、Automation アカウントによって変更が検出され、そのアカウントの実行アカウントのプロパティ ペインに [*Incomplete*]\(不完全\) の状態が表示されます。

![実行アカウントの構成が不完全な状態](media/manage-runas-account/automation-account-runas-incomplete-config.png)

実行アカウントを選択すると、アカウントのプロパティ ペインに次のエラー メッセージが表示されます。

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

こうした実行アカウントの問題は、アカウントを削除してから再作成すればすぐに解決できます。

## <a name="next-steps"></a>次のステップ

* サービス プリンシパルの詳細については、[アプリケーション オブジェクトとサービス プリンシパル オブジェクト](../active-directory/develop/app-objects-and-service-principals.md)に関するページを参照してください。
* 証明書と Azure サービスの詳細については、「[Azure Cloud Services の証明書の概要](../cloud-services/cloud-services-certs-create.md)」を参照してください。
