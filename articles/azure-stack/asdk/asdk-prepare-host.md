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
ms.date: 01/21/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: 36012c023025b8304dfaf9cc63997f600ef6cbe8
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2019
ms.locfileid: "55962725"
---
# <a name="prepare-the-asdk-host-computer"></a>ASDK ホスト コンピューターの準備
ASDK をホスト コンピューターにインストールするには、ASDK ホストがインストール用に準備されている必要があります。 開発キットのホスト コンピューターが準備されると、CloudBuilder.vhdx 仮想マシンのハード ドライブから起動して ASDK のデプロイが開始します。

## <a name="prepare-the-development-kit-host-computer"></a>開発キットのホスト コンピューターの準備
ASDK をホスト コンピューターにインストールする前に、ASDK ホスト コンピューター環境を準備する必要があります。
1. 開発キットのホスト コンピューターにローカル管理者としてサインインします。
2. CloudBuilder.vhdx ファイルが C:\ ドライブのルート (C:\CloudBuilder.vhdx) に移動してあることを確認します。
3. 次のスクリプトを実行して、開発キットのインストーラー ファイル (asdk-installer.ps1) を [Azure Stack の GitHub ツール リポジトリ](https://github.com/Azure/AzureStack-Tools)から開発キットのホスト コンピューターの **C:\AzureStack_Installer** フォルダーにダウンロードします。

   > [!IMPORTANT]
   > ASDK をインストールするたびに、必ず asdk-installer.ps1 ファイルをダウンロードしてください。 このスクリプトは頻繁に変更されるため、各 ASDK デプロイに対して最新バージョンを使用する必要があります。 古いバージョンのスクリプトは最新リリースでは動作しない可能性があります。

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

6. **[オプション設定]** ページで、開発キットのホスト コンピューターのローカル管理者アカウント情報を指定し、**[次へ]** をクリックします。<br><br>この手順でローカル管理者の資格情報を指定しない場合は、開発キットのセットアップの一環としてコンピューターを再起動した後、ホストに直接または KVM でアクセスする必要があります。

   ![](media/asdk-prepare-host/3.PNG)

    次のオプションの設定の値を指定することもできます。
    - **[コンピューター名]**: このオプションは、開発キット ホストの名前を設定します。 名前は FQDN の要件に準拠している必要があり、長さは 15 文字以下にする必要があります。 既定は、Windows によって生成されたランダムなコンピューター名です。
    - **[Static IP configuration] (静的 IP 構成)**: 静的 IP アドレスを使用するようにデプロイを設定します。 それ以外の場合、インストーラーが cloudbuilder.vhdx から再起動されるときに、ネットワーク インターフェイスが DHCP と構成されます。 静的 IP 構成を使用することを選択した場合は、追加オプションが表示され、そこで次の操作も行う必要があります。
      - ネットワーク アダプターを選択します。 **[次へ]** をクリックする前に、アダプターに接続できることを確認します。
      - 表示されている **[IP アドレス]**、**[ゲートウェイ]**、および **[DNS]** 値が正しいことを確認し、**[次へ]** をクリックします。
13. **[次へ]** をクリックして、準備プロセスを開始します。
14. 準備が **[完了]** と示されたら、**[次へ]** をクリックします。

    ![](media/asdk-prepare-host/4.PNG)

15. **[今すぐ再起動する]** をクリックして開発キットのホスト コンピューターを cloudbuilder.vhdx から起動し、[デプロイ処理を続行](asdk-install.md)します。

    ![](media/asdk-prepare-host/5.PNG)


## <a name="next-steps"></a>次の手順
[ASDK のインストール](asdk-install.md)
