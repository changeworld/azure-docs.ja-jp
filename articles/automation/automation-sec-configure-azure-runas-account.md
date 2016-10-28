<properties
    pageTitle="Azure 実行アカウントの構成 | Microsoft Azure"
    description="Azure Automation のセキュリティ プリンシパル認証の作成、テスト、使用例をわかりやすく説明しています。"
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""
	keywords="サービス プリンシパル名, setspn, Azure の認証"/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/17/2016"
    ms.author="magoedte"/>

# Azure 実行アカウントを使用した Runbook の認証

このトピックでは、Azure ポータルから実行アカウント機能を使用して Automation アカウントを構成し、Azure Resource Manager または Azure サービス管理のリソースを管理するための Runbook を認証する方法について説明します。

Azure ポータルで新しい Automation アカウントを作成すると、次のものが自動的に作成されます。

- Azure Active Directory の新しいサービス プリンシパルと証明書を作成し、ロールベースのアクセス制御 (RBAC) の Contributor ロールを割り当てる実行アカウント。この Contributor ロールは、Runbook を使用した Resource Manager のリソースの管理に使用されます。
- クラシック実行アカウント。Azure サービス管理リソースまたはクラシック リソースを Runbook で管理する際に使用する管理証明書をアップロードすることで作成されます。

これによって必要な作業が単純化され、オートメーションのニーズを満たす Runbook をすぐに作成し、デプロイすることができます。

実行アカウントとクラシック実行アカウントを使用すると、次のことができます。

- Azure ポータルでの Runbook を使用した Azure Resource Manager リソースまたは Azure サービス管理リソースの管理に、Azure を使用した標準的な認証方法を取り入れる。
- Azure アラート内で構成されるグローバル Runbook の使用を自動化する。


>[AZURE.NOTE] Azure [アラート統合機能](../azure-portal/insights-receive-alert-notifications.md)と Automation Global Runbook の連携には、実行アカウントとクラシック実行アカウントを使って構成された Automation アカウントが必要です。既に実行アカウントとクラシック実行アカウントが定義されている Automation アカウントを選んで利用できるほか、Automation アカウントを新たに作成することもできます。

Azure ポータルからの Automation アカウントの作成方法を紹介すると共に、PowerShell を使用して Automation アカウントを更新する方法と、Runbook 内で認証を行う方法について説明します。

次に進む前に、いくつかの注意事項と考慮事項があります。

1. これは、クラシック デプロイメント モデルまたは Resource Manager デプロイメント モデルで既に作成した既存の Automation アカウントには影響しません。
2. これは、Azure ポータルで作成した Automation アカウントに対してのみ有効です。クラシック ポータルからアカウントを作成しようとしても、実行アカウントの構成はレプリケートされません。
3. 現時点で、クラシック リソースを管理するために以前作成された Runbook と資産 (スケジュールや変数など) を所有しており、かつその Runbook を新しいクラシック実行アカウントで認証する場合は、Runbook を新しい Automation アカウントに移行するか、次の PowerShell スクリプトを使用して既存のアカウントを更新する必要があります。
4. 新しい実行アカウントまたはクラシック実行 Automation アカウントを使って認証を行うには、次のサンプル コードを使用して既存の Runbook を変更する必要があります。実行アカウントは、証明書ベースのサービス プリンシパルを使用した Resource Manager リソースに対する認証用であり、クラシック実行アカウントは、管理証明書を使用したサービス管理リソースに対する認証用であることに**注意してください**。


## Azure ポータルから新しい Automation アカウントを作成する

このセクションでは、以下の手順に従って、Azure ポータルから新しい Azure Automation アカウントを作成します。実行アカウントとクラシック実行アカウントの両方を作成します。

>[AZURE.NOTE] この手順を実施するユーザーは、"*必ず*" サブスクリプション管理ロールのメンバーかつサブスクリプションの共同管理者 (ユーザーにサブスクリプションへのアクセスを付与できるユーザー) である必要があります。さらにこのユーザーは、そのサブスクリプションの既定の Active Directory にユーザーとして追加される必要があります。アカウントを特権ロールに割り当てる必要はありません。

