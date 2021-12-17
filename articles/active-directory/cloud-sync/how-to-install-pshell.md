---
title: コマンド ライン インターフェイス (CLI) と PowerShell を使用して Azure AD Connect クラウド プロビジョニング エージェントをインストールする
description: PowerShell コマンドレットを使用して Azure AD Connect クラウド プロビジョニング エージェントをインストールする方法について説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9587c60cc37badc29a9a9f3ba80a77f6f193a3a9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128583047"
---
# <a name="install-the-azure-ad-connect-provisioning-agent-by-using-a-cli-and-powershell"></a>CLI と PowerShell を使用して Azure AD Connect プロビジョニング エージェントをインストールする
この記事では、PowerShell コマンドレットを使用して Azure Active Directory (Azure AD) Connect プロビジョニング エージェントをインストールする方法を紹介します。
 
>[!NOTE]
>この記事では、コマンド ライン インターフェイス (CLI) を使用したプロビジョニング エージェントのインストールについて説明します。 ウィザードを使用して Azure AD Connect プロビジョニング エージェントをインストールする方法については、「[Azure AD Connect プロビジョニング エージェントをインストールする](how-to-install.md)」を参照してください。

## <a name="prerequisite"></a>前提条件

PowerShell コマンドレットを使用して Azure AD Connect プロビジョニング エージェントをインストールするには、Windows Server で TLS 1.2 を有効にする必要があります。 TLS 1.2 を有効にするには、「[Azure AD Connect クラウド同期の前提条件](how-to-prerequisites.md#tls-requirements)」の手順に従ってください。

>[!IMPORTANT]
>次のインストール手順は、[前提条件](how-to-prerequisites.md)がすべて満たされていることを前提としています。

## <a name="install-the-azure-ad-connect-provisioning-agent-by-using-powershell-cmdlets"></a>PowerShell コマンドレットを使用して Azure AD Connect プロビジョニング エージェントをインストールする 

 1. Azure portal にサインインし、 **[Azure Active Directory]** に移動します。
 1. 左側のメニューで **[Azure AD Connect]** を選びます。
 1. **[プロビジョニングの管理]**  >  **[すべてのエージェントの確認]** の順に選択します。
 1. Azure portal から Azure AD Connect プロビジョニング エージェントをダウンロードします。

    ![オンプレミス エージェントのダウンロードを示すスクリーンショット。](media/how-to-install/install-9.png)</br>

 1. この手順のために、C:\temp フォルダーにエージェントがダウンロードされました。 
 1. プロビジョニング エージェントを quiet モードでインストールします。

       ```
       $installerProcess = Start-Process c:\temp\AADConnectProvisioningAgent.Installer.exe /quiet -NoNewWindow -PassThru 
       $installerProcess.WaitForExit()  
       ```
 1. プロビジョニング エージェント PS モジュールをインポートします。

       ```
       Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.PowerShell.dll" 
       ```
 1. グローバル管理者の資格情報を使用して Azure AD に接続します。 このセクションをカスタマイズして、セキュリティで保護されたストアからパスワードを取得することができます。 

       ```
       $globalAdminPassword = ConvertTo-SecureString -String "Global admin password" -AsPlainText -Force 
    
       $globalAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("GlobalAdmin@contoso.onmicrosoft.com", $globalAdminPassword) 
       
       Connect-AADCloudSyncAzureAD -Credential $globalAdminCreds 
       ```
 1. gMSA アカウントを追加し、ドメイン管理者の資格情報を指定して、既定の gMSA アカウントを作成します。
 
       ```
       $domainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 
    
       $domainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $domainAdminPassword) 
    
       Add-AADCloudSyncGMSA -Credential $domainAdminCreds 
       ```
 1. または、上記のコマンドレットを使用して、事前に作成した gMSA アカウントを指定します。
 
       ```
       Add-AADCloudSyncGMSA -CustomGMSAName preCreatedGMSAName$ 
       ```
 1. ドメイン名を追加します。

       ```
       $contosoDomainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 
    
       $contosoDomainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $contosoDomainAdminPassword) 
    
       Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds 
       ```
 1. または、上記のコマンドレットを使用して、優先ドメイン コントローラーを構成します。

       ```
       $preferredDCs = @("PreferredDC1", "PreferredDC2", "PreferredDC3") 
    
       Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds -PreferredDomainControllers $preferredDCs 
       ```
 1. 前の手順を繰り返してドメインをさらに追加します。 各ドメインのアカウント名とドメイン名を指定してください。
 
 1. サービスを再起動します。
 
       ```
       Restart-Service -Name AADConnectProvisioningAgent  
       ```
 1. Azure portal にアクセスして、クラウド同期構成を作成します。

## <a name="provisioning-agent-gmsa-powershell-cmdlets"></a>プロビジョニング エージェント gMSA PowerShell コマンドレット
エージェントをインストールしたので、さらに詳細なアクセス許可を gMSA に適用できます。 アクセス許可の構成方法とその詳しい手順については、「[Azure AD Connect クラウド プロビジョニング エージェント gMSA PowerShell コマンドレット](how-to-gmsa-cmdlets.md)」を参照してください。

## <a name="next-steps"></a>次のステップ 

- [プロビジョニングとは](what-is-provisioning.md)
- [Azure AD Connect クラウド プロビジョニング エージェント gMSA PowerShell コマンドレット](how-to-gmsa-cmdlets.md)
- [Azure AD Connect クラウド同期とは](what-is-cloud-sync.md)
