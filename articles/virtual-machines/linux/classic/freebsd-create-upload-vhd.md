---
title: "FreeBSD VM イメージの作成とアップロード | Microsoft Docs"
description: "FreeBSD オペレーティング システムを格納した仮想ハード ディスク (VHD) を作成およびアップロードして、Azure 仮想マシンを作成する方法について説明します"
services: virtual-machines-linux
documentationcenter: 
author: thomas1206
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: huishao
ms.openlocfilehash: 7b41826f071174df8f00af56a228e0f31c3cfe2f
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2017
---
# <a name="create-and-upload-a-freebsd-vhd-to-azure"></a>FreeBSD VHD の作成と Azure へのアップロード
この記事では、FreeBSD オペレーティング システムを格納した仮想ハード ディスク (VHD) を作成してアップロードする方法について説明します。 アップロードした VHD を独自のイメージとして使用し、Azure の仮想マシン (VM) を作成することができます。

> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。 Resource Manager モデルを使用した VHD のアップロード方法については、[こちら](../upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)をご覧ください。

## <a name="prerequisites"></a>前提条件
この記事では、次の項目があることを前提としています。

* **Azure サブスクリプション**- アカウントをお持ちでない場合でも、数分でアカウントを作成できます。 MSDN サブスクリプションをお持ちの場合は、「[Visual Studio サブスクライバー向けの月単位の Azure クレジット](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)」をご覧ください。 それ以外の場合は、 [無料試用版のアカウントの作成](https://azure.microsoft.com/pricing/free-trial/)方法に関するページをご覧ください。  
* **Azure PowerShell ツール**- Azure PowerShell モジュールをインストールし、Azure サブスクリプションを使用するように構成しておきます。 このモジュールをダウンロードするには、 [Azure のダウンロード ページ](https://azure.microsoft.com/downloads/)にアクセスしてください。 モジュールをインストールして構成する方法についてのチュートリアルはこちらで入手できます。 [Azure のダウンロード](https://azure.microsoft.com/downloads/) のコマンドレットを使用して VHD をアップロードします。
* **.vhd ファイルにインストールされている FreeBSD オペレーティング システム**- サポートされている FreeBSD オペレーティング システムを仮想ハード ディスクにインストールしておきます。 .vhd ファイルを作成するツールはいくつかあります。 たとえば Hyper-V などの仮想化ソリューションを使用して .vhd ファイルを作成し、オペレーティング システムをインストールすることができます。 Hyper-V をインストールして使用する手順については、「 [Hyper-V をインストールして仮想マシンを作成する](http://technet.microsoft.com/library/hh846766.aspx)」を参照してください。

> [!NOTE]
> 新しい VHDX 形式は、Azure ではサポートされていません。 Hyper-V マネージャーまたは [convert-vhd](https://technet.microsoft.com/library/hh848454.aspx)コマンドレットを使用して、ディスクを VHD 形式に変換できます。 また、 [Hyper-V で FreeBSD を使用する方法についてのチュートリアルが MSDN](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx)に存在します。
>
>

このタスクの手順は次のとおり 4 つあります。

## <a name="step-1-prepare-the-image-for-upload"></a>手順 1. アップロードするイメージを準備する
FreeBSD オペレーティング システムがインストールされた仮想マシンで、次の手順を実行します。

1. DHCP を有効にします。

        # echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
        # service netif restart
2. SSH を有効にします。

    SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。 既定で、FreeBSD ディスクからのインストール後、SSH は有効になります。 
3. シリアル コンソールをセットアップします。

        # echo 'console="comconsole vidconsole"' >> /boot/loader.conf
        # echo 'comconsole_speed="115200"' >> /boot/loader.conf
4. sudo をインストールします。

    Azure では "root" アカウントは無効化されます。 つまり、特権のないユーザーから sudo を利用し、昇格した特権でコマンドを実行する必要があります。

        # pkg install sudo
   
5. Azure エージェントの前提条件を満たします。

        # pkg install python27  
        # pkg install Py27-setuptools  
        # ln -s /usr/local/bin/python2.7 /usr/bin/python   
        # pkg install git
6. Azure エージェントをインストールします。

    Azure エージェントの最新版は常に [GitHub](https://github.com/Azure/WALinuxAgent/releases)にあります。 バージョン 2.0.10 以降は FreeBSD 10 および 10.1 を正式にサポートし、バージョン 2.1.4 以上 (2.2.x を含む) は FreeBSD 10.2 以降のリリースを正式にサポートします。

        # git clone https://github.com/Azure/WALinuxAgent.git  
        # cd WALinuxAgent  
        # git tag  
        …
        WALinuxAgent-2.0.16
        …
        v2.1.4
        v2.1.4.rc0
        v2.1.4.rc1

    2.0 については、例として 2.0.16 を使用します。

        # git checkout WALinuxAgent-2.0.16
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  

    2.1 については、例として 2.1.4 を使用します。

        # git checkout v2.1.4
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  
        # ln -sf /usr/local/sbin/waagent2.0 /usr/sbin/waagent2.0

   > [!IMPORTANT]
   > Azure エージェントがインストール後、実行されていることを確認するようお勧めします。
   >
   >

        # waagent -version
        WALinuxAgent-2.1.4 running on freebsd 10.3
        Python: 2.7.11
        # ps auxw | grep waagent
        root   639   0.0  0.5 104620 17520 u0- I    05:17    0:00.20 python /usr/local/sbin/waagent -daemon (python2.7)
        # cat /var/log/waagent.log
7. システムのプロビジョニングを解除します。

    システムのプロビジョニングを解除してクリーンアップし、再プロビジョニングに適した状態にします。 以下のコマンドは前回プロビジョニングされたユーザー アカウントおよび関連付けられたデータも削除します。

        # echo "y" |  /usr/local/sbin/waagent -deprovision+user  
        # echo  'waagent_enable="YES"' >> /etc/rc.conf

    これで VM をシャットダウンできます。

## <a name="step-2-prepare-the-connection-to-azure"></a>手順 2. Azure への接続を準備する
クラシック デプロイメント モデル (`azure config mode asm`) で Azure CLI を使用していることを確認し、次のように、自分のアカウントにログインします。

```azurecli
azure login
```


<a id="upload"> </a>


## <a name="step-3-upload-the-vhd-file"></a>手順 3: .vhd ファイルをアップロードする

VHD ファイルをアップロードするストレージ アカウントが必要です。 既存のストレージ アカウントを選択することも、 [新しいストレージ アカウントを作成する](../../../storage/common/storage-create-storage-account.md)こともできます。

.vhd ファイルをアップロードするときは、Blob Storage 内であればどこにでも .vhd ファイルを置くことができます。 以下、ファイルをアップロードするときに使用するいくつかの用語について補足します。

* **BlobStorageURL** は、ステップ 2. で作成したストレージ アカウントの URL です。
* **YourImagesFolder** は、イメージを格納する Blob Storage 内のコンテナーです。
* **VHDName** は、仮想ハード ディスクを識別するために Azure クラシック ポータルに表示されるラベルです。
* **PathToVHDFile** は、.vhd ファイルの完全なパスとファイル名です。

前の手順で使用した Azure PowerShell ウィンドウで、次のように入力します。

        Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## <a name="step-4-create-a-vm-with-the-uploaded-vhd-file"></a>ステップ 4: アップロードした .vhd ファイルで VM を作成する
.vhd ファイルをアップロードしたら、サブスクリプションに関連付けられたカスタム イメージの一覧にイメージとして追加し、そのカスタム イメージから仮想マシンを作成できます。

1. 前の手順で使用した Azure PowerShell ウィンドウで、次のように入力します。

        Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

   > [!NOTE]
   > OS の種類としては Linux を使用します。 現在のバージョンの Azure PowerShell でパラメーターとして指定できるのは "Linux" と "Windows" のみです。
   >
   >
2. ここまでの手順を完了すると、Azure クラシック ポータルで **[イメージ]** タブをクリックしたときに、新しいイメージが一覧に表示されます。  

    ![Choose an image](./media/freebsd-create-upload-vhd/addfreebsdimage.png)
3. ギャラリーから仮想マシンを作成します。 これで **[マイ イメージ]**で新しいイメージが使用可能になりました。
4. 新しいイメージを選択し、 表示される画面でホスト名、パスワード、SSH キーなどを設定します。

    ![Custom image](./media/freebsd-create-upload-vhd/createfreebsdimageinazure.png)
5. プロビジョニングが完了すると、Azure に FreeBSD VM が実行状態として表示されます。

    ![FreeBSD image in azure](./media/freebsd-create-upload-vhd/freebsdimageinazure.png)
