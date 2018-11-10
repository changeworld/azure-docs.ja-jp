---
title: Avere vFXT のコントロール パネルにアクセスする - Azure
description: vFXT クラスターとブラウザー ベースの Avere Control Panel に接続して Avere vFXT を構成する方法
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: c48f0d8f7ad34db585f4deae566641b6453357e8
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2018
ms.locfileid: "50670059"
---
# <a name="access-the-vfxt-cluster"></a>vFXT クラスターへのアクセス

設定を変更し、Avere vFXT クラスターを監視するには、Avere Control Panel を使用します。 Avere Control Panel は、クラスターに対するブラウザーベースのグラフィカル インターフェイスです。

vFXT クラスターはプライベート仮想ネットワーク内にあるため、クラスターの管理 IP アドレスに到達するには、SSH トンネルを作成するか、別の方法を使用する必要があります。 基本的な手順は 2 つあります。 

1. ワークステーションとプライベート vnet 間に接続を作成する 
1. クラスターの管理 IP アドレスを使用して Web ブラウザーにコントロール パネルを読み込む 

> [!NOTE] 
> この記事では、クラスター コントローラー、またはクラスターの仮想ネットワーク内にある別の VM にパブリック IP アドレスを設定していることを前提としています。 vnet アクセスのために VPN または ExpressRoute を使用している場合は、[Avere Control Panel への接続](#connect-to-the-avere-control-panel-in-a-browser)に関するセクションに進んでください。

接続する前に、クラスター コントローラーの作成時に使用した SSH 公開キーと秘密キーのペアがローカル コンピューターにインストールされていることを確認します。 ヘルプが必要な場合は、[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) または [Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) の SSH キーのドキュメントをお読みください。  

## <a name="access-with-a-linux-host"></a>Linux ホストによるアクセス

次の形式を使用します。 

ssh -L *local_port*:*cluster_mgmt_ip*:443 *controller_username*@*controller_public_IP* 

このコマンドでは、クラスター コントローラーの IP アドレスを介してクラスターの管理 IP アドレスに接続します。

例:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

SSH 公開キーを使用してクラスターを作成し、一致するキーがクライアント システムにインストールされている場合、認証は自動的に行われます。


## <a name="access-with-a-windows-host"></a>Windows ホストによるアクセス

PuTTY を使用している場合は、**[ホスト名]** フィールドにクラスター コントローラーのユーザー名とその IP アドレスを *自分のユーザー名*@*コントローラーのパブリック IP* という形式で入力します。

例: ``azureuser@203.0.113.51``

**[構成]** パネルで次の手順を行います。

1. 左側にある **[接続]** > **[SSH]** を展開します。 
1. **[Tunnels]\(トンネル\)** をクリックします。 
1. ソース ポート (8443 など) を入力します。 
1. 宛先については、vFXT クラスターの管理 IP アドレスとポート 443 を入力します。 
   例: ``203.0.113.51:443``
1. **[追加]** をクリックします。
1. **[開く]** をクリックします。

![トンネルを追加するためにクリックする場所が署名された Putty アプリケーションのスクリーンショット](media/avere-vfxt-ptty-numbered.png)

SSH 公開キーを使用してクラスターを作成し、一致するキーがクライアント システムにインストールされている場合、認証は自動的に行われます。

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>ブラウザーで Avere Control Panel に接続する

この手順では、Web ブラウザーを使用して、vFXT クラスター上で実行されている構成ユーティリティに接続します。

Web ブラウザーを開き、 https://127.0.0.1:8443 に移動します。 

ブラウザーによっては、**[Advanced]\(詳細設定\)** をクリックして、そのページへのアクセスが安全であることを確認する必要があります。

ユーザー名 `admin` と、クラスターの作成時に指定したパスワードを入力します。

![ユーザー名 'admin' とパスワードが入力された Avere のサインイン ページのスクリーンショット](media/avere-vfxt-gui-login.png)

**[Login]\(ログイン\)** をクリックするか、キーボードの Enter キーを押します。

## <a name="next-steps"></a>次の手順

これでクラスターにアクセスし、[サポート](avere-vfxt-enable-support.md)を有効にすることができるようになりました。
