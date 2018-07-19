---
title: Azure Stack 上の MySQL リソース プロバイダーのメンテナンス | Microsoft Docs
description: Azure Stack 上の MySQL リソース プロバイダー サービスのメンテナンス方法について説明します。
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: bc1c96d2f027d459ca20fccb70cd94ac9e5cae94
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130140"
---
# <a name="mysql-resource-provider-maintenance-operations"></a>MySQL リソース プロバイダーのメンテナンス操作

MySQL リソース プロバイダーは、ロックダウンされた仮想マシン上で実行されます。 保守操作を有効にするには、仮想マシンのセキュリティを更新する必要があります。 最小権限の原則を使用してそれを行うには、PowerShell Just Enough Administration (JEA) エンドポイント DBAdapterMaintenance を使用できます。 リソース プロバイダーのインストール パッケージには、この操作のためのスクリプトが含まれています。

## <a name="update-the-virtual-machine-operating-system"></a>仮想マシンのオペレーティング システムの更新

リソース プロバイダーは "*ユーザー*" 仮想マシン上で実行されているので、必要な修正プログラムおよび更新プログラムがリリースされたら、それらを適用する必要があります。 修正プログラム適用と更新のサイクルの一環で提供される Windows 更新プログラム パッケージを使用して、VM に更新プログラムを適用できます。

次のいずれかの方法を使用して、プロバイダーの仮想マシンを更新します。

- 現在パッチが適用されている Windows Server 2016 Core イメージを使用して最新のリソース プロバイダーのパッケージをインストールする。
- リソース プロバイダーのインストールまたは更新中に Windows 更新プログラム パッケージをインストールする。

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>仮想マシンの Windows Defender の定義を更新する

Defender の定義を更新するには、次の手順に従います。

1. [Windows Defender の定義](https://www.microsoft.com/en-us/wdsi/definitions)から Windows Defender 定義の更新をダウンロードします。

    定義に関するページ上で下方にスクロールして [Manually download and install the definitions]\(定義を手動でダウンロードしてインストールする\) を見つけます。 "Windows Defender Antivirus for Windows 10 および Windows 8.1" 64 ビット ファイルをダウンロードします。

    あるいは、[こちらのダイレクト リンク](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64)を使用して、fpam-fe.exe ファイルをダウンロードして実行します。

2. MySQL リソース プロバイダー アダプター仮想マシンのメンテナンス エンドポイントへの PowerShell セッションを開きす。

3. メンテナンス エンドポイントのセッションを使用して、リソース プロバイダー アダプター VM に定義更新ファイルをコピーします。

4. メンテナンス PowerShell セッションで _Update-DBAdapterWindowsDefenderDefinitions_ コマンドを実行します。

5. 定義をインストールしたら、_Remove-ItemOnUserDrive_ コマンドを使用することにより、定義更新ファイルを削除することをお勧めします。

**定義を更新するための PowerShell スクリプトの例**

次のスクリプトを編集して実行することにより、Defender の定義を更新することができます。 スクリプト内の値を、実際の環境の値に置き換えます。

```powershell
# Set credentials for the local admin on the resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Provide the public IP address for the adapter VM.
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions.  
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' `
    -Outfile $localPathToDefenderUpdate  

# Create a session to the maintenance endpoint.
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance

# Copy the defender update file to the adapter virtual machine.
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\"

# Install the update definitions.
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinition -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}

# Cleanup the definitions package file and session.
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession

```

## <a name="secrets-rotation"></a>シークレットのローテーション

*この記事の説明は、Azure Stack 統合システム バージョン 1804 以降に対してのみ適用されます。Azure Stack の 1804 より前のバージョンに対してシークレットのローテーションを試みないでください。*"

Azure Stack 統合システムと共に SQL および MySQL リソース プロバイダーを使用する場合、次のインフラストラクチャ (デプロイ) のシークレットをローテーションすることができます。

- [デプロイ時に提供](azure-stack-pki-certs.md)された外部 SSL 証明書。
- デプロイ時に提供されたリソース プロバイダー VM のローカル管理者アカウントのパスワード。
- リソース プロバイダーの診断ユーザー (dbadapterdiag) のパスワード。

### <a name="powershell-examples-for-rotating-secrets"></a>PowerShell のシークレットのローテーション例

**すべてのシークレットを同時に変更する。**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd `  
    -VMLocalCredential $localCreds

