---
title: Azure Stack への接続 | Microsoft Docs
description: Azure Stack に接続する方法について説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/06/2018
ms.author: mabrigg
ms.openlocfilehash: c1932f2ed0486fb56e467466c0fed53702e8f9b0
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248688"
---
# <a name="connect-to-azure-stack-development-kit"></a>Azure Stack Development Kit に接続する

*適用対象: Azure Stack Development Kit*

リソースを管理するにはまず、Azure Stack Development Kit に接続する必要があります。 この記事では、開発キットに接続するために必要な手順を説明します。 次の接続方法のうちどちらかを使用できます。

* [リモート デスクトップ接続](#connect-with-remote-desktop)。 リモート デスクトップ接続を使用して接続する場合、単一のユーザーが開発キットにすばやく接続できます。
* [仮想プライベート ネットワーク (VPN)](#connect-with-vpn)。 VPN を使用して接続する場合、Azure Stack インフラストラクチャの外部のクライアントから複数のユーザーが同時に接続できます。 VPN 接続には、いくつかのセットアップが必要です。

<a name="connect-to-azure-stack-with-remote-desktop"></a>
##  <a name="connect-to-azure-stack-by-using-remote-desktop-connection"></a>リモート デスクトップ接続を使用して Azure Stack に接続する

リモート デスクトップ接続では、同時に 1 人のユーザーがオペレーター ポータルまたはユーザー ポータルでリソースを管理できます。

1. リモート デスクトップ接続を開き、開発キットに接続します。 ユーザー名には「**AzureStack\AzureStackAdmin**」を入力してください。 Azure Stack をセットアップするときに指定したオペレーター パスワードを使用します。  

2. 開発キット コンピューターでサーバー マネージャーを開きます。 **[ローカル サーバー]** を選択し、**[Internet Explorer のセキュリティ強化]** チェック ボックスをオフにして、サーバー マネージャーを閉じます。

3. [ユーザー ポータル](azure-stack-key-features.md#portal)を開くには、https://portal.local.azurestack.external/ に移動します。 ユーザー資格情報を使用してサインインします。 Azure Stack の[オペレーター ポータル](azure-stack-key-features.md#portal)を開くには、https://adminportal.local.azurestack.external/ に移動します。 インストール時に指定した Azure Active Directory (Azure AD) の資格情報を使用してサインインします。

<a name="connect-to-azure-stack-with-vpn"></a>
## <a name="connect-to-azure-stack-by-using-vpn"></a>VPN を使用して Azure Stack に接続する

Azure Stack Development Kit に対して、分割トンネルの VPN 接続を確立することができます。 VPN 接続を使用して、Azure Stack のオペレーター ポータル、ユーザー ポータル、およびローカルにインストールされているツール (Visual Studio、PowerShell など) にアクセスして、Azure Stack リソースを管理できます。 VPN 接続は、Azure AD デプロイと Active Directory フェデレーション サービス (AD FS) デプロイでサポートされています。 VPN 接続は、同時に複数のクライアントが Azure Stack に接続できます。

> [!NOTE]
> VPN 接続では、Azure Stack インフラストラクチャ VM には接続できません。

### <a name="prerequisites"></a>前提条件

1. [Azure Stack 互換の Azure PowerShell](azure-stack-powershell-install.md) をローカル コンピューターにインストールします。  
2. [Azure Stack を操作するために必要なツール](azure-stack-powershell-download.md)をダウンロードします。

### <a name="set-up-vpn-connectivity"></a>VPN 接続の設定

開発キットへの VPN 接続を作成するには、Windows ベースのローカル コンピューター上で管理者として Windows PowerShell を開きます。 その後、次のスクリプトを実行します。IP アドレスとパスワードの値は実際の環境に合わせて更新してください。

```PowerShell
# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1

# Add the development kit computer’s host IP address and certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment.

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

![ネットワーク接続](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Azure Stack への接続

次のどちらかの方法で、Azure Stack インスタンスに接続します。  

* `Connect-AzsVpn ` コマンドを使用する。
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  プロンプトに従って Azure Stack ホストを信頼し、**AzureStackCertificateAuthority** の証明書をローカル コンピューターの証明書ストアにインストールします  (プロンプトが PowerShell ウィンドウに隠れている場合があります)。

* ローカル コンピューターの **[ネットワーク設定]** > **[VPN]** > **[azurestack]** > **[接続]** の順に選択します。 サインイン プロンプトで、ユーザー名 (**AzureStack\AzureStackAdmin**) とパスワードを入力します。

### <a name="test-vpn-connectivity"></a>VPN 接続をテストする

ポータルの接続をテストするには、Web ブラウザーを開き、ユーザー ポータル (https://portal.local.azurestack.external/) またはオペレーター ポータル (https://adminportal.local.azurestack.external/) のいずれかに移動します。 サインインしてリソースを作成します。  

## <a name="next-steps"></a>次の手順

[Azure Stack ユーザーが仮想マシンを使用できるようにする](azure-stack-tutorial-tenant-vm.md)
