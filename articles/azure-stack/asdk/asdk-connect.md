---
title: Azure Stack への接続 | Microsoft Docs
description: ASDK に接続する方法について説明します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: jeffgilb
ms.reviewer: knithinc
ms.openlocfilehash: 1b5d5a2934205877f0e0c2ac891e62c90e960b3d
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085151"
---
# <a name="connect-to-the-asdk"></a>ASDK に接続する

リソースを管理するにはまず、Azure Stack Development Kit (ASDK) に接続する必要があります。 この記事では、次の接続オプションを使用して ASDK に接続するために必要な手順を説明します。

* [リモート デスクトップ接続 (RDP)](#connect-with-rdp)。 リモート デスクトップ接続を使用して接続する場合、単一のユーザーが開発キットにすばやく接続できます。
* [仮想プライベート ネットワーク (VPN)](#connect-with-vpn)。 VPN を使用して接続する場合、Azure Stack インフラストラクチャの外部のクライアントから複数のユーザーが同時に接続できます。 VPN 接続には、いくつかのセットアップが必要です。

<a name="connect-with-rdp"></a>
## <a name="connect-to-azure-stack-using-rdp"></a>RDP を使用して Azure Stack に接続する

リモート デスクトップ接続では、同時に 1 人のユーザーが、ASDK ホスト コンピューターから Azure Stack 管理ポータルまたはユーザー ポータルでリモート デスクトップ接続を使用してリソースを管理できます。 

> [!TIP]
> このオプションでは、ASDK ホスト コンピューターにサインインしているときに、RDP を再度使用して、ASDK ホスト コンピューター上に作成された仮想マシンにサインインできます。 

1. リモート デスクトップ接続 (mstc.exe) を開き、ASDK ホスト コンピューターにリモートでサインインする権限のあるアカウントを使用して、開発キット ホスト コンピューター IP アドレスに接続します。 既定では、**AzureStack\AzureStackAdmin** に ASDK ホスト コンピューターにリモートでアクセス許可があります。  

2. 開発キットのホスト コンピューターでサーバー マネージャー (ServerManager.exe) を開きます。 **[ローカル サーバー]** を選択し、**[IE セキュリティ強化の構成]** をオフにして、サーバー マネージャーを閉じます。

3. 管理ポータルに **AzureStack\CloudAdmin** としてサインインするか、他の Azure Stack オペレーターの資格情報を使用します。 ASDK 管理ポータルのアドレスは [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external) です。

4. ユーザー ポータルに **AzureStack\CloudAdmin** としてサインインするか、他の Azure Stack ユーザーの資格情報を使用します。 ASDK ユーザー ポータルのアドレスは [https://portal.local.azurestack.external](https://portal.local.azurestack.external) です。

> [!NOTE]
> どのアカウントをいつ使用するかについては、「[ASDK の管理の基本](asdk-admin-basics.md#what-account-should-i-use)」をご覧ください。

<a name="connect-with-vpn"></a>
## <a name="connect-to-azure-stack-using-vpn"></a>VPN を使用して Azure Stack に接続する

ASDK ホスト コンピューターに対する分割トンネルの VPN 接続を確立して、Azure Stack ポータルと、Visual Studio や PowerShell などのローカルにインストールされたツールにアクセスできます。 VPN 接続を使うと、複数のユーザーは、ASDK でホストされている Azure Stack リソースに同時に接続できます。

VPN 接続は、Azure AD と Active Directory フェデレーション サービス (AD FS) の両方のデプロイでサポートされています。

> [!NOTE]
> VPN 接続では、Azure Stack VM には接続*できません*。 VPN 経由で接続している間は、Azure Stack VM に RDP 接続することはできません。

### <a name="prerequisites"></a>前提条件
ASDK への VPN 接続を設定する前に、次の前提条件を満たしていることを確認してください。

- [Azure Stack 互換の Azure PowerShell](asdk-post-deploy.md#install-azure-stack-powershell) をローカル コンピューターにインストールします。  
- [Azure Stack を操作するために必要なツール](asdk-post-deploy.md#download-the-azure-stack-tools)をダウンロードします。

### <a name="set-up-vpn-connectivity"></a>VPN 接続の設定

ASDK への VPN 接続を作成するには、Windows ベースのローカル コンピューター上で管理者として PowerShell を開きます。 その後、次のスクリプトを実行します。IP アドレスとパスワードの値は実際の環境に合わせて更新してください。

```PowerShell
# Change directories to the default Azure Stack tools directory
cd C:\AzureStack-Tools-master

# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1

# Add the development kit host computer’s IP address as the ASDK certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment.

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<operator's password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user.
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

セットアップが成功すると、VPN 接続の一覧に **azurestack** と表示されます。

![ネットワーク接続](media/asdk-connect/vpn.png)  

### <a name="connect-to-azure-stack"></a>Azure Stack への接続

  次のどちらかの方法で、Azure Stack インスタンスに接続します。  

  * `Connect-AzsVpn ` コマンドを使用する。
      
    ```PowerShell
    Connect-AzsVpn `
      -Password $Password
    ```

  * ローカル コンピューターの **[ネットワーク設定]** > **[VPN]** > **[azurestack]** > **[接続]** の順に選択します。 サインイン プロンプトで、ユーザー名 (**AzureStack\AzureStackAdmin**) とパスワードを入力します。

初回の接続では、Azure Stack ルート証明書を **AzureStackCertificateAuthority** からローカル コンピューターの証明書ストアにインストールすることを求めるメッセージが表示されます。 この手順により、信頼されたホストの一覧に、ASDK 証明機関 (CA) が追加されます。 **[はい]** をクリックして証明書をインストールします。

![ルート証明書](media/asdk-connect/cert.png)  
  
  > [!IMPORTANT]
  > プロンプトが PowerShell ウィンドウや他のアプリケーションで隠れている場合があります。

### <a name="test-vpn-connectivity"></a>VPN 接続をテストする

ポータルの接続をテストするには、Web ブラウザーを開き、ユーザー ポータル (https://portal.local.azurestack.external/) または管理ポータル (https://adminportal.local.azurestack.external/) のいずれかに移動します。 

適切なサブスクリプションの資格情報でサインインし、リソースを作成して管理します。  

## <a name="next-steps"></a>次の手順

[トラブルシューティング](asdk-troubleshooting.md)
