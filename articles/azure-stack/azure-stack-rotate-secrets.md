---
title: "Azure Stack でシークレットをローテーションする | Microsoft Docs"
description: "Azure Stack でシークレットをローテーションする方法について説明します。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 49071044-6767-4041-9EDD-6132295FA551
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2018
ms.author: mabrigg
ms.openlocfilehash: e2e9d93af3889714ade1d0364a6f747c184e6d75
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2018
---
# <a name="rotate-secrets-in-azure-stack"></a>Azure Stack でシークレットをローテーションする

*適用対象: Azure Stack 統合システム*

Azure Stack コンポーネントのパスワードを定期的に更新します。

## <a name="updating-the-passwords-for-the-baseboard-management-controller-bmc"></a>ベースボード管理コントローラー (BMC) のパスワードの更新

ベースボード管理コントローラー (BMC) は、サーバーの物理的な状態を監視します。 BMC のパスワードの更新に関する仕様や手順は、ご利用の OEM (Original Equipment Manufacturer) ハードウェア ベンダーによって異なります。

1. OEM の指示に従って、Azure Stack の物理サーバー上で BMC を更新します。 環境内の各 BMC のパスワードは同じである必要があります。
2. Azure Stack セッションで特権エンドポイントを開きます。 手順については、「[Azure Stack での特権エンドポイントの使用](azure-stack-privileged-endpoint.md)」を参照してください。
3. PowerShell プロンプトが環境に応じて **[IP address or ERCS VM name]: PS>** または **[azs-ercs01]: PS>** に変化したら、`invoke-command` を実行することによって `Set-BmcPassword` を実行します。 パラメーターとして特権エンドポイントのセッション変数を渡します。 例: 

    ```powershell
    # Interactive Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PECred = Get-Credential "<Domain>\CloudAdmin" -Message "PE Credentials" 
    $NewBMCpwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    ```
    
    パスワードをコード行として含む静的な PowerShell バージョンを使用することもできます。
    
    ```powershell
    # Static Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEUser = "<Privileged Endpoint user for exmaple Domain\CloudAdmin>"
    $PEpwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PECred = New-Object System.Management.Automation.PSCredential ($PEUser, $PEpwd) 
    $NewBMCpwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    ```

## <a name="next-steps"></a>次の手順

セキュリティと Azure Stack の詳細については、「[Azure Stack infrastructure security posture (Azure Stack インフラストラクチャのセキュリティ状態)](azure-stack-security-foundations.md)」を参照してください。
