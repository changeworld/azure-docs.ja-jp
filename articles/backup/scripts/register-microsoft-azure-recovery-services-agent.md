---
title: スクリプト サンプル - オンプレミスの Windows サーバーまたはクライアント コンピューターを Recovery Services コンテナーに登録する
description: スクリプトを使用して、オンプレミスの Windows サーバーまたはクライアント コンピューターを Recovery Services コンテナーに登録する方法について学習します。
ms.topic: sample
ms.date: 06/23/2021
ms.openlocfilehash: a35d241ba86868c666916e284fb0700b59034eee
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2021
ms.locfileid: "112560824"
---
# <a name="powershell-script-to-register-an-on-premises-windows-server-or-a-client-machine-with-recovery-services-vault"></a>オンプレミスの Windows サーバーまたはクライアント コンピューターを Recovery Services コンテナーに登録するための PowerShell スクリプト

このスクリプトは、オンプレミスの Windows サーバーまたはクライアント コンピューターを Recovery Services コンテナーに登録するのに役立ちます。 

## <a name="sample-script"></a>サンプル スクリプト

```azurepowershell
<#

.SYNOPSIS
Registers MARS agent

.DESCRIPTION
Registers MARS agent

.ROLE
Administrators

#>
param (
    [Parameter(Mandatory = $true)]
    [String]
    $vaultcredPath,
    [Parameter(Mandatory = $true)]
    [String]
    $passphrase
)
Set-StrictMode -Version 5.0
$env:PSModulePath = (Get-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Session Manager\Environment' -Name PSModulePath).PSModulePath
Import-Module MSOnlineBackup
$ErrorActionPreference = "Stop"
Try {
    $date = Get-Date
    Start-OBRegistration -VaultCredentials $vaultcredPath -Confirm:$false
    $securePassphrase = ConvertTo-SecureString -String $passphrase -AsPlainText -Force
    Set-OBMachineSetting -EncryptionPassphrase $securePassphrase -SecurityPIN " "
}
Catch {
    if ($error[0].ErrorDetails) {
        throw $error[0].ErrorDetails
    }
    throw $error[0]
}

```

## <a name="how-to-execute-the-script"></a>スクリプトを実行する方法

1. 上記のスクリプトを、任意の名前と .ps1 拡張子で、お使いのコンピューターに保存します。
1. 次のパラメーターを指定して、スクリプトを実行します。
   - – vaultcredPath - ダウンロードしたコンテナー資格情報ファイルの完全なパス
   - – passphrase - [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-7.1&preserve-view=true) コマンドレットを使用して安全な文字列に変換されたプレーンテキスト文字列。

>[!Note]
>また、Azure portal から生成されたセキュリティ PIN を指定する必要もあります。 PIN を生成するには、[Recovery Services コンテナー] ブレードで **[設定]**  ->  **[プロパティ]**  ->  **[セキュリティ PIN]** に移動し、 **[生成]** を選択します。

## <a name="next-steps"></a>次のステップ

MARS エージェントを使用したオンプレミスのバックアップの展開と管理を、PowerShell を使用して行う方法について、[詳しく学習](../backup-client-automation.md)します。

