---
title: Azure Automation の Hybrid Runbook Worker での Runbook の実行
description: この記事では、Hybrid Runbook Worker ロールを持つローカル データセンターまたはクラウド プロバイダーのコンピューターでの Runbook の実行について説明します。
services: automation
ms.subservice: process-automation
ms.date: 01/29/2019
ms.topic: conceptual
ms.openlocfilehash: 902734ddc7195d643c3aedb4054f57723d1a51c2
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632132"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker での Runbook の実行

通常、Hybrid Runbook Worker をターゲットとする Runbook では、ローカル コンピューター上のリソース、または worker がデプロイされているローカル環境内のリソースを管理します。 Azure Automation の Runbook は、通常、Azure クラウド内のリソースを管理します。 Azure Automation で実行される Runbook と、Hybrid Runbook Worker で実行される Runbook は、使い方は異なりますが、構造的には同じものです。

Hybrid Runbook Worker で実行される Runbook を作成するときは、worker がホストされているマシンで Runbook を編集し、テストする必要があります。 ホスト マシンには、ローカル リソースの管理とアクセスのために必要な、すべての PowerShell モジュールとネットワーク アクセスがあります。 Hybrid Runbook Worker マシンで Runbook をテストした後は、それを Azure Automation 環境にアップロードし、worker で実行できます。 

>[!NOTE]
>この記事は、新しい Azure PowerShell Az モジュールを使用するために更新されました。 AzureRM モジュールはまだ使用でき、少なくとも 2020 年 12 月までは引き続きバグ修正が行われます。 Az モジュールと AzureRM の互換性の詳細については、「[Introducing the new Azure PowerShell Az module (新しい Azure PowerShell Az モジュールの概要)](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)」を参照してください。 Hybrid Runbook Worker での Az モジュールのインストール手順については、「[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)」を参照してください。 Automation アカウントについては、「[Azure Automation の Azure PowerShell モジュールを更新する方法](automation-update-azure-modules.md)」に従って、モジュールを最新バージョンに更新できます。

## <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker に対する Runbook のアクセス許可

Hybrid Runbook Worker で実行される Runbook は、Azure 以外のリソースにアクセスするため、Azure リソースへの認証に Runbook で通常使用される認証メカニズムを使用できません。 Runbook では、ローカル リソースに対して独自の認証を提供するか、または [Azure リソース用のマネージド ID](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager) を使用して認証を構成します。 また、すべての Runbook にユーザー コンテキストを提供する実行アカウントを指定することもできます。

### <a name="runbook-authentication"></a>Runbook の認証

既定では、Runbook はローカル コンピューター上で実行されます。 Windows では、ローカル システム アカウントのコンテキストで実行されます。 Linux では、特別なユーザー アカウント **nxautomation** のコンテキストで実行されます。 どちらのシナリオでも、Runbook ではアクセスするリソースに対して独自の認証を提供する必要があります。

資格情報を指定できるコマンドレットで Runbook の[資格情報](automation-credentials.md)資産と[証明書](automation-certificates.md)資産を使用することにより、Runbook で異なるリソースに対して認証を行うことができます。 次の例は、コンピューターを再起動する Runbook の一部を示しています。 資格情報資産から資格情報を取得し、変数資産からはコンピューター名を取得して、`Restart-Computer` コマンドレットでこれらの値を使用します。

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

