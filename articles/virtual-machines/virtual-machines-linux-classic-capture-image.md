---
title: "Linux VM のイメージをキャプチャする | Microsoft Docs"
description: "クラシック デプロイ モデルで作成された Linux ベースの Azure の仮想マシン (VM) のイメージをキャプチャする方法について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 17d7ffee-a58e-4290-9de1-64c3cf1ddc05
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/31/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 7453b7b5938a15564110cae31fa878ee3616620a


---
# <a name="how-to-capture-a-classic-linux-virtual-machine-as-an-image"></a>従来の Linux 仮想マシンをイメージとしてキャプチャする方法
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[Resource Manager モデルを使用してこれらの手順を実行する](virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)方法について説明します。

ここでは、Linux を実行する従来の Azure 仮想マシンをキャプチャして、他の仮想マシンを作成する際にイメージとして使用する方法を示します。 このイメージには、仮想マシンに接続された OS ディスクやデータ ディスクが含まれます。 ネットワーク構成は含まれないため、イメージから他の仮想マシンを作成するときは、ネットワーク構成を行う必要があります。

Azure では、イメージは **[イメージ]** に格納されます。アップロードしたすべてのイメージがこの場所に格納されます。 イメージの詳細については、「[Azure の仮想マシン イメージについて][Azure の仮想マシン イメージについて]」をご覧ください。

## <a name="before-you-begin"></a>開始する前に
これらの手順は、すでにクラシック デプロイ モデルを使用して Azure 仮想マシンを作成し、データ ディスクの接続を含め、オペレーティング システムの構成が完了していることを前提としています。 VM を作成する必要がある場合は、[Linux 仮想マシンを作成する方法][Linux 仮想マシンを作成する方法]に関する記事をお読みください。

## <a name="capture-the-virtual-machine"></a>仮想マシンをキャプチャする
1. 任意の SSH クライアントを使用して、[仮想マシンに接続](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)します。
2. SSH のウィンドウで、次のコマンドを入力します。 `waagent` からの出力は、このユーティリティのバージョンによって若干異なる場合があります。
   
    `sudo waagent -deprovision+user`
   
    このコマンドは、システムをクリーンアップし、再プロビジョニングに適した状態にします。 この操作では次のタスクが実行されます。
   
   * すべての SSH ホスト キーの削除 (構成ファイルで Provisioning.RegenerateSshHostKeyPair が 'y' の場合)
   * /etc/resolv.conf 内のネームサーバー構成の消去
   * /etc/shadow の `root` ユーザーのパスワードの削除 (構成ファイルで Provisioning.DeleteRootPassword が "y" の場合)
   * キャッシュされた DHCP クライアントのリースの削除
   * ホスト名を localhost.localdomain にリセット
   * (/var/lib/waagent から取得した) 前回プロビジョニングされたユーザー アカウント **および関連データ**の削除
     
     > [!NOTE]
     > プロビジョニング解除では、イメージを "汎用化" するためにファイルとデータが削除されます。 このコマンドは、必ず新しいイメージ テンプレートとしてキャプチャする仮想マシン上で実行してください。 プロビジョニング解除により、イメージからすべての機密情報が削除されることや、イメージが第三者への再配布に適した状態になることが保証されるわけではありません。
     > 
     > 
3. 「 **y** 」と入力して続行します。 `-force` パラメーターを追加すると、この確認手順を省略できます。
4. 「 **Exit** 」と入力して、SSH クライアントを閉じます。
   
   > [!NOTE]
   > 残りの手順は、クライアント コンピューターに既に [Azure CLI がインストールされている](../xplat-cli-install.md) ことを前提としています。 次の手順はすべて、[Azure クラシック ポータル][Azure クラシック ポータル]でも実行できます。
   > 
   > 
5. クライアント コンピューターから Azure CLI を開き、Azure サブスクリプションにログインします。 詳細については、「 [Connect to an Azure subscription from the Azure CLI (Azure CLI から Azure サブスクリプションへの接続)](../xplat-cli-connect.md)」を参照してください。
6. サービス管理モードであることを確認します。
   
    `azure config mode asm`
7. 次のコマンドを実行して、上記の手順で既にプロビジョニング解除されている仮想マシンをシャットダウンします。
   
    `azure vm shutdown <your-virtual-machine-name>`
   
   > [!NOTE]
   > `azure vm list` を使用することによって、サブスクリプションで作成されたすべての仮想マシンを確認できます。
   > 
   > 
8. 仮想マシンが停止したら、次のコマンドを実行してイメージをキャプチャします。
   
    `azure vm capture -t <your-virtual-machine-name> <new-image-name>`
   
    *new-image-name* を使用するイメージ名に置き換えて入力します。 このコマンドでは、一般化された OS イメージが作成されます。 `-t` サブコマンドによって、元の仮想マシンが削除されます。
9. 新しいイメージが、新しい仮想マシンの構成に使用できるイメージの一覧に表示されるようになりました。 次のコマンドを実行すると、新しいイメージを表示できます。
   
   `azure vm image list`
   
   [Azure クラシック ポータル][Azure クラシック ポータル]の **[イメージ]** 一覧に表示されます。
   
   ![イメージのキャプチャの成功](./media/virtual-machines-linux-classic-capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>次のステップ
イメージを使用して仮想マシンを作成する準備ができました。 Azure CLI コマンド `azure vm create` を使用し、作成したイメージの名前を指定することができます。 このコマンドの詳細については、 [クラシック デプロイメント モデルでの Azure CLI の使用](../virtual-machines-command-line-tools.md) に関する記事をご覧ください。 また、[Azure クラシック ポータル][Azure クラシック ポータル]を使用して、**[ギャラリーから]** の方法を使用し、作成したイメージを選択することで、カスタム仮想マシンを作成することもできます。 詳細については、「[カスタム仮想マシンの作成方法][カスタム仮想マシンの作成方法]」をご覧ください。

**関連項目:** [Azure Linux エージェント ユーザー ガイド](virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Azure クラシック ポータル]: http://manage.windowsazure.com
[Azure の仮想マシン イメージについて]: virtual-machines-linux-classic-about-images.md
[カスタム仮想マシンの作成方法]: virtual-machines-linux-classic-create-custom.md
[データ ディスクを仮想マシンに接続する方法]: virtual-machines-windows-classic-attach-disk.md
[Linux 仮想マシンを作成する方法]: virtual-machines-linux-classic-create-custom.md



<!--HONumber=Nov16_HO3-->


