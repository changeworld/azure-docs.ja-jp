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
    ms.date="05/16/2016"
    ms.author="magoedte"/>

# Azure 実行アカウントを使用した Runbook の認証
このトピックでは、Azure ポータルから新しい実行アカウント機能 (サービス プリンシパル) を使用して Automation アカウントを構成し、サブスクリプション内の Azure Resource Manager (ARM) リソースに Automation Runbook でアクセスする方法について説明します。Azure ポータルで新しい Automation アカウントを作成すると、新しいサービス プリンシパルが自動的に作成され、サブスクリプションに含まれるロールベース アクセス制御 (RBAC) の Contributor ロールに既定で割り当てられます。これによって必要な作業が単純化され、オートメーションのニーズを満たす Runbook をすぐに作成し、デプロイすることができます。

サービス プリンシパルを使用して次のことができます。

* Runbook を使用した Azure ARM リソース管理に、Azure を使用した標準的な認証方法を取り入れる。
* Azure アラート内で構成されるグローバル Runbook の使用を自動化する。


>[AZURE.NOTE] Azure [アラート統合機能](../azure-portal/insights-receive-alert-notifications.md)と Automation Global Runbook の連携には、サービス プリンシパルを使って構成された Automation アカウントが必要です。既にサービス プリンシパル ユーザーが定義されている Automation アカウントを選んで利用できるほか、Automation アカウントを新たに作成することもできます。



Azure ポータルから Automation アカウントを作成する方法と、Azure PowerShell から実行アカウントを使ってアカウントを更新する方法を紹介すると共に、そのサービス プリンシパルを使って Runbook 内で認証を行う方法について説明します。

## Azure ポータルから新しい Automation アカウントを作成する
このセクションでは、Azure ポータルから以下の手順に従って、新しい Azure Automation アカウントとサービス プリンシパルを作成します。

>[AZURE.NOTE] 以下の手順を実行するユーザーは、サブスクリプション管理ロールのメンバーである*必要*があります。

1. 管理する Azure サブスクリプションのサービス管理者として Azure ポータルにログインします。
2. **[Automation アカウント]** を選択します。
3. [Automation アカウント] ブレードで **[追加]** をクリックします。<br>![Add Automation Account](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties.png)
4. **[Automation アカウントの追加]** ブレードの **[名前]** ボックスに、新しい Automation アカウントの名前を入力します。
5. 複数のサブスクリプションがある場合は、新しいアカウントに対して 1 つ指定し、新規または既存の**リソース グループ**と、Azure データ センターの**場所**を指定します。
6. **[Azure 実行アカウントの作成]** オプションで **[はい]** が選択されていることを確認し、**[作成]** ボタンをクリックします。  

    ![Add Automation Account Warning](media/automation-sec-configure-azure-runas-account/add-account-decline-create-runas-msg.png)

    >[AZURE.NOTE] 実行アカウントを作成しなかった場合 (先ほどのオプションで **[いいえ]** を選択した場合)、**[Automation アカウントの追加]** ブレードに警告メッセージが表示されます。アカウントが作成されてサブスクリプションの**共同作成者**ロールに割り当てられますが、サブスクリプションのディレクトリ サービスには対応する認証 ID が割り当てられず、サブスクリプション内のリソースにアクセスすることはできません。このアカウントを参照する Runbook は認証を通過できず、ARM リソースに対するタスクを実行することができません。

    ![Add Automation Account Warning](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties-error.png)

    >[AZURE.NOTE] **[作成]** ボタンのクリック後にアクセスが拒否されたことを示すエラー メッセージが表示された場合、使用しているアカウントが、サブスクリプション管理ロールに属していないことが原因です。

7. Azure によって Automation アカウントが作成されている間、メニューの **[通知]** で進行状況を追跡できます。

### 含まれるリソース
Automation アカウントの作成が完了すると、いくつかのリソースが自動的に作成されます。これらのリソースの概要を次の表に示します。