また、[InlineScript](automation-powershell-workflow.md#inlinescript) アクティビティを使用することもできます。 `InlineScript` を使用すると、[PSCredential 共通パラメーター](/powershell/module/psworkflow/about/about_workflowcommonparameters)で指定されている資格情報を使用して、別のコンピューター上でコード ブロックを実行できます。

### <a name="run-as-account"></a>実行アカウント

Runbook でローカル リソースに独自の認証を提供するのではなく、Hybrid Runbook Worker グループに対して実行アカウントを指定することができます。 これを行うには、ローカル リソースにアクセスできる[資格情報資産](automation-credentials.md)を定義する必要があります。 これらのリソースには証明書ストアが含まれており、すべての Runbook はグループ内の Hybrid Runbook Worker 上でこれらの資格情報を使用して実行されます。

資格情報のユーザー名は、次の形式にする必要があります。

* ドメイン\ユーザー名
* username@domain
* ユーザー名 (オンプレミス コンピューターのローカルのアカウントの場合)

Hybrid Runbook Worker グループの実行アカウントを指定するには、以下の手順のようにします。

1. ローカル リソースに対するアクセス権を持つ [資格情報資産](automation-credentials.md) を作成します。
2. Azure ポータルで Automation アカウントを開きます。
3. **[ハイブリッド Worker グループ]** タイルを選択し、グループを選択します。
4. **[すべての設定]** を選択し、 **[ハイブリッド Worker グループの設定]** を選択します。
5. **[実行者]** の値を **[既定]** から **[カスタム]** に変更します。
6. 資格情報を選択し、 **[保存]** をクリックします。

### <a name="managed-identities-for-azure-resources"></a><a name="managed-identities-for-azure-resources"></a>Azure リソースのマネージド ID

Azure Virtual Machines 上の Hybrid Runbook Worker では、Azure リソースのマネージド ID を使って、Azure リソースに対する認証を行うことができます。 実行アカウントの代わりに Azure リソースのマネージド ID を使うと、次のことを行う必要がないという利点があります。

* 実行アカウントの証明書をエクスポートし、それを Hybrid Runbook Worker にインポートする。
* 実行アカウントで使用される証明書を更新する。
* Runbook のコードで実行アカウントの接続オブジェクトを処理する。

Hybrid Runbook Worker 上の Azure リソースに対してマネージド ID を使用するには、次の手順のようにします。

1. Azure VM を作成します。
2. VM で Azure リソース用のマネージド ID を構成します。 「[Azure portal を使用して Azure VM で Azure リソースのマネージド ID を構成する](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)」をご覧ください。
3. Resource Manager で VM にリソース グループへのアクセス権を付与します。 「[Windows VM のシステム割り当てマネージド ID を使用して Resource Manager にアクセスする](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)」をご覧ください。
4. Hybrid Runbook Worker を VM にインストールします。 「[Windows Hybrid Runbook Worker をデプロイする](automation-windows-hrw-install.md)」をご覧ください。
5. [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) コマンドレットと `Identity` パラメーターを使用して Azure リソースに対する認証を行うように、Runbook を更新します。 この構成により、実行アカウントを使用し、関連するアカウントを管理を実行する必要性が減ります。

```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
```

> [!NOTE]
> `Connect-AzAccount -Identity` は、システム割り当て ID と単一ユーザー割り当て ID を使用する Hybrid Runbook Worker に対して機能します。 Hybrid Runbook Worker で複数のユーザー割り当て ID を使用する場合は、Runbook で `Connect-AzAccount` の *AccountId* パラメーターを指定して、特定のユーザー割り当て ID を選択する必要があります。

### <a name="automation-run-as-account"></a><a name="runas-script"></a>Automation 実行アカウント

Azure にリソースをデプロイするために自動ビルド プロセスの一部として、デプロイ シーケンスでタスクまたは一連の手順をサポートするために、オンプレミスのシステムにアクセスすることが必要になる場合があります。 実行アカウントを使用して Azure に対する認証を提供するには、実行アカウントの証明書をインストールする必要があります。

次に示す **Export-RunAsCertificateToHybridWorker** という名前の PowerShell Runbook では、Azure Automation アカウントから実行アカウントの証明書がエクスポートされます。 この Runbook では、証明書がダウンロードされて、同じアカウントに接続された Hybrid Runbook Worker 上のローカル コンピューター証明書ストアにインポートされます。 そのステップが完了した後、Runbook では、worker が実行アカウントを使用して Azure に対する認証を正常に実行できることが確認されます。

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. Run this runbook on the hybrid worker where you want the certificate installed. This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running on the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test to see if authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzContext -Subscription $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm that Azure Resource Manager calls are working
Get-AzAutomationAccount | Select-Object AutomationAccountName
```

>[!NOTE]
>PowerShell Runbook の場合、`Add-AzAccount` と `Add-AzureRMAccount` は `Connect-AzAccount` の別名です。 ライブラリ項目を検索して `Connect-AzAccount` が表示されない場合は、`Add-AzAccount` を使用するか、Automation アカウントでモジュールを更新することができます。

実行アカウントの準備を完了するには、次のようにします。

1. **Export-RunAsCertificateToHybridWorker** Runbook を、 **.ps1** という拡張子でコンピューターに保存します。
2. それをお使いの Automation アカウントにインポートします。
3. Runbook を編集し、`Password` 変数の値を独自のパスワードに変更します。 
4. Runbook を発行します。
5. 実行アカウントを使用して Runbook の実行と認証を行う Hybrid Runbook Worker グループを対象に、Runbook を実行します。 
6. ジョブ ストリームを調べて、ローカル コンピューターのストアへの証明書のインポートの試行が報告された後に複数の行があることを確認します。 この動作は、サブスクリプションで定義されている Automation アカウントの数と、認証がどの程度成功したかによって異なります。

## <a name="job-behavior-on-hybrid-runbook-workers"></a>Hybrid Runbook Worker でのジョブの動作

Azure Automation による Hybrid Runbook Worker でのジョブの処理は、Azure サンドボックスで実行されるジョブとは若干異なります。 主な違いの 1 つは、Runbook Worker ではジョブ期間に制限がないことです。 Azure サンドボックスで実行される Runbook の場合は、[フェア シェア](automation-runbook-execution.md#fair-share)により 3 時間に制限されます。

実行時間の長い Runbook の場合、worker がホストされているコンピューターが再起動される場合など、再起動に対して回復性が保証されている必要があります。 Hybrid Runbook Worker のホスト コンピューターが再起動された場合、実行中の Runbook ジョブは最初から再起動されるか、PowerShell ワークフロー Runbook の最後のチェックポイントから再起動されます。 Runbook ジョブの再起動が 3 回を超えると、そのジョブは中断されます。

Hybrid Runbook Worker のジョブは、Windows ではローカル システム アカウントで実行され、Linux では **nxautomation** アカウントで実行されることに注意してください。 Linux の場合、Runbook モジュールが格納されている場所に **nxautomation** アカウントがアクセスできることを確認する必要があります。 [Install-Module](/powershell/module/powershellget/install-module) コマンドレットを使用するときは、`Scope` パラメーターに対して AllUsers を指定し、**nxautomation** アカウントがアクセスできるようにします。 Linux での PowerShell に関する詳細については、「[Windows 以外のプラットフォームでの PowerShell に関する既知の問題](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms)」を参照してください。

## <a name="starting-a-runbook-on-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker での Runbook の開始

「[Azure Automation での Runbook の開始](automation-starting-a-runbook.md)」では、Runbook を開始するさまざまな方法が説明されています。 Hybrid Runbook Worker での Runbook の開始では、Hybrid Runbook Worker グループの名前を指定できる **Run on** オプションが使用されます。 グループを指定すると、そのグループ内の worker の 1 つによって Runbook が取得されて実行されます。 Runbook でこのオプションが指定されていない場合は、Azure Automation によって通常どおり Runbook が実行されます。

Azure portal で Runbook を開始するときは、 **[実行場所を選択して実行]** オプションで **[Azure]** または **[ハイブリッド worker]** を選択できます。 **[ハイブリッド worker]** を選択した場合は、ドロップダウンから Hybrid Runbook Worker グループを選択できます。

`Start-AzureAutomationRunbook` コマンドレットで `RunOn` パラメーターを使用します。 次の例では、Windows PowerShell を使用して、MyHybridGroup という名前の Hybrid Runbook Worker グループで **Test-Runbook** という名前の Runbook を開始しています。

```azurepowershell-interactive
Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> `RunOn` パラメーターは、Microsoft Azure PowerShell バージョン 0.9.1 の `Start-AzureAutomationRunbook` に追加されました。 以前のバージョンがインストールされている場合は、 [最新バージョンをダウンロード](https://azure.microsoft.com/downloads/) する必要があります。 PowerShell から Runbook を開始するワークステーションにのみ、このバージョンをインストールします。 Hybrid Runbook Worker コンピューターには、そのコンピューターから Runbook を開始する場合を除き、インストールする必要はありません。

## <a name="working-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Windows Hybrid Runbook Worker での署名された Runbook の使用

署名された Runbook のみを実行するように Windows Hybrid Runbook Worker を構成できます。

> [!IMPORTANT]
> 署名された Runbook のみを実行するように Hybrid Runbook Worker を構成すると、署名されていない Runbook は worker で実行できなくなります。

### <a name="create-signing-certificate"></a>署名証明書の作成

次の例では、Runbook の署名に使用できる自己署名証明書を作成します。 このコードでは、証明書が作成されてエクスポートされるので、後で Hybrid Runbook Worker にインポートできます。 後で証明書を参照するときに使用できるように、サムプリントも返されます。

```powershell
# Create a self-signed certificate that can be used for code signing
$SigningCert = New-SelfSignedCertificate -CertStoreLocation cert:\LocalMachine\my `
                                        -Subject "CN=contoso.com" `
                                        -KeyAlgorithm RSA `
                                        -KeyLength 2048 `
                                        -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
                                        -KeyExportPolicy Exportable `
                                        -KeyUsage DigitalSignature `
                                        -Type CodeSigningCert


# Export the certificate so that it can be imported to the hybrid workers
Export-Certificate -Cert $SigningCert -FilePath .\hybridworkersigningcertificate.cer

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Retrieve the thumbprint for later use
$SigningCert.Thumbprint
```

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>証明書をインポートし、署名を検証するように worker を構成する

作成した証明書をグループ内の各 Hybrid Runbook Worker にコピーします。 次のスクリプトを実行して、証明書をインポートし、Runbook で署名の検証を使用するように worker を構成します。

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>証明書を使用して Runbook に署名する

署名された Runbook のみを使用するように Hybrid Runbook Worker を構成したので、Hybrid Runbook Worker で使用される Runbook に署名する必要があります。 次のサンプルの PowerShell コードを使用して Runbook に署名します。

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Runbook が署名されたら、Automation アカウントにインポートし、署名ブロックで発行する必要があります。 Runbook のインポート方法については、「[ファイルから Azure Automation への Runbook のインポート](manage-runbooks.md#importing-a-runbook)」をご覧ください。

## <a name="working-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker での署名された Runbook の使用

署名された Runbook を使用できるようにするには、Linux Hybrid Runbook Worker のローカル コンピューター上に [GPG](https://gnupg.org/index.html) 実行可能ファイルが存在する必要があります。

> [!IMPORTANT]
> 署名された Runbook のみを実行するように Hybrid Runbook Worker を構成すると、署名されていない Runbook は worker で実行できなくなります。

### <a name="create-a-gpg-keyring-and-keypair"></a>GPG キーリングとキー ペアを作成する

GPG のキーリングとキー ペアを作成するには、Hybrid Runbook Worker の **nxautomation** アカウントを使用します。

1. sudo アプリケーションを使用し、**nxautomation** アカウントとしてサインインします。

    ```bash
    sudo su – nxautomation
    ```

2. **nxautomation** を使用して、GPG のキー ペアを生成します。 GPG で手順が示されます。 名前、メール アドレス、有効期限、パスフレーズを指定する必要があります。 その後、コンピューターのエントロピがキーを生成するのに十分になるまで待ちます。

    ```bash
    sudo gpg --generate-key
    ```

3. sudo を使って GPG ディレクトリを生成したため、次のコマンドを使ってその所有者を **nxautomation** に変更する必要があります。

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>Hybrid Runbook Worker でキーリングを使用できるようにする

キーリングが作成されたら、Hybrid Runbook Worker でそれを使用できるようにします。 設定ファイル **/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf** を変更し、このファイルの `[worker-optional]` セクションに次のコード例を含めます。

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>署名の検証が有効であることを確認する

コンピューターで署名の検証が無効になっている場合は、次の sudo コマンドを実行して有効にする必要があります。 `<LogAnalyticsworkspaceId>` は、ワークスペースの ID に置き換えます。

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Runbook に署名する

署名の検証を構成したら、次の GPG コマンドを使用して Runbook に署名します。

```bash
gpg –-clear-sign <runbook name>
```

署名された Runbook は `<runbook name>.asc` という名前になります。

署名された Runbook を Azure Automation にアップロードし、標準の Runbook のように実行できるようになります。

## <a name="next-steps"></a>次のステップ

* Runbook を開始する方法について詳しくは、「[Azure Automation で Runbook を開始する](automation-starting-a-runbook.md)」を参照してください。
* テキスト エディターを使用して、Azure Automation で PowerShell Runbook を操作する方法については、[Azure Automation での Runbook の編集](automation-edit-textual-runbook.md)に関する記事を参照してください。
* Runbook が正常に完了しない場合は、[Runbook の実行エラー](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails)に関するトラブルシューティング ガイドを参照してください。
* PowerShell (言語リファレンス、学習モジュールを含む) の詳細については、[PowerShell ドキュメント](https://docs.microsoft.com/powershell/scripting/overview)に関するページを参照してください。
