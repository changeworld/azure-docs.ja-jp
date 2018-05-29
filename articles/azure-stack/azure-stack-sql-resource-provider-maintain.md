---
title: Azure Stack での SQL データベースの使用 | Microsoft Docs
description: SQL データベースを Azure Stack にサービスとしてデプロイする方法と、SQL Server リソース プロバイダー アダプターの簡単なデプロイ手順について説明します。
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
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 53436d131672622ae1a72a1bb84d5aa83fdbdc0c
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2018
ms.locfileid: "33207936"
---
# <a name="maintenance-operations"></a>メンテナンス操作 
SQL リソース プロバイダーは、ロック ダウンされた仮想マシンです。 リソース プロバイダー仮想マシンのセキュリティの更新は、PowerShell Just Enough Administration (JEA) エンドポイント _DBAdapterMaintenance_ から実行できます。 これらの操作のためのスクリプトが、RP のインストール パッケージで提供されています。

## <a name="patching-and-updating"></a>修正プログラム適用と更新
SQL リソース プロバイダーはアドオン コンポーネントであるため、Azure Stack の一部としては提供されません。 必要に応じて、SQL リソース プロバイダーの更新プログラムが提供されます。 SQL リソースプロバイダーは、既定のプロバイダー サブスクリプションの下で、_ユーザー_の仮想マシンでインスタンス化されます。 そのため、Windows 修正プログラム、ウイルス対策シグネチャなどを提供する必要があります。修正プログラム適用と更新のサイクルの一環で提供される Windows 更新プログラムを使用して、Windows VM に更新プログラムを適用できます。 更新されたアダプターがリリースされると、更新プログラムを適用するためのスクリプトが提供されます。 このスクリプトでは、新しい RP VM を作成し、既に存在する状態に移行させます。

 ## <a name="backuprestoredisaster-recovery"></a>バックアップ/復元/ディザスター リカバリー
 SQL リソースプロバイダーはアドオン コンポーネントであるため、Azure Stack BC-DR プロセスの一部としてはバックアップされません。 以下を容易にするスクリプトが提供されます。
- 必要な状態情報のバックアップ (Azure Stack ストレージ アカウントに格納されている)
- スタックをすべて回復する場合には RP の復元が必要になります。
データベース サーバーの回復は (必要な場合)、リソース プロバイダーを復元する前に最初に行う必要があります。

## <a name="updating-sql-credentials"></a>SQL 資格情報の更新
管理者は、SQL Server のシステム管理者アカウントの作成と保守を担当します。 リソース プロバイダーには、ユーザーに代わってデータベースを管理するためにこれらの権限があるアカウントが必要となりますが、データベース内のデータにアクセスする必要はありません。 SQL Server で sa パスワードを更新する必要がある場合、リソース プロバイダーの管理者インターフェイスの更新機能を使用して、リソース プロバイダーが使用する保存済みパスワードを変更できます。 これらのパスワードは、Azure Stack インスタンス上の Key Vault に格納されています。

設定を変更するには、**[参照]** &gt; **[管理リソース]** &gt; **[SQL Hosting Servers]\(SQL ホスティング サーバー\)** &gt; **[SQL ログイン]** の順にクリックし、ログイン名を選択します。 変更は、最初に SQL インスタンス (および必要な場合はレプリカ) で行う必要があります 。 **[設定]** パネルで、**[パスワード]** をクリックします。

![管理パスワードの更新](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)

## <a name="update-the-virtual-machine-operating-system"></a>仮想マシンのオペレーティング システムの更新
Windows Server VM を更新するには、いくつかの方法があります。
* 現在パッチが適用された Windows Server 2016 Core イメージを使用して最新のリソース プロバイダーのパッケージをインストールする
* RP の更新またはインストール中に Windows 更新プログラム パッケージをインストールする

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>仮想マシンの Windows Defender の定義を更新する
これらの手順に従って Defender の定義を更新します。

1. [Windows Defender の定義](https://www.microsoft.com/en-us/wdsi/definitions)から Windows Defender 定義の更新をダウンロードします。

    そのページの [Manually download and install the definitions]\(定義を手動でダウンロードしてインストールする\) から "Windows Defender Antivirus for Windows 10 および Windows 8.1" 64 ビット ファイルをダウンロードします。 
    
    直接リンク: https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64

2. SQL RP アダプター仮想マシンのメンテナンス エンドポイントに対し PowerShell セッションを作成します。
3. メンテナンス エンドポイントのセッションを使用して DB アダプター コンピューターに、定義更新ファイルをコピーします。
4. メンテナンス PowerShell セッションで _Update-DBAdapterWindowsDefenderDefinitions_ コマンドを呼び出します。
5. インストール後に使用される定義更新ファイルを削除することをお勧めします。 定義更新ファイルは、メンテナンス セッションで _Remove-ItemOnUserDrive_ コマンドを使用して削除できます。


Defender の定義を更新するサンプル スクリプトを次に示します (仮想マシンのアドレスまたは名前を実際の値に置き換えてください)。

```powershell
# Set credentials for the diagnostic user
$diagPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$diagCreds = New-Object System.Management.Automation.PSCredential `
    ("dbadapterdiag", $vmLocalAdminPass)$diagCreds = Get-Credential

# Public IP Address of the DB adapter machine
$databaseRPMachine  = "XX.XX.XX.XX"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"
 
# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions. 
Invoke-WebRequest -Uri https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64 `
    -Outfile $localPathToDefenderUpdate 

# Create session to the maintenance endpoint
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $diagCreds -ConfigurationName DBAdapterMaintenance
# Copy defender update file to the db adapter machine
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\mpam-fe.exe"
# Install the update file
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinitions -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession
```


## <a name="collect-diagnostic-logs"></a>診断ログの収集
SQL リソース プロバイダーは、ロック ダウンされた仮想マシンです。 仮想マシンからログを収集することが必要になった場合は、、そのために PowerShell Just Enough Administration (JEA) エンドポイント _DBAdapterDiagnostics_ が提供されています。 このエンドポイントで使用できる 2 つのコマンドが 2 つあります。

* Get-AzsDBAdapterLog - RP 診断ログを含む zip パッケージを準備し、セッション ユーザー ドライブに配置します。 このコマンドは、パラメーターなしで呼び出すことができます。過去 4 時間分のログが収集されます。
* Remove-AzsDBAdapterLog - リソース プロバイダー VM 上の既存のログ パッケージを削除します。

RP ログを抽出する診断エンドポイントに接続するために、RP 展開中または更新中に _dbadapterdiag_ というユーザー アカウントが作成されます。 このアカウントのパスワードは、展開/更新中に指定したローカル管理者アカウントのパスワードと同じです。

これらのコマンドを使用するには、リソース プロバイダーの仮想マシンにリモート PowerShell セッションを作成し、コマンドを呼び出す必要があります。 必要に応じて、FromDate および ToDate パラメーターを指定できます。 これらの一方または両方を指定しない場合、FromDate は現在の時刻より 4 時間前になり、ToDate は現在の時刻になります。

このサンプル スクリプトは、これらのコマンドの使用方法を示します。

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<see above>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, $diagnosticsUserPassword)
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
        -ConfigurationName DBAdapterDiagnostics

# Sample captures logs from the previous one hour
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup logs
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session
$session | Remove-PSSession
```

## <a name="next-steps"></a>次の手順
[サーバーをホストする SQL Server を追加する](azure-stack-sql-resource-provider-hosting-servers.md)