1. サブスクリプション管理ロールのメンバーかつサブスクリプションの共同管理者であるアカウントを使用して、Azure ポータルにログインします。
2. **[Automation アカウント]** を選択します。
3. [Automation アカウント] ブレードで **[追加]** をクリックします。<br>![Add Automation Account](media/automation-sec-configure-azure-runas-account/create-automation-account-properties-b.png)

    >[AZURE.NOTE] お使いのアカウントが、サブスクリプション管理ロールのメンバーかつサブスクリプションの共同管理者ではない場合、**[Automation アカウントの追加]** ブレードに次の警告が表示されます。<br>![Add Automation Account Warning](media/automation-sec-configure-azure-runas-account/create-account-without-perms.png)

4. **[Automation アカウントの追加]** ブレードの **[名前]** ボックスに、新しい Automation アカウントの名前を入力します。
5. 複数のサブスクリプションがある場合は、新しいアカウントに対して 1 つのサブスクリプションを指定し、新規または既存の**リソース グループ**と、Azure データ センターの**場所**を指定します。
6. **[Azure 実行アカウントの作成]** オプションで **[はい]** が選択されていることを確認し、**[作成]** ボタンをクリックします。

    >[AZURE.NOTE] 実行アカウントを作成しなかった場合 (先ほどのオプションで **[いいえ]** を選択した場合)、**[Automation アカウントの追加]** ブレードに警告メッセージが表示されます。Azure ポータルでアカウントを作成している間は、クラシック サブスクリプションまたは Resource Manager サブスクリプションのディレクトリ サービスには対応する認証 ID が割り当てられず、サブスクリプション内のリソースにアクセスすることはできません。そのため、このアカウントを参照する Runbook は認証を通過できず、これらのデプロイメント モデルのリソースに対するタスクを実行することができません。
    
    >![Add Automation Account Warning](media/automation-sec-configure-azure-runas-account/create-account-decline-create-runas-msg.png)<br> サービス プリンシパルが作成されていない場合、Contributor ロールは割り当てられません。


7. Azure によって Automation アカウントが作成されている間、メニューの **[通知]** で進行状況を追跡できます。

### 含まれるリソース

Automation アカウントが正常に作成されると、いくつかのリソースが自動的に作成されます。次の表は、実行アカウントのリソースの一覧です。<br>

リソース|説明 
--------|-----------
AzureAutomationTutorial Runbook|実行アカウントを使用した認証の方法と、すべての Resource Manager リソースの取得方法を示す、サンプルの PowerShell Runbook。
AzureAutomationTutorialScript Runbook|実行アカウントを使用した認証の方法と、すべての Resource Manager リソースの取得方法を示す、サンプルの PowerShell Runbook。 
AzureRunAsCertificate|Automation アカウントの作成時または既存のアカウント用に下の PowerShell スクリプトを使用した場合に自動的に作成される、証明書資産。これにより、Azure を使用して認証を行い、Runbook から Azure Resource Manager リソースを管理できるようになります。この証明書には、1 年の有効期間があります。 
AzureRunAsConnection|Automation アカウントの作成時または既存のアカウント用に下の PowerShell スクリプトを使用した場合に自動的に作成される、接続資産。

次の表は、クラシック実行アカウントのリソースの一覧です。<br>

リソース|説明 
--------|-----------
AzureClassicAutomationTutorial Runbook|クラシック実行アカウント (証明書) を使用してサブスクリプション内のすべてのクラシック VM を取得し、VM の名前と状態を出力するサンプルの Runbook。
AzureClassicAutomationTutorial Script Runbook|クラシック実行アカウント (証明書) を使用してサブスクリプション内のすべてのクラシック VM を取得し、VM の名前と状態を出力するサンプルの Runbook。
AzureClassicRunAsCertificate|Runbook から Azure のクラシック リソースを管理できるように、Azure を使用した認証に使用される、自動的に作成される証明書資産。この証明書には、1 年の有効期間があります。 
AzureClassicRunAsConnection|Runbook から Azure のクラシック リソースを管理できるように、Azure を使用した認証に使用される、自動的に作成される接続資産。  

## 実行アカウントの認証を検証する

次に、新しい実行アカウントを使用して正しく認証できることを確認するために小さなテストを実施します。

