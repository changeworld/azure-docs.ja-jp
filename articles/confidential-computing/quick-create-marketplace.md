---
title: クイックスタート - Marketplace で Azure Confidential Computing 仮想マシンを作成する
description: Marketplace でコンフィデンシャル コンピューティング仮想マシンをすばやく作成する方法を説明します。実際に自分でデプロイしてみましょう。
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 06/13/2021
ms.author: JenCook
ms.openlocfilehash: 1bf3dd7fadea22c4266f87373c09c16f08349f7c
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "113107255"
---
# <a name="quickstart-deploy-an-azure-confidential-computing-vm-in-the-marketplace"></a>クイック スタート:Marketplace で Azure Confidential Computing VM をデプロイする

Intel SGX を使用した仮想マシン (VM) を Azure portal から作成して、Azure Confidential Computing を体験してみましょう。 オプションで、Open Enclave Software (SDK) を使用して構築されたエンクレーブ アプリケーションをテストできます。 

このチュートリアルは、テンプレート構成を使用したコンフィデンシャル コンピューティング仮想マシンのデプロイに関心がある読者にお勧めします。 それ以外の場合は、[ポータルまたは CLI を使用して](quick-create-portal.md)標準の Azure Virtual Machine のデプロイ フローに従うことをお勧めします。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、始める前に[アカウントを作成](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)してください。

> [!NOTE]
> 無料試用版アカウントでは、このチュートリアルで使用されている仮想マシンを利用できません。 従量課金制サブスクリプションにアップグレードしてください。


## <a name="sign-in-to-azure"></a>Azure へのサインイン

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. 一番上にある **[+ リソースの作成]** を選択します。

1. **[Get Started]\(作業の開始\)** の既定のペインで、 **[Azure Confidential Computing (Virtual Machine)]\(Azure Confidential Computing (仮想マシン)\)** を検索します。

1. **[Azure Confidential Computing (Virtual Machine)]\(Azure Confidential Computing (仮想マシン)\)** テンプレートをクリックします。

    ![VM をデプロイする](media/quick-create-marketplace/portal-search-marketplace.png)

1. 仮想マシンのランディング ページで、 **[作成]** を選択します。


## <a name="configure-a-confidential-computing-virtual-machine"></a>コンフィデンシャル コンピューティング仮想マシンを構成する

1. **[基本]** タブで、対象の **サブスクリプション** と **リソース グループ** を選択します (このテンプレートをデプロイするには、グループが空になっている必要があります)。

1. **[仮想マシン名]** に、新しい VM の名前を入力します。

1. 次の値を入力または選択します。

   * **[リージョン]** :適切な Azure リージョンを選択します。

        > [!NOTE]
        > コンフィデンシャル コンピューティング仮想マシンを実行できるのは、特定のリージョンにある特殊なハードウェアのみです。 DCsv2 シリーズ VM が利用できる最新のリージョンについては、[リージョン別の提供状況](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)に関するページを参照してください。

1. 仮想マシンに使用するオペレーティング システム イメージを構成します。 この設定では、Gen 2 の VM とイメージのデプロイのみがサポートされます

    * **[イメージの選択]** : このチュートリアルでは、Ubuntu 18.04 LTS (Gen 2) を選択します。 Windows Server Datacenter 2019、Windows Server Datacenter 2016、または Ubuntu 16.04 LTS を選択することもできます。 そのように選択した場合は、このチュートリアルの中で適宜、対応する指示に従ってください。
   
1. [基本] タブで、次の情報を入力します。

   * **[認証の種類]** : Linux VM を作成する場合は、 **[SSH 公開キー]** を選択します。 

        > [!NOTE]
        > 認証には、SSH 公開キーまたはパスワードを使用する選択肢があります。 安全性が高いのは SSH です。 SSH キーを生成する方法の手順については、[Azure の Linux VM と Mac for Linux VM に SSH キーを作成する方法](../virtual-machines/linux/mac-create-ssh-keys.md)に関するページを参照してください。

    * **[ユーザー名]** : VM の管理者名を入力します。

    * **[SSH 公開キー]** : 該当する場合は、RSA 公開キーを入力します。
    
    * **パスワード**:該当する場合は、認証のパスワードを入力します。
    
1. [Virtual Machine Settings]\(仮想マシンの設定\) タブで、次の情報を入力します。

   * VM の SKU サイズを選択します
   * **DC1s_v2**、**DC2s_v2**、**DC4s_V2** の仮想マシンを選択した場合は、ディスクの種類として **[Standard SSD]** または **[Premium SSD]** を選択します。 **DC8_v2** 仮想マシンの場合、ディスクの種類として選択できるのは **[Standard SSD]** のみです。

   * **[パブリック受信ポート]** : **[選択したポートを許可する]** を選択し、 **[パブリック受信ポートを選択]** ボックスの一覧で **[SSH (22)]** と **[HTTP (80)]** を選択します。 Windows VM をデプロイしている場合は、 **[HTTP (80)]** と **[RDP (3389)]** を選択してください。 このクイックスタートでは、VM に接続するためにこの手順が必要です。
   
    >[!Note]
    > 運用環境のデプロイでは、RDP または SSH ポートの許可は推奨されません。  

     ![受信ポート](media/quick-create-portal/inbound-port-virtual-machine.png)