リソース|説明 
----|----
AzureAutomationTutorial Runbook|実行アカウントを使用した認証方法と、サブスクリプション内の最初の 10 個の Azure VM を表示する方法を示す、サンプル Runbook。
AzureRunAsCertificate|Automation アカウントの作成時に実行アカウントを作成するように選択した場合、または既存のアカウント用に下の PowerShell スクリプトを使用している場合に作成される、証明書資産。この証明書には、1 年の有効期間があります。 
AzureRunAsConnection|Automation アカウントの作成時に実行アカウントを作成するように選択した場合、または既存のアカウント用に下の PowerShell スクリプトを使用している場合に作成される、接続資産。
モジュール|Runbook 内ですぐに使用できる、Azure、PowerShell、および Automation 用のコマンドレットを含む 15 個のモジュール。  

## PowerShell を使用して Automation アカウントを更新する
以下の手順では、PowerShell を使用して既存の Automation アカウントを更新し、サービス プリンシパルを作成します。アカウントは作成済みであるものの、実行アカウントの作成を拒否した場合に、この手順が必要となります。

以降の手順に進む前に、次のことを確認してください。

1. [Windows PowerShell 用 Azure Active Directory モジュール (64 ビット バージョン)](http://go.microsoft.com/fwlink/p/?linkid=236297) をダウンロードしてインストール済みであること。
2. Automation アカウントが作成済みであること。このアカウントは、以下のスクリプトの –AutomationAccountName パラメーターと -ApplicationDisplayName パラメーターの値として参照されます。
3. [Azure Automation Authoring Toolkit](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.2) がインストール済みであること。

```
Install-Module AzureAutomationAuthoringToolkit -Scope CurrentUser
```

この PowerShell スクリプトで構成の対象となる要素は次のとおりです。

* Azure AD アプリケーション。自己署名証明書で認証され、このアプリケーションの Azure AD におけるサービス プリンシパル アカウントを作成します。現在のサブスクリプションで、このアカウントの Contributor ロールが割り当てられます (これを所有者など他の任意のロールに変更できます)。詳細については、「[Azure Automation におけるロールベースのアクセス制御](../automation/automation-role-based-access-control.md)」の記事を参照してください。  
* Automation 証明書資産。指定された Automation アカウントに **AzureRunAsCertificate** という名前で存在し、サービス プリンシパルで使用される証明書が格納されます。
* Automation 接続資産。指定された Automation アカウントに **AzureRunAsConnection** という名前で存在し、アプリケーション ID、テナント ID、サブスクリプション ID、証明書の拇印が格納されます。  


### PowerShell スクリプトの実行

1. ご使用のコンピューターに次のスクリプトを保存します。この例では、**New-AzureServicePrincipal.ps1** というファイル名で保存します。  

    ```
    #Requires - RunAsAdministrator
    Param (
    [Parameter(Mandatory=$true)]
    [String] $ResourceGroup,

    [Parameter(Mandatory=$true)]
    [String] $AutomationAccountName,

    [Parameter(Mandatory=$true)]
    [String] $ApplicationDisplayName,

    [Parameter(Mandatory=$true)]
    [String] $SubscriptionName,

    [Parameter(Mandatory=$true)]
    [String] $CertPlainPassword,

    [Parameter(Mandatory=$false)]
    [int] $NoOfMonthsUntilExpired = 12
    )

    Login-AzureRmAccount
    Import-Module AzureRM.Resources
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName

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
    While ($NewRole -eq $null -and $Retries -le 2)
    {
      # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
      Sleep 5
      New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose
      Sleep 5
      $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
      $Retries++;
    }

    # Get the tenant id for this subscription
    $SubscriptionInfo = Get-AzureRmSubscription
    $TenantID = $SubscriptionInfo | Select TenantId -First 1

    # Create the automation resources
    New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name AzureRunAsCertificate -Password $CertPassword -Exportable | write-verbose

    # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    $ConnectionAssetName = "AzureRunAsConnection"
    $SubscriptionId = $SubscriptionInfo | Select SubscriptionId -First 1
    Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
    $ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId.SubscriptionId}
    New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
    ```
<br>
2. コンピューターの**スタート**画面から、昇格されたユーザー権限で **Windows PowerShell** を起動します。
3. 昇格された PowerShell コマンドライン シェルから、手順 1. で作成したスクリプトが格納されているフォルダーに移動してスクリプトを実行します。*–ResourceGroup*、*-AutomationAccountName*、*-ApplicationDisplayName*、*-SubscriptionName*、*-CertPlainPassword* の各パラメーターの値は適宜変更してください。<br>

    ```
    .\New-AzureServicePrincipal.ps1 -ResourceGroup <ResourceGroupName> `
     -AutomationAccountName <NameofAutomationAccount> `
     -ApplicationDisplayName <DisplayNameofAutomationAccount> `
     -SubscriptionName <SubscriptionName> `
     -CertPlainPassword "<StrongPassword>"
    ```   
<br>

    >[AZURE.NOTE] スクリプトの実行後、Azure での認証が求められます。*必ず*、サブスクリプションのサービス管理者であるアカウントでログインしてください。 <br>
4. スクリプトが正常に完了したら、次のセクションに進んで新しい資格情報の構成をテストし、検証します。

### 認証の検証
次に、新しいサービス プリンシパルを使用して正しく認証できることを確認するために小さなテストを実施します。正しく認証できない場合は、手順 1. に戻り、先行する各手順をもう一度確かめてください。

1. 先ほど作成した Automation アカウントを Azure ポータルで開きます。  
2. **[Runbook]** タイルをクリックして、Runbook の一覧を開きます。
3. **[Runbook の追加]** ボタンをクリックし、**[Runbook の追加]** ブレードで **[新しい Runbook の作成]** を選択して新しい Runbook を作成します。
4. Runbook に *Test-SecPrin-Runbook* という名前を付け、**[Runbook の種類]** で [PowerShell] を選択します。**[作成]** をクリックして Runbook を作成します。
5. **[PowerShell Runbook の編集]** ブレードのキャンバスに次のコードを貼り付けます。<br>

    ```
     $Conn = Get-AutomationConnection -Name AzureRunAsConnection 
     Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
     -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    ```  
<br>
6. **[保存]** をクリックして Runbook を保存します。
7. **[テスト ウィンドウ]** をクリックして、**[テスト]** ブレードを開きます。
8. **[開始]** をクリックしてテストを開始します。
9. [Runbook ジョブ](automation-runbook-execution.md)が作成され、その状態がペインに表示されます。  
10. 最初のジョブの状態は*キュー登録済み*であり、クラウドの Runbook Worker が使用可能になるのを待っていることを示しています。その後、ワーカーがジョブを要求すると*開始中*になり、Runbook が実際に実行を開始すると*実行中*になります。  
11. Runbook ジョブが完了すると、その出力が表示されます。このケースでは、状態が **[完了]** となります。<br> ![Security Principal Runbook Test](media/automation-sec-configure-azure-runas-account/runbook-test-results.png)<br>
12. **[テスト]** ブレードを閉じてキャンバスに戻ります。
13. **[PowerShell Runbook の編集]** ブレードを閉じます。
14. **[Test-SecPrin-Runbook]** ブレードを閉じます。

## ARM リソースで認証を行うサンプル コード

AzureAutomationTutorial のサンプル Runbook から次の更新済みサンプル コードを取得して使用することで、実行アカウントを使用して認証を行い、Runbook で ARM リソースを管理することができます。

   ```
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
   ```

スクリプトには、サブスクリプション コンテキストの参照をサポートする 2 つのコード行が追加されているため、複数のサブスクリプション間での作業がしやすくなっています。SubscriptionId という名前の変数資産にはサブスクリプションの ID が格納されており、Add-AzureRmAccount コマンドレットのステートメントの後に、*-SubscriptionId* パラメーター セットを使用して [Set-AzureRmContext コマンドレット](https://msdn.microsoft.com/library/mt619263.aspx)が宣言されます。変数名が一般的すぎる場合は、目的に沿った特定しやすい名前になるよう、プレフィックスやその他の名前付け規則を含むように変数名を変更できます。また、-SubscriptionId の代わりに、-SubscriptionName パラメーター セットを対応する変数資産と共に使用することもできます。

## 次のステップ
- サービス プリンシパルの詳細については、「[アプリケーション オブジェクトおよびサービス プリンシパル オブジェクト](../active-directory/active-directory-application-objects.md)」を参照してください。
- Azure Automation におけるロールベースのアクセス制御の詳細については、「[Azure Automation におけるロールベースのアクセス制御](../automation/automation-role-based-access-control.md)」を参照してください。

<!---HONumber=AcomDC_0518_2016-->