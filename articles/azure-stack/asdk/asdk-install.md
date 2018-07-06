---
title: Azure Stack Development Kit (ASDK) のインストール | Microsoft Docs
description: Azure Stack Development Kit (ASDK) をインストールする方法について説明します。
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
ms.openlocfilehash: 74a81901c8ad38a84357a9f3c2e1d948aa81e8bc
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2018
ms.locfileid: "37084333"
---
# <a name="install-the-azure-stack-development-kit-asdk"></a>Azure Stack Development Kit (ASDK) のインストール
[ASDK ホスト コンピューターの準備](asdk-prepare-host.md)ができたら、この記事の次の手順に従って ASDK を CloudBuilder.vhdx イメージにデプロイできます。

## <a name="install-the-asdk"></a>ASDK のインストール
この記事の手順は、**asdk-installer.ps1** PowerShell スクリプトをダウンロードして実行することによって提供されるグラフィカル ユーザー インターフェイス (GUI) を使って ASDK をデプロイする方法を示しています。

> [!NOTE]
> Azure Stack Development Kit のインストーラー ユーザー インターフェイスは、WCF および PowerShell に基づくオープン ソースのスクリプトです。


1. ホスト コンピューターが CloudBuilder.vhdx イメージで正常に起動した後、ASDK インストールの[開発キットのホスト コンピューターを準備](asdk-prepare-host.md)するときに指定した管理者資格情報を使ってログインします。 これは、開発キット ホストのローカル管理者の資格情報と同じである必要があります。
2. 管理者特権で PowerShell コンソールを開き、**&lt;drive letter>\AzureStack_Installer\asdk-installer.ps1** スクリプト (現時点では CloudBuilder.vhdx イメージの C:\ 以外のドライブにある可能性があります) を実行します。 **[インストール]** をクリックします。

    ![](media/asdk-install/1.PNG) 

3. ID プロバイダーの **[タイプ]** ドロップダウン ボックスで、**[Azure クラウド]** または **[AD FS]** を選択します。 **[ローカル管理者のパスワード]** の **[パスワード]** ボックスに、(現在の構成済みのローカル管理者パスワードと同じ) ローカル管理者パスワードを入力し、**[次へ]** をクリックします。
    - **Azure クラウド**: Azure Active Directory (Azure AD) を ID プロバイダーとして構成します。 このオプションを使うには、インターネット接続、Azure AD ディレクトリ テナントのフル ネーム (*domainname*.onmicrosoft.com 形式) または Azure AD 確認済みカスタム ドメイン名、および指定したディレクトリのグローバル管理者資格情報が必要です。 
    - **AD FS**: 既定のスタンプ ディレクトリ サービスが ID プロバイダーとして使われます。 サインインに使用する既定のアカウントは azurestackadmin@azurestack.local であり、使用するパスワードは、セットアップの一部として指定したものです。

    ![](media/asdk-install/2.PNG) 
    
    > [!NOTE]
    > AD FS を ID プロバイダーとして使って、インターネット接続されていない Azure Stack 環境を使う場合でも、最適な結果を得るには、インターネットへの接続中に ASDK をインストールすることが最良です。 そうすることにより、開発キット インストールに含まれている Windows Server 2016 の評価バージョンがデプロイ時にアクティブ化されます。
4. 開発キットに使用するネットワーク アダプターを選択して、**[次へ]** をクリックします。

    ![](media/asdk-install/3.PNG)

5. BGPNAT01 仮想マシン用に DHCP または静的なネットワーク構成を選択します。
    > [!TIP]
    > BGPNAT01 VM は、Azure Stack 用の NAT 機能と VPN 機能を提供するエッジ ルーターです。

    - **[DHCP]** (既定): 仮想マシンが DHCP サーバーから IP ネットワークの構成を取得します。
    - **[静的]**: DHCP が Azure Stack に、インターネットにアクセスするための有効な IP アドレスを割り当てることができない場合にのみこのオプションは使用されます。 **静的 IP アドレスは、CIDR 形式のサブネット マスク長を使って指定する必要があります (例: 10.0.0.5/24)**。
    - 有効な**タイム サーバー IP** アドレスを入力します。 この必須フィールドでは、開発キットによって使われるタイム サーバーを設定します。 このパラメーターは、有効なタイム サーバーの IP アドレスとして指定する必要があります。 サーバー名はサポートされていません。

      > [!TIP]
      > タイム サーバーの IP アドレスを検索するには、[pool.ntp.org](http://pool.ntp.org) にアクセスするか、time.windows.com に ping を実行します。 

    - **任意で**、次の値を設定します。
        - **[VLAN ID]**: VLAN ID を設定します。 このオプションは、ホストと AzS-BGPNAT01 が物理ネットワーク (およびインターネット) にアクセスするために VLAN ID を構成する必要がある場合にのみ使用します。 
        - **[DNS Forwarder]\(DNS フォワーダ\)**: DNS サーバーは Azure Stack のデプロイの一部として作成されます。 ソリューション内のコンピューターにスタンプ外の名前解決を許可するには、既存のインフラストラクチャの DNS サーバーを提供します。 スタンプ内の DNS サーバーが、このサーバーに不明な名前解決の要求を送信します。

    ![](media/asdk-install/4.PNG)

6. **[Verifying network interface card properties]\(ネットワーク インターフェイス カードのプロパティを確認しています\)** ページに、進行状況バーが表示されます。 検証が完了したら、**[次へ]** をクリックします。

    ![](media/asdk-install/5.PNG)

9. **[概要]** ページで、**[Deploy]\(デプロイ\)** をクリックして、開発キットのホスト コンピューターへの ASDK のインストールを開始します。

    ![](media/asdk-install/6.PNG)

    > [!TIP]
    > ここで、開発キットのインストールに使用される PowerShell のセットアップ コマンドをコピーすることもできます。 これは、[PowerShell を使って ASDK をホスト コンピューターに再デプロイする](asdk-deploy-powershell.md)必要がある場合に役立ちます。

10. Azure AD デプロイを実行している場合、セットアップを開始して数分後に、Azure AD のグローバル管理者アカウントの資格情報を入力するように求めるメッセージが表示されます。

    ![](media/asdk-install/7.PNG)

11. デプロイ処理には数時間かかります。その間に、ホスト コンピューターは自動的に 1 回再起動されます。 デプロイの進行状況を監視する場合は、開発キットのホストが再起動された後に、azurestack\AzureStackAdmin としてサインインします。 デプロイが成功した場合、PowerShell コンソールには、**[COMPLETE: Action 'Deployment']\(完了: アクション 'デプロイ'\)** と表示されます。 
    > [!IMPORTANT]
    > コンピューターがドメインに参加した後に、ローカル管理者としてサインインした場合、デプロイの進行状況は表示されません。 デプロイは再実行せず、代わりに azurestack\AzureStackAdmin としてサインインし、それが実行中であることを確認します。

    ![](media/asdk-install/8.PNG)

これで、ASDK を正常にインストールしました。

なんらかの理由でデプロイが失敗した場合は、最初から[デプロイし直す](asdk-redeploy.md)か、同じ管理者特権の PowerShell ウィンドウで次の PowerShell コマンドを使って、前回の成功した手順からデプロイを再開することができます。

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

## <a name="next-steps"></a>次の手順
[デプロイ後の構成](asdk-post-deploy.md)