1. 必要に応じて、 **[監視]** オプションを選択します

1. **[Review + create]\(レビュー + 作成\)** を選択します。

1. **[確認および作成]** ウィンドウで、 **[作成]** を選択します。

> [!NOTE]
> Linux VM をデプロイした方は、次のセクションに進んで、引き続きこのチュートリアルの作業を行ってください。 Windows VM をデプロイした方は、[こちらの手順に従って対象の Windows VM に接続](../virtual-machines/windows/connect-logon.md)してください


## <a name="connect-to-the-linux-vm"></a>Linux VM に接続する

BASH シェルを既に使用している場合、**ssh** コマンドを使用して Azure VM に接続します。 次のコマンドの VM ユーザー名と IP アドレスを置き換えて、Linux VM に接続します。

```bash
ssh azureadmin@40.55.55.555
```

ご利用の VM のパブリック IP アドレスは、Azure portal から、仮想マシンの [概要] セクションにアクセスして確認できます。

:::image type="content" source="media/quick-create-portal/public-ip-virtual-machine.png" alt-text="Azure portal での IP アドレス":::

Windows を使用していて BASH シェルがない場合は PuTTY などの SSH クライアントをインストールします。

1. [PuTTY をダウンロードしてインストールします](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

1. PuTTY を実行します。

1. PuTTY の構成画面で、VM のパブリック IP アドレスを入力します。

1. **[Open]\(開く\)** を選択し、プロンプトでユーザー名とパスワードを入力します。

Linux VM への接続の詳細については、[ポータルを使用して Azure に Linux VM を作成する方法](../virtual-machines/linux/quick-create-portal.md)に関するページを参照してください。

> [!NOTE]
> サーバーのホストキーがレジストリにキャッシュされていないことを示す PuTTY のセキュリティ アラートが表示された場合は、次のオプションから選択します。 このホストを信頼する場合は、 **[Yes]\(はい\)** を選択して PuTTy のキャッシュにキーを追加し、接続を続行します。 キーをキャッシュに追加せずに接続を 1 回だけ実行する場合は、 **[No]\(いいえ\)** を選択します。 このホストを信頼しない場合は、 **[Cancel]\(キャンセル\)** を選択して接続を破棄します。

## <a name="intel-sgx-drivers"></a>Intel SGX ドライバー

> [!NOTE]
> Intel SGX ドライバーは、既に Ubuntu & Windows ギャラリー イメージの一部として含まれています。 ドライバーを特別にインストールする必要はありません。 オプションで、[Intel SGX DCAP ドライバーの一覧](https://01.org/intel-software-guard-extensions/downloads)にアクセスして、イメージに含まれている既存のドライバーを更新することもできます。

## <a name="optional-testing-enclave-apps-built-with-open-enclave-sdk-oe-sdk"></a>オプション: Open Enclave SDK (OE SDK) を使用して構築されたエンクレーブ アプリのテスト <a id="Install"></a>

Ubuntu 18.04 LTS Gen 2 イメージが実行されている DCsv2 シリーズ仮想マシンに対し、手順に従って [OE SDK](https://github.com/openenclave/openenclave) をインストールします。 

仮想マシンが Ubuntu 18.04 LTS Gen 2 で実行されている場合は、[Ubuntu 18.04 のインストール手順](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)に従う必要があります。


> [!NOTE]
> Intel SGX ドライバーは、既に Ubuntu & Windows ギャラリー イメージの一部として含まれています。 ドライバーを特別にインストールする必要はありません。 オプションで、イメージに含まれている既存のドライバーを更新することもできます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、リソース グループ、仮想マシン、およびすべての関連リソースを削除できます。 

仮想マシンのリソース グループを選択し、 **[削除]** をクリックします。 リソース グループの名前を確認してリソースの削除を終了します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、コンフィデンシャル コンピューティング仮想マシンをデプロイし、Open Enclave SDK をインストールしました。 Azure におけるコンフィデンシャル コンピューティング仮想マシンの詳細については、「[仮想マシン上のソリューション](virtual-machine-solutions.md)」を参照してください。 

コンフィデンシャル コンピューティング アプリケーションを作成する方法については、引き続き GitHub の Open Enclave SDK サンプルをご覧ください。 

> [!div class="nextstepaction"]
> [Open Enclave SDK サンプルを作成する](https://github.com/openenclave/openenclave/blob/master/samples/README.md)
