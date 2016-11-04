---
title: Windows VM への MongoDB のインストール | Microsoft Docs
description: Windows Server を実行するクラシック デプロイ モデルで作成された Azure VM に MongoDB をインストールする方法について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: iainfou

---
# <a name="install-mongodb-on-a-windows-vm"></a>Windows VM への MongoDB のインストール
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

リソース マネージャーのデプロイ モデルを使用して MongoDB を構成するには、「[この記事](virtual-machines-windows-classic-install-mongodb.md)」を参照してください。

[MongoDB][MongoDB] は、高いパフォーマンスを特徴とし、広く普及しているオープン ソースの NoSQL データベースです。 この記事の手順に従って、[Azure クラシック ポータル][AzurePortal] を使用して Windows Server 仮想マシン (VM) を作成します。 その後で、MongoDB をインストールして構成する前に、データ ディスクを作成して VM に接続します。 使用する VM が Azure に既に存在する場合は、 [MongoDB のインストールと構成](#install-and-run-mongodb-on-the-virtual-machine)を直接開始できます。

## <a name="create-a-virtual-machine-running-windows-server"></a>Windows Server を実行する仮想マシンの作成
仮想マシンを作成するには、以下の手順を実行します。

[!INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

> [!NOTE]
> 仮想マシンの作成中に MongoDB のエンドポイントを追加して、**Mongo** という名前を付けることができます。また、プロトコルとして **TCP** を指定し、パブリック ポートとプライベート ポートの両方を **27017** に設定します。
> 
> 

## <a name="attach-a-data-disk"></a>データ ディスクの接続
仮想マシン用のストレージを用意するため、データ ディスクを接続し、Windows で使用できるように初期化します。 データ ディスクが既にある場合は、その既存のディスクを接続するか、空のディスクを接続することができます。

[!INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

ディスクを初期化する方法については、「 [データ ディスクを Windows 仮想マシンに接続する方法](virtual-machines-windows-classic-attach-disk.md)」の「方法: Windows Server での新しいデータ ディスクの初期化」を参照してください。

## <a name="install-and-run-mongodb-on-the-virtual-machine"></a>仮想マシンへの MongoDB のインストールと実行
[!INCLUDE [install-and-run-mongo-on-win2k8-vm](../../includes/install-and-run-mongo-on-win2k8-vm.md)]

## <a name="summary"></a>概要
このチュートリアルでは、Windows Server を実行する仮想マシンを作成する方法、リモートから仮想マシンに接続する方法、データ ディスクを接続する方法について学習しました。  Windows ベースの仮想マシンに MongoDB をインストールして構成する方法も学習しました。 [MongoDB のドキュメント][MongoDocs] の高度なトピックに従って Windows ベースの仮想マシンで MongoDB に今すぐアクセスすることができます。

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzurePortal]: http://manage.windowsazure.com



<!--HONumber=Oct16_HO2-->