1. 先ほど作成した Automation アカウントを Azure ポータルで開きます。
2. **[Runbook]** タイルをクリックして、Runbook の一覧を開きます。
3. **AzureAutomationTutorialScript** Runbook を選択し、**[開始]** をクリックして、Runbook を開始します。Runbook を開始することを確認するプロンプトが表示されます。
4. [Runbook ジョブ](automation-runbook-execution.md)が作成されると、[ジョブ] ブレードが表示され、ジョブの状態が **[ジョブの概要]** タイルに表示されます。
5. 最初のジョブの状態は "*キュー登録済み*" であり、クラウドの Runbook ワーカーが使用できるようになるのを待っていることを示します。その後、ワーカーがジョブを要求すると*開始中*になり、Runbook が実際に実行を開始すると*実行中*になります。
6. Runbook ジョブが完了すると、状態は **[完了]** と表示されます。<br> ![Security Principal Runbook Test](media/automation-sec-configure-azure-runas-account/job-summary-automationtutorialscript.png)<br>
7. Runbook の詳細な結果を表示するには、**[出力]** タイルをクリックします。
8. **[出力]** ブレードに、正常に認証され、リソース グループ内で使用可能なすべてのリソースの一覧が返されたことが示されます。
9. **[出力]** ブレードを閉じ、**[ジョブの概要]** ブレードに戻ります。
13. **[ジョブの概要]** と、対応する **[AzureAutomationTutorialScript]** Runbook ブレードを閉じます。

## クラシック実行認証を検証する

次に、新しいクラシック実行アカウントを使用して正しく認証できることを確認するために小さなテストを実施します。

1. 先ほど作成した Automation アカウントを Azure ポータルで開きます。
2. **[Runbook]** タイルをクリックして、Runbook の一覧を開きます。
3. **AzureClassicAutomationTutorialScript** Runbook を選択し、**[開始]** をクリックして、Runbook を開始します。Runbook を開始することを確認するプロンプトが表示されます。
4. [Runbook ジョブ](automation-runbook-execution.md)が作成されると、[ジョブ] ブレードが表示され、ジョブの状態が **[ジョブの概要]** タイルに表示されます。
5. 最初のジョブの状態は "*キュー登録済み*" であり、クラウドの Runbook ワーカーが使用できるようになるのを待っていることを示します。その後、ワーカーがジョブを要求すると*開始中*になり、Runbook が実際に実行を開始すると*実行中*になります。
6. Runbook ジョブが完了すると、状態は **[完了]** と表示されます。<br> ![Security Principal Runbook Test](media/automation-sec-configure-azure-runas-account/job-summary-automationclassictutorialscript.png)<br>
7. Runbook の詳細な結果を表示するには、**[出力]** タイルをクリックします。
8. **[出力]** ブレードに、正常に認証され、サブスクリプション内のすべてのクラシック VM の一覧が返されたことが示されます。
9. **[出力]** ブレードを閉じ、**[ジョブの概要]** ブレードに戻ります。
13. **[ジョブの概要]** と、対応する **AzureClassicAutomationTutorialScript** Runbook ブレードを閉じます。

## PowerShell を使用して Automation アカウントを更新する

次に示す場合に、PowerShell を使用して既存の Automation アカウントを更新できます。

1. Automation アカウントは作成済みであるものの、実行アカウントの作成を拒否した場合
2. Resource Manager リソースを管理するための Automation アカウントを既に所有しており、それを更新して Runbook 認証用の実行アカウントを含める場合
2. クラシック リソースを管理するための Automation アカウントを既に所有しており、それを、新しいアカウントを作成する代わりにクラシック実行アカウントを使用するように更新し、Runbook と資産をそのアカウントに移行する場合

以降の手順に進む前に、次のことを確認してください。

