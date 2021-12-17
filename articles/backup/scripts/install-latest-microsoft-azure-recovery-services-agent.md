---
title: サンプル スクリプト - オンプレミスの Windows サーバーに最新の MARS エージェントをインストールする
description: スクリプトを使用して、ストレージ アカウント内にあるオンプレミスの Windows サーバーに最新の MARS エージェントをインストールする方法について説明します。
ms.topic: sample
ms.date: 06/23/2021
ms.openlocfilehash: 1080149d33eebed160449865d58f422e6eba36b1
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2021
ms.locfileid: "112560842"
---
# <a name="powershell-script-to-install-the-latest-mars-agent-on-an-on-premises-windows-server"></a>オンプレミスの Windows サーバーに最新の MARS エージェントをインストールするための PowerShell スクリプト

このスクリプトは、オンプレミスの Windows サーバーに最新の MARS エージェントをインストールするために使用します。

## <a name="sample-script"></a>サンプル スクリプト

```azurepowershell
<#

.SYNOPSIS
Sets MARS agent

.DESCRIPTION
Sets MARS agent

.ROLE
Administrators

#>
Set-StrictMode -Version 5.0
$ErrorActionPreference = "Stop"
Try {
    $agentPath = $env:TEMP + '\MARSAgentInstaller.exe'
    Invoke-WebRequest -Uri 'https://aka.ms/azurebackup_agent' -OutFile $agentPath
    & $agentPath /q | out-null

    $env:PSModulePath = (Get-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Session Manager\Environment' -Name PSModulePath).PSModulePath
    $azureBackupModuleName = 'MSOnlineBackup'
    $azureBackupModule = Get-Module -ListAvailable -Name $azureBackupModuleName
    if ($azureBackupModule) {
        $true
    }
    else {
        $false
    }
}
Catch {
    if ($error[0].ErrorDetails) {
        throw $error[0].ErrorDetails
    }
    throw $error[0]
}

```

## <a name="next-steps"></a>次のステップ

PowerShell を使用して、MARS エージェントを使用してオンプレミスのバックアップをデプロイおよび管理する方法について[説明](../backup-client-automation.md)します。