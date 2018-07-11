---
title: クイック スタート - Azure portal で Linux VM を作成する | Microsoft Docs
description: このクイック スタートでは、Azure portal を使用して Linux 仮想マシンを作成する方法について説明します
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/03/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 03d4ad081a788299bab563ca6b956edc347732da
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37447960"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-the-azure-portal"></a>クイック スタート: Azure portal で Linux 仮想マシンを作成する

Azure 仮想マシン (VM) は、Azure portal で作成できます。 この方法では、ブラウザー ベースのユーザー インターフェイスを使用して、VM とその関連リソースを作成できます。 このクイック スタートでは、Azure portal を使用して、Ubuntu を実行する Linux 仮想マシン (VM) を Azure にデプロイする方法を示します。 次に、VM の動作を確認するために、VM に SSH 接続し、NGINX Web サーバーをインストールします。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-ssh-key-pair"></a>SSH キー ペアの作成

このクイック スタートを完了するには、SSH キー ペアが必要です。 既存の SSH キー ペアがある場合は、この手順はスキップしてかまいません。

SSH キー ペアを作成して Linux VM にログインするには、Bash シェルから次のコマンドを実行し、画面の指示に従います。 たとえば、[Azure Cloud Shell](../../cloud-shell/overview.md) または [Linux 用 Windows サブシステム](/windows/wsl/install-win10)を使用できます。 コマンド出力に公開キー ファイルの名前が表示されます。 公開キー ファイル (`cat ~/.ssh/id_rsa.pub`) の内容をクリップボードにコピーします。

```bash
ssh-keygen -t rsa -b 2048
```

PuTTy の使用を含む SSH キー ペアの作成方法の詳細については、[Windows で SSH キーを使用する方法](ssh-from-windows.md)に関するページを参照してください。

## <a name="log-in-to-azure"></a>Azure にログインする

Azure Portal (http://portal.azure.com) にログインします

## <a name="create-virtual-machine"></a>仮想マシンの作成

1. Azure portal の左上隅にある **[リソースの作成]** を選択します。

2. Azure Marketplace リソースの一覧の上にある検索ボックスで Canonical の **Ubuntu Server 16.04 LTS** を検索して選択し、**[作成]** を選択します。

3. VM 名 (たとえば、*myVM*) を指定します。ディスクの種類を *[SSD]* のままにし、ユーザー名 (たとえば、*azureuser*) を指定します。

4. が必要です。 **[認証の種類]** で **[SSH 公開鍵]** を選択し、公開キーをテキスト ボックスに貼り付けます。 公開キーの先頭と末尾の空白は削除するように注意してください。

    ![ポータルのブレードで VM に関する基本情報を入力する](./media/quick-create-portal/create-vm-portal-basic-blade.png)

5. リソース グループを**新規作成**し、名前 (たとえば、*myResourceGroup*) を指定します。 目的の**場所**を選択し、**[OK]** を選択します。

4. VM のサイズを選択します。 たとえば、"*計算の種類*" または "*ディスクの種類*" でフィルター処理することができます。 推奨される VM サイズは *D2s_v3* です。

    ![VM のサイズを示すスクリーンショット](./media/quick-create-portal/create-linux-vm-portal-sizes.png)

5. **[設定]** ページの **[ネットワーク]** > **[ネットワーク セキュリティ グループ]** > **[Select public inbound ports]\(パブリック受信ポートの選択\)** の順に移動し、**[HTTP]** と **[SSH (22)]** を選択します。 残りの部分は既定値のままにし、**[OK]** を選択します。

6. 概要ページで、**[作成]** を選択して、VM のデプロイを開始します。

7. 対応する VM が、Azure portal のダッシュボードにピン留めされます。 デプロイが完了すると、VM の概要が自動的に表示されます。

## <a name="connect-to-virtual-machine"></a>仮想マシンへの接続

VM との SSH 接続を作成します。

1. VM の概要ページの **[接続]** ボタンを選択します。 

    ![ポータル 9](./media/quick-create-portal/portal-quick-start-9.png)

2. **[Connect to virtual machine]\(仮想マシンへの接続\)** ページで、ポート 22 を介して DNS 名で接続する既定のオプションをそのまま使用します。 **[VM ローカル アカウントを使用してログインする]** に、接続コマンドが表示されます。 ボタンをクリックしてこのコマンドをコピーします。 SSH 接続コマンドの例を次に示します。

    ```bash
    ssh azureuser@myvm-123abc.eastus.cloudapp.azure.com
    ```

3. Azure Cloud Shell や Bash on Ubuntu on Windows などのシェルに SSH 接続コマンドを貼り付けて、接続を作成します。 

## <a name="install-web-server"></a>Web サーバーのインストール

VM の動作を確認するために、NGINX Web サーバーをインストールします。 パッケージ ソースを更新して最新の NGINX パッケージをインストールするには、SSH セッションから次のコマンドを実行します。

```bash
# update packages
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

完了したら SSH セッションを `exit` し、Azure portal の VM のプロパティに戻ります。


## <a name="view-the-web-server-in-action"></a>動作中の Web サーバーを表示する

NGINX がインストールされ、VM に対しポート 80 が開かれると、Web サーバーにインターネットからアクセスできるようになります。 Web ブラウザーを開いて、VM のパブリック IP アドレスを入力します。 パブリック IP アドレスは、VM の概要ページ、または受信ポート規則を追加する *[ネットワーキング]* ページの上部に表示されます。

![NGINX の既定のサイト](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、リソース グループ、仮想マシン、およびすべての関連リソースを削除できます。 これを行うには、仮想マシンのリソース グループを選択し、**[削除]** を選択して、削除するリソース グループの名前を確認します。

## <a name="next-steps"></a>次の手順

このクイック スタートでは、単純な仮想マシンをデプロイし、ネットワーク セキュリティ グループと規則を作成し、基本的な Web サーバーをインストールしました。 Azure 仮想マシンの詳細については、Linux VM のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [Azure Linux 仮想マシンのチュートリアル](./tutorial-manage-vm.md)
