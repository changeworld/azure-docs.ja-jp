---
title: Azure Stack Development Kit (ASDK) ホスト コンピューターの準備 | Microsoft Docs
description: ASDK のインストール用に Azure Stack Development Kit (ASDK) ホスト コンピューターを準備する方法について説明します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 5de25f574cb876701ffce74f1dca8c4bb9764157
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
ms.locfileid: "30165672"
---
# <a name="prepare-the-asdk-host-computer"></a>ASDK ホスト コンピューターの準備
ASDK をホスト コンピューターにインストールする前に、インストール用の ASDK 環境を準備する必要があります。 開発キットのホスト コンピューターが準備されると、CloudBuilder.vhdx 仮想マシンのハード ドライブから起動して ASDK のデプロイが開始します。

## <a name="prepare-the-development-kit-host-computer"></a>開発キットのホスト コンピューターの準備
ASDK をホスト コンピューターにインストールする前に、ASDK ホスト コンピューター環境を準備する必要があります。
1. 開発キットのホスト コンピューターにローカル管理者としてサインインします。
2. CloudBuilder.vhdx ファイルが C:\ ドライブのルート (C:\CloudBuilder.vhdx) に移動してあることを確認します。
3. 次のスクリプトを実行して、開発キットのインストーラー ファイル (asdk-installer.ps1) を [Azure Stack の GitHub ツール リポジトリ](https://github.com/Azure/AzureStack-Tools)から開発キットのホスト コンピューターの **C:\AzureStack_Installer** フォルダーにダウンロードします。

  ```powershell
  # Variables
  $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
  $LocalPath = 'C:\AzureStack_Installer'
  # Create folder
  New-Item $LocalPath -Type directory
  # Enforce usage of TLSv1.2 to download from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  # Download file
  Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
  ```

4. 管理者特権で PowerShell コンソールから **C:\AzureStack_Installer\asdk-installer.ps1** スクリプトを開始し、**[Prepare Environment]\(環境の準備\)** をクリックします。

    ![](media/asdk-prepare-host/1.PNG) 

5. インストーラーの **[Select Cloudbuilder vhdx]\(Cloudbuilder vhdx の選択\)** ページで、[前の手順](asdk-download.md)でダウンロードおよび展開した **cloudbuilder.vhdx** ファイルを探して選択します。 このページでは、開発キットのホスト コンピューターに他のドライバーを追加する必要がある場合、オプションで **[ドライバーの追加]** チェック ボックスをオンにすることもできます。 **[次へ]** をクリックします。  

    ![](media/asdk-prepare-host/2.PNG)

6. **[オプション設定]** ページで、開発キットのホスト コンピューターのローカル管理者アカウント情報を指定し、**[次へ]** をクリックします。 次のオプションの設定の値を指定することもできます。
  - **[コンピューター名]**: このオプションでは、開発キットのホスト名が設定されます。 名前は FQDN の要件に準拠している必要があり、長さは 15 文字以下にする必要があります。 既定は、Windows によって生成されたランダムなコンピューター名です。
  - **[タイム ゾーン]**: 開発キットのホストのタイム ゾーンが設定されます。 既定は、(UTC-8:00) 太平洋標準時 (米国およびカナダ) です。
  - **[Static IP configuration]\(静的な IP 構成\)**: デプロイが静的 IP アドレスを使用するよう設定されます。 それ以外の場合、インストーラーが cloudbuilder.vhx から再起動されるときに、ネットワーク インターフェイスが DHCP と構成されます。

    ![](media/asdk-prepare-host/3.PNG)

  > [!IMPORTANT]
  > この手順でローカル管理者の資格情報を指定しない場合は、開発キットのセットアップの一環としてコンピューターを再起動した後、ホストに直接または KVM でアクセスする必要があります。

7. 前の手順で静的 IP を構成した場合、次を実行する必要があります。
    - ネットワーク アダプターを選択します。 **[次へ]** をクリックする前に、アダプターに接続できることを確認します。
    - **[IP アドレス]**、**[ゲートウェイ]**、および **[DNS]** 値が正しいことを確認し、**[次へ]** をクリックします。
13. **[次へ]** をクリックして、準備プロセスを開始します。
14. 準備が **[完了]** と示されたら、**[次へ]** をクリックします。
15. **[今すぐ再起動する]** をクリックして開発キットのホスト コンピューターを cloudbuilder.vhdx から起動し、[デプロイ処理を続行](asdk-install.md)します。

    ![](media/asdk-prepare-host/4.PNG)


## <a name="next-steps"></a>次の手順
[ASDK のインストール](asdk-install.md)