```

**診断ユーザーのパスワードを変更する。**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword  $passwd

```

**VM ローカル管理者アカウントのパスワードを変更する。**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds

```

**SSL 証明書のパスワードを変更する。**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd

```

### <a name="secretrotationmysqlproviderps1-parameters"></a>SecretRotationMySQLProvider.ps1 パラメーター

|パラメーター|説明|
|-----|-----|
|AzCredential|Azure Stack サービス管理者アカウントの資格情報。|
|CloudAdminCredential|Azure Stack クラウド管理者ドメイン アカウントの資格情報。|
|PrivilegedEndpoint|Get-AzureStackStampInformation にアクセスするための特権エンドポイント。|
|DiagnosticsUserPassword|診断ユーザー アカウントのパスワード。|
|VMLocalCredential|MySQLAdapter VM でのローカル管理者アカウント。|
|DefaultSSLCertificatePassword|既定の SSL 証明書 (* pfx) のパスワード。|
|DependencyFilesLocalPath|依存関係ファイルのローカル パス。|
|     |     |

### <a name="known-issues"></a>既知の問題

**問題:**<br>
シークレット ローテーションのスクリプトの実行が失敗した場合、シークレット ローテーションのログが自動的に収集されません。

**対処法:**<br>
Get-AzsDBAdapterLogs コマンドレットを使用して、C:\Logs に保存されているすべてのリソース プロバイダーのログ (AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log など) を収集します。

## <a name="collect-diagnostic-logs"></a>診断ログの収集

ロックダウンされた仮想マシンからログを収集するには、PowerShell Just Enough Administration (JEA) エンドポイント DBAdapterDiagnostics を使用します。 このエンドポイントでは、次のコマンドが提供されます。

- **Get-AzsDBAdapterLog**。 このコマンドは、リソース プロバイダー診断ログの zip パッケージを作成し、そのファイルをセッションのユーザー ドライブ上に保存します。 このコマンドはパラメーターなしで実行することができ、過去 4 時間のログが収集されます。

- **Remove-AzsDBAdapterLog**。 このコマンドは、リソース プロバイダー VM に対する既存のログ パッケージを削除します。

### <a name="endpoint-requirements-and-process"></a>エンドポイントの要件とプロセス

リソース プロバイダーをインストールまたは更新すると、dbadapterdiag ユーザー アカウントが作成されます。 このアカウントを使用することで、診断ログを収集できます。

>[!NOTE]
>dbadapterdiag アカウント パスワードは、プロバイダーのデプロイ中または更新中に作成される仮想マシンのローカル管理者用に使用されるパスワードと同じです。

_DBAdapterDiagnostics_ コマンドを使用するには、リソース プロバイダーの仮想マシンに対するリモート PowerShell セッションを作成し、**Get-AzsDBAdapterLog** コマンドを実行します。

**FromDate** および **ToDate** パラメーターを使用して、ログ収集の期間を設定します。 これらのパラメーターの一方または両方を指定しない場合は、次の既定値が使用されます。

* FromDate は現在の時刻の 4 時間前です。
* ToDate は現在の時刻です。

**ログを収集する PowerShell スクリプトの例**

次のスクリプトでは、リソース プロバイダー VM から診断ログを収集する方法を示します。

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'
$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds
        -ConfigurationName DBAdapterDiagnostics

# Sample that captures logs from the previous hour.
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs to the user drive.
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup the logs.
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove-AzsDBAdapterLog}
# Close the session.
$session | Remove-PSSession

```

## <a name="next-steps"></a>次の手順

[MySQL リソース プロバイダーを削除する](azure-stack-mysql-resource-provider-remove.md)