1. Windows 7 を実行している場合は、[Windows Management Framework (WMF) 4.0](https://www.microsoft.com/download/details.aspx?id=40855) をダウンロードしてインストール済みであること。Windows Server 2012 R2、Windows Server 2012、Windows 2008 R2、Windows 8.1、および Windows 7 SP1 を実行している場合は、[Windows Management Framework 5.0](https://www.microsoft.com/download/details.aspx?id=50395) をインストールできます。
2. Azure PowerShell 1.0このリリースとそのインストール方法については、「[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」を参照してください。
3. Automation アカウントが作成済みであること。このアカウントは、以下の両方のスクリプトの –AutomationAccountName パラメーターと -ApplicationDisplayName パラメーターの値として参照されます。

スクリプトの必須パラメーターである *SubscriptionID*、*ResourceGroup*、*AutomationAccountName* の値を取得するには、Azure ポータルの **[Automation アカウント]** ブレードからお使いの Automation アカウントを選択し、**[すべての設定]** を選択します。**[すべての設定]** ブレードで、**[アカウント設定]** の **[プロパティ]** を選択します。**[プロパティ]** ブレードで、値をメモすることができます。<br> ![Automation Account properties](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)

### 実行アカウント用の PowerShell スクリプトを作成する

下の PowerShell スクリプトで構成の対象となる要素は次のとおりです。

- Azure AD アプリケーション。自己署名証明書で認証され、このアプリケーションの Azure AD におけるサービス プリンシパル アカウントを作成します。現在のサブスクリプションで、このアカウントの Contributor ロールが割り当てられます (これを所有者など他の任意のロールに変更できます)。詳細については、記事「[Azure Automation におけるロールベースのアクセス制御](../automation/automation-role-based-access-control.md)」を参照してください。
- Automation 証明書資産。指定された Automation アカウントに **AzureRunAsCertificate** という名前で存在し、サービス プリンシパルで使用される証明書が格納されます。
- Automation 接続資産。指定された Automation アカウントに **AzureRunAsConnection** という名前で存在し、アプリケーション ID、テナント ID、サブスクリプション ID、証明書の拇印が格納されます。

次の手順では、スクリプトを実行する手順を説明します。

1. ご使用のコンピューターに次のスクリプトを保存します。この例では、**New-AzureServicePrincipal.ps1** というファイル名で保存します。

        #Requires -RunAsAdministrator
        Param (
        [Parameter(Mandatory=$true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory=$true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory=$true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory=$true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory=$true)]
        [String] $CertPlainPassword,

        [Parameter(Mandatory=$false)]
        [int] $NoOfMonthsUntilExpired = 12
        )

        Login-AzureRmAccount
        Import-Module AzureRM.Resources
        Select-AzureRmSubscription -SubscriptionId $SubscriptionId

        $CurrentDate = Get-Date
        $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
        $KeyId = (New-Guid).Guid
        $CertPath = Join-Path $env:TEMP ($ApplicationDisplayName + ".pfx")

        $Cert = New-SelfSignedCertificate -DnsName $ApplicationDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"

        $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose

        $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate -ArgumentList @($CertPath, $CertPlainPassword)
        $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

        $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $KeyCredential.StartDate = $CurrentDate
        $KeyCredential.EndDate= $EndDate
        $KeyCredential.KeyId = $KeyId
        $KeyCredential.Type = "AsymmetricX509Cert"
        $KeyCredential.Usage = "Verify"
        $KeyCredential.Value = $KeyValue

        # Use Key credentials
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $ApplicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $keyCredential

        New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId | Write-Verbose
        Get-AzureRmADServicePrincipal | Where {$_.ApplicationId -eq $Application.ApplicationId} | Write-Verbose

        $NewRole = $null
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6)
        {
           # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
           Sleep 5
           New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
           Sleep 10
           $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
           $Retries++;
        } 

        # Get the tenant id for this subscription
        $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
        $TenantID = $SubscriptionInfo | Select TenantId -First 1

        # Create the automation resources
        New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name AzureRunAsCertificate -Password $CertPassword -Exportable | write-verbose

        # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        $ConnectionAssetName = "AzureRunAsConnection"
        Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
        $ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues

2. コンピューターの**スタート**画面から、昇格されたユーザー権限で **Windows PowerShell** を起動します。
3. 昇格された PowerShell コマンドライン シェルから、手順 1. で作成したスクリプトが格納されているフォルダーに移動してスクリプトを実行します。*–ResourceGroup*、*-AutomationAccountName*、*-ApplicationDisplayName*、*-SubscriptionId*、*-CertPlainPassword* の各パラメーターの値は適宜変更してください。<br>

    >[AZURE.NOTE] スクリプトの実行後、Azure での認証が求められます。サブスクリプション管理ロールのメンバーかつサブスクリプションの共同管理者であるアカウントを使用してログインする必要があります。
    
        .\New-AzureServicePrincipal.ps1 -ResourceGroup <ResourceGroupName> 
        -AutomationAccountName <NameofAutomationAccount> `
        -ApplicationDisplayName <DisplayNameofAutomationAccount> `
        -SubscriptionId <SubscriptionId> `
        -CertPlainPassword "<StrongPassword>"  
<br>

スクリプトが正常に完了したら、次の[サンプル コード](#sample-code-to-authenticate-with-resource-manager-resources)を参照し、Resource Manager リソースでの認証と、資格情報の構成の検証を行います。

### クラシック実行アカウント用の PowerShell スクリプトを作成する

下の PowerShell スクリプトで構成の対象となる要素は次のとおりです。

- Automation 証明書資産。指定された Automation アカウントに **AzureClassicRunAsCertificate** という名前で存在し、Runbook の認証に使用される証明書が格納されます。
- Automation 接続資産。指定された Automation アカウントに **AzureClassicRunAsConnection** という名前で存在し、サブスクリプション名、サブスクリプション ID、証明書資産名が格納されます。

このスクリプトは自己署名管理証明書を作成し、コンピューターの一時ファイル フォルダーに保存します。このフォルダーは、PowerShell セッションの実行に使用されるユーザー プロファイル内に存在します (*%USERPROFILE%\\AppData\\Local\\Temp*)。スクリプトの実行後に、Automation アカウントが作成されたサブスクリプションの管理ストアに Azure 管理証明書をアップロードする必要があります。次の手順では、スクリプトを実行して証明書をアップロードする手順を説明します。

1. ご使用のコンピューターに次のスクリプトを保存します。この例では、**New-AzureClassicRunAsAccount.ps1** というファイル名で保存します。

        #Requires -RunAsAdministrator
        Param (
        [Parameter(Mandatory=$true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory=$true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory=$true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory=$true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory=$true)]
        [String] $CertPlainPassword,

        [Parameter(Mandatory=$false)]
        [int] $NoOfMonthsUntilExpired = 12
        )

        Login-AzureRmAccount
        Import-Module AzureRM.Resources
        $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId
        $SubscriptionName = $subscription.Subscription.SubscriptionName

        $CurrentDate = Get-Date
        $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
        $KeyId = (New-Guid).Guid
        $CertPath = Join-Path $env:TEMP ($ApplicationDisplayName + ".pfx")
        $CertPathCer = Join-Path $env:TEMP ($ApplicationDisplayName + ".cer")

        $Cert = New-SelfSignedCertificate -DnsName $ApplicationDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"

        $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my" + $Cert.Thumbprint) -FilePath $CertPathCer -Type CERT | Write-Verbose

        # Create the automation resources
        $ClassicCertificateAssetName = "AzureClassicRunAsCertificate"
        New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name $ClassicCertificateAssetName  -Password $CertPassword -Exportable | write-verbose

        # Create a Automation connection asset named AzureClassicRunAsConnection in the Automation account. This connection uses the ClassicCertificateAssetName.
        $ConnectionAssetName = "AzureClassicRunAsConnection"
        Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
        $ConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicCertificateAssetName}
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureClassicCertificate -ConnectionFieldValues $ConnectionFieldValues 

        Write-Host -ForegroundColor red "Please upload the cert $CertPathCer to the Management store by following the steps below."
        Write-Host -ForegroundColor red "Log in to the Microsoft Azure Management portal (https://manage.windowsazure.com) and select Settings -> Management Certificates."
        Write-Host -ForegroundColor red "Then click Upload and upload the certificate $CertPathCer"

2. コンピューターの**スタート**画面から、昇格されたユーザー権限で **Windows PowerShell** を起動します。
3. 昇格された PowerShell コマンドライン シェルから、手順 1. で作成したスクリプトが格納されているフォルダーに移動してスクリプトを実行します。*–ResourceGroup*、*-AutomationAccountName*、*-ApplicationDisplayName*、*-SubscriptionId*、*-CertPlainPassword* の各パラメーターの値は適宜変更してください。<br>

    >[AZURE.NOTE] スクリプトの実行後、Azure での認証が求められます。サブスクリプション管理ロールのメンバーかつサブスクリプションの共同管理者であるアカウントを使用してログインする必要があります。
   
        .\New-AzureClassicRunAsAccount.ps1 -ResourceGroup <ResourceGroupName> 
        -AutomationAccountName <NameofAutomationAccount> `
        -ApplicationDisplayName <DisplayNameofAutomationAccount> `
        -SubscriptionId <SubscriptionId> `
        -CertPlainPassword "<StrongPassword>" 

スクリプトが正常に完了したら、ユーザー プロファイルの**一時**フォルダーに作成された証明書をコピーする必要があります。Azure クラシック ポータルに[管理 API 証明書をアップロード](../azure-api-management-certs.md)する手順を実施した後、[サンプル コード](#sample-code-to-authenticate-with-service-management-resources)を参照し、サービス管理リソースで資格情報の構成を検証します。

## Resource Manager リソースで認証を行うサンプル コード

**AzureAutomationTutorialScript** のサンプル Runbook から次の更新済みサンプル コードを取得して使用することで、実行アカウントを使用して認証を行い、Runbook で Resource Manager リソースを管理することができます。

    $connectionName = "AzureRunAsConnection"
    $SubId = Get-AutomationVariable -Name 'SubscriptionId'
    try
    {
       # Get the connection "AzureRunAsConnection "
       $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
       
       "Logging in to Azure..."
       Add-AzureRmAccount `
         -ServicePrincipal `
         -TenantId $servicePrincipalConnection.TenantId `
         -ApplicationId $servicePrincipalConnection.ApplicationId `
         -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
	   "Setting context to a specific subscription"	 
	   Set-AzureRmContext -SubscriptionId $SubId	 		 
    }
    catch {
        if (!$servicePrincipalConnection)
        {
           $ErrorMessage = "Connection $connectionName not found."
           throw $ErrorMessage
         } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
         }
    } 
   

スクリプトには、サブスクリプション コンテキストの参照をサポートする 2 つのコード行が追加されているため、複数のサブスクリプション間での作業がしやすくなっています。SubscriptionId という名前の変数資産にはサブスクリプションの ID が格納されており、Add-AzureRmAccount コマンドレットのステートメントの後に、*-SubscriptionId* パラメーター セットを使用して [Set-AzureRmContext コマンドレット](https://msdn.microsoft.com/library/mt619263.aspx)が宣言されます。変数名が一般的すぎる場合は、目的に沿った特定しやすい名前になるよう、プレフィックスやその他の名前付け規則を含むように変数名を変更できます。また、-SubscriptionId の代わりに、-SubscriptionName パラメーター セットを対応する変数資産と共に使用することもできます。

Runbook での認証に使用されるコマンドレット (**Add-AzureRmAccount**) は、*ServicePrincipalCertificate* パラメーター セットを使用することに注意してください。認証に使用するのはサービス プリンシパル証明書であり、資格情報ではありません。

## サービス管理リソースで認証を行うサンプル コード

サンプルの **AzureClassicAutomationTutorialScript** Runbook から次の更新済みサンプル コードを取得して使用することで、クラシック実行アカウントを使用して認証を行い、Runbook でクラシック リソースを管理することができます。
    
    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        
    
    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }
      
    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }
      
    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert 
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID


## 次のステップ

- サービス プリンシパルの詳細については、[アプリケーション オブジェクトおよびサービス プリンシパル オブジェクト](../active-directory/active-directory-application-objects.md)に関するページを参照してください。
- Azure Automation におけるロールベースのアクセス制御の詳細については、「[Azure Automation におけるロールベースのアクセス制御](../automation/automation-role-based-access-control.md)」を参照してください。
- 証明書と Azure サービスの詳細については、「[Azure Cloud Services の証明書の概要](../cloud-services/cloud-services-certs-create.md)」を参照してください。

<!---HONumber=AcomDC_1005_2016-->