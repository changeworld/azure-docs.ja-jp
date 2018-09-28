---
title: 'チュートリアル: Azure パブリック IP アドレスを参照する Azure DNS エイリアス レコードを作成する'
description: このチュートリアルでは、Azure パブリック IP アドレスを参照する Azure DNS エイリアス レコードを構成する方法を示します。
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 842015d853790e3ac78214cca6a70becb7f9fadf
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991231"
---
# <a name="tutorial-configure-an-alias-record-to-refer-to-an-azure-public-ip-address"></a>チュートリアル: Azure パブリック IP アドレスを参照するエイリアス レコードを構成する 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ネットワーク インフラストラクチャを作成する
> * Web サーバー仮想マシンを作成する
> * エイリアス レコードを作成する
> * エイリアス レコードをテストする


Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件
テスト対象の Azure DNS でホストできる利用可能なドメイン名が必要です。 このドメインのネーム サーバー (NS) レコードを設定するできるなど、このドメインを完全に制御できる必要があります。

Azure DNS にドメインをホストする手順については、「[チュートリアル: Azure DNS にドメインをホストする](dns-delegate-domain-azure-dns.md)」を参照してください。

このチュートリアルで使用するドメインの例は contoso.com ですが、独自のドメイン名を使用する必要があります。

## <a name="create-the-network-infrastructure"></a>ネットワーク インフラストラクチャを作成する
まず、Web サーバーを配置する VNet とサブネットを作成します。
1. Azure Portal ( http://portal.azure.com ) にサインインします。
2. ポータルの左上で、**[リソースの作成]** をクリックし、検索ボックスに「*resource group*」と入力し、**RG-DNS-Alias-pip** という名前のリソース グループを作成します。
3. **[リソースの作成]** をクリックし、**[ネットワーク]** をクリックし、**[仮想ネットワーク]** をクリックします。
4. **VNet-Server** という名前の仮想ネットワークを作成し、**RG-DNS-Alias-pip** リソース グループに配置し、サブネットに **SN Web** という名前を付けます。

## <a name="create-a-web-server-virtual-machine"></a>Web サーバー仮想マシンを作成する
1. **[リソースの作成]** をクリックし、**[Windows Server 2016 VM]** をクリックします。
2. 名前として「**Web-01**」と入力し、この VM を **RG-DNS-Alias-TM** リソース グループに配置します。 ユーザー名とパスワードを入力し、**[OK]** をクリックします。
3. **[サイズ]** では、8 GB RAM の SKU を選択します。
4. **[設定]** では、**[VNet-Servers]** 仮想ネットワーク、**[SN-Web]** サブネットを選択します。 パブリック受信ポートでは、**[HTTP]**、**[HTTPS]**、および **[RDP (3389)]** を選択し、**[OK]** をクリックします。
5. [概要] ページで **[作成]** をクリックします。

   完了するまで数分かかります。

### <a name="install-iis"></a>IIS のインストール

**Web-01** にIIS をインストールする

1. **Web 01** に接続し、サインインします。
2. [サーバー マネージャー] ダッシュボードから **[役割と機能の追加]** をクリックします。
3. **[次へ]** を 3 回クリックし、**[サーバーの役割]** ページで、**[Web サーバー (IIS)]** を選択します。
4. **[機能の追加]** をクリックし、**[次へ]** をクリックします。
5. **[次へ]** を 4 回クリックして、**[インストール]** をクリックします。

   これが完了するまでに数分かかります。
6. インストールが完了したら、**[閉じる]** をクリックします。
7. Web ブラウザーを開き、**localhost** を参照して既定の IIS Web ページが表示されることを確認します。

## <a name="create-an-alias-record"></a>エイリアス レコードを作成する

パブリック IP アドレスを指すエイリアス レコードを作成します。

1. Azure DNS ゾーンをクリックし、ゾーンを開きます。
2. **[レコード セット]** をクリックします。
3. **[名前]** テキスト ボックス **web01**。
4. **[型]** は **A** レコードのままにします。
5. **[エイリアス レコード セット]** チェック ボックスをクリックします。
6. **[Azure サービスの選択]** をクリックし、**Web-01-ip** パブリック IP アドレスを選択します。

## <a name="test-the-alias-record"></a>エイリアス レコードをテストする

1. **RG-DNS-Alias-pip** リソース グループで、**Web-01** 仮想マシンをクリックします。 パブリック IP アドレスをメモします。
1. Web ブラウザーから、Web01-01 仮想マシンの完全修飾ドメイン名を参照します。 たとえば、**web01.contoso.com** です。 IIS の既定のWeb ページが表示されます。
2. Web ブラウザーを閉じます。
3. **Web 01** 仮想マシンを停止し、再起動します。
4. 再起動したとき、仮想マシンの新しいパブリック IP アドレスをメモします。
5. 新しいブラウザーを開き、Web01-01 仮想マシンの完全修飾ドメイン名をもう一度参照します。 たとえば、**web01.contoso.com** です。
6. これは成功するはずです。エイリアス レコードを使用して、標準の A レコードではなくパブリック IP アドレス リソースを指したからです。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルで作成したリソースが不要になったら、**RG-DNS-Alias-pip** リソース グループを削除すればすべてのリソースを削除できます。


## <a name="next-steps"></a>次の手順

このチュートリアルでは、エイリアス レコードを作成し、Azure パブリック IP アドレスを参照しました。 Azure DNS と Web アプリについて詳しくは、Web アプリのチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [カスタム ドメインにおける Web アプリの DNS レコードの作成](./dns-web-sites-custom-domain.md)
