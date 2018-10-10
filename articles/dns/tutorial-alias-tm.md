---
title: チュートリアル - Traffic Manager でドメインの頂点名をサポートするための Azure DNS エイリアス レコードを作成する
description: このチュートリアルでは、Traffic Manager でのドメイン頂点名の使用をサポートするために Azure DNS エイリアス レコードを構成する方法を示します。
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 4a5d8968861f6f2938e605d2f7106529ca401df4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967440"
---
# <a name="tutorial-configure-an-alias-record-to-support-apex-domain-names-with-traffic-manager"></a>チュートリアル: Traffic Manager で頂点のドメイン名をサポートするエイリアス レコードを構成する 

Traffic Manager プロファイルを参照するためのドメイン名の頂点 (contoso.com など) に対するエイリアス レコードを作成することができます。 したがって、サービスのリダイレクトを使用する代わりに、ゾーンから直接 Traffic Manager プロファイルを参照するように Azure DNS を構成できます。 


このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ホスト VM とネットワーク インフラストラクチャを作成する
> * Traffic Manager プロファイルの作成
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
2. ポータルの左上で、**[リソースの作成]** をクリックし、検索ボックスに「*resource group*」と入力し、**RG-DNS-Alias-TM** という名前のリソース グループを作成します。
3. **[リソースの作成]** をクリックし、**[ネットワーキング]** をクリックして、**[仮想ネットワーク]** をクリックします。
4. **VNet-Server** という名前の仮想ネットワークを作成し、**RG-DNS-Alias-TM** リソース グループにそれを配置して、サブネットに **SN-Web** という名前を付けます。

## <a name="create-two-web-server-virtual-machines"></a>Web サーバー仮想マシンを 2 つ作成する
1. **[リソースの作成]** をクリックし、**[Windows Server 2016 VM]** をクリックします。
2. 名前として「**Web-01**」と入力し、この VM を **RG-DNS-Alias-TM** リソース グループに配置します。 ユーザー名とパスワードを入力し、**[OK]** をクリックします。
3. **[サイズ]** では、8 GB RAM の SKU を選択します。
4. **[設定]** で、**VNet-Server** 仮想ネットワークと **SN-Web** サブネットを選択します。
5. **[パブリック IP アドレス]** をクリックし、**[割り当て]** で **[静的]** をクリックして、**[OK]** をクリックします。
6. パブリック受信ポートでは、**[HTTP]**、**[HTTPS]**、および **[RDP (3389)]** を選択し、**[OK]** をクリックします。
7. [概要] ページで **[作成]** をクリックします。

   完了するまで数分かかります。
6. この手順を繰り返して、**Web-02** という名前の別の仮想マシンを作成します。

### <a name="add-a-dns-label"></a>DNS ラベルを追加する
Traffic Manager でパブリック IP アドレスを使用するには、DNS ラベルが必要です。
1. **RG-DNS-Alias-TM** リソース グループで、**Web-01-ip** パブリック IP アドレスをクリックします。
2. **[設定]** で **[構成]** をクリックします。
3. DNS 名ラベルのテキスト ボックスに、「**web01pip**」と入力します。
4. **[Save]** をクリックします。

**Web-02-ip** パブリック IP アドレスに対してこの手順を繰り返し、DNS 名ラベルとして **web02pip** を使用します。

### <a name="install-iis"></a>IIS のインストール

**Web-01** と **Web-02** の両方に、IIS をインストールします。

1. **Web-01** に接続してサインインします。
2. [サーバー マネージャー] ダッシュボードから **[役割と機能の追加]** をクリックします。
3. **[次へ]** を 3 回クリックし、**[サーバーの役割]** ページで、**[Web サーバー (IIS)]** を選択します。
4. **[機能の追加]** をクリックし、**[次へ]** をクリックします。
5. **[次へ]** を 4 回クリックして、**[インストール]** をクリックします。

   これが完了するまでに数分かかります。
6. インストールが完了したら、**[閉じる]** をクリックします。
7. Web ブラウザーを開き、**localhost** を参照して既定の IIS Web ページが表示されることを確認します。

このプロセスを繰り返し、**Web-02** に IIS をインストールします。


## <a name="create-a-traffic-manager-profile"></a>Traffic Manager プロファイルの作成

Due 

1. **RG-DNS-Alias-TM** リソース グループを開き、**Web-01-ip** パブリック IP アドレスをクリックします。 後で使うので IP アドレスを書き留めておきます。 **Web-02-ip** パブリック IP アドレスについても同じようにします。
1. **[リソースの作成]** をクリックし、**[ネットワーキング]** をクリックして、**[Traffic Manager プロファイル]** をクリックします。
2. 名前に「**TM-alias-test**」と入力して、**RG-DNS-Alias-TM** リソース グループに配置します。
3. **Create** をクリックしてください。
4. デプロイが完了したら、**[リソースに移動]** をクリックします。
5. Traffic Manager プロファイルのページの **[設定]** で、**[エンドポイント]** をクリックします。
6. **[追加]** をクリックします。
7. **[種類]** で **[外部エンドポイント]** を選択し、**[名前]** に「**EP-Web01**」と入力します。
8. **[完全修飾ドメイン名 (FQDN) または IP]** テキスト ボックスに、前に書き留めた **Web-01-ip** の IP アドレスを入力します。
9. 他のリソースと同じ **[場所]** を選択して、**[OK]** をクリックします。

この手順を繰り返して、**Web-02** エンドポイントを追加します。前に書き留めた **Web-02-ip** の IP アドレスを使用します。

## <a name="create-an-alias-record"></a>エイリアス レコードを作成する

Traffic Manager プロファイルを指すエイリアス レコードを作成します。

1. Azure DNS ゾーンをクリックし、ゾーンを開きます。
2. **[レコード セット]** をクリックします。
3. **[名前]** テキスト ボックスは、ドメイン名の頂点 (contoso.com など) を表す空のままにします。
4. **[Type]\(種類\)** は **A** レコードのままにします。
5. **[Alias Record Set]\(エイリアス レコード セット\)** チェック ボックスをオンにします。
6. **[Choose Azure service]\(Azure サービスの選択\)** をクリックして、**TM-alias-test** Traffic Manager プロファイルを選択します。

## <a name="test-the-alias-record"></a>エイリアス レコードをテストする

1. Web ブラウザーから、ドメイン名の頂点 (contoso.com など) を参照します。 IIS の既定の Web ページが表示されます。 Web ブラウザーを閉じます。
2. **Web-01** 仮想マシンをシャットダウンし、シャットダウンが完了するまで数分待ちます。
3. 新しい Web ブラウザーを開いて、ドメイン名の頂点を再度参照します。
4. Traffic Manager が状況を処理して、トラフィックを **Web-02** に転送するため、再び既定の IIS ページが表示されるはずです。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルで作成したリソースが不要になったら、**RG-DNS-Alias-TM** リソース グループを削除すればすべてのリソースを削除できます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、頂点のドメイン名を使用して Traffic Manager プロファイルを参照するためのエイリアス レコードを作成しました。 Azure DNS と Web アプリについて詳しくは、Web アプリのチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [カスタム ドメインにおける Web アプリの DNS レコードの作成](./dns-web-sites-custom-domain.md)
