<properties
	pageTitle="Windows VM への MongoDB のインストール | Microsoft Azure"
	description="Windows Server を実行するクラシック デプロイ モデルで作成された Azure VM に MongoDB をインストールする方法について説明します。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/07/2016"
	ms.author="iainfou"/>

#Windows VM への MongoDB のインストール

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]リソース マネージャー モデル。

[MongoDB][MongoDB] は、高いパフォーマンスを特徴とし、広く普及しているオープン ソースの NoSQL データベースです。この記事では、[Azure クラシック ポータル][AzurePortal]を使用して新しい Windows Server 仮想マシン (VM) を作成し、データ ディスクを作成して VM にアタッチして、MongoDB をインストールおよび構成する手順を説明します。使用する VM が Azure に既に存在する場合は、[MongoDB のインストールと構成](#install-and-run-mongo-on-win2k8-vm)を直接開始できます。


## Windows Server を実行する仮想マシンの作成

仮想マシンを作成するには、以下の手順を実行します。

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

> [AZURE.NOTE] 仮想マシンの作成中に MongoDB のエンドポイントを追加して、**Mongo** という名前を付けることができます。また、プロトコルとして **TCP** を指定し、パブリック ポートとプライベート ポートの両方を **27017** に設定します。

## データ ディスクの接続
仮想マシン用のストレージを用意するため、データ ディスクを接続し、Windows で使用できるように初期化します。使用するデータがある場合は既存のディスクを接続するか、空のディスクを接続できます。

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

ディスクを初期化する方法については、「[データ ディスクを Windows 仮想マシンに接続する方法](virtual-machines-windows-classic-attach-disk.md)」の「方法: Windows Server での新しいデータ ディスクの初期化」を参照してください。

## 仮想マシンへの MongoDB のインストールと実行

[AZURE.INCLUDE [install-and-run-mongo-on-win2k8-vm](../../includes/install-and-run-mongo-on-win2k8-vm.md)]

## 概要
このチュートリアルでは、Windows Server を実行する仮想マシンを作成する方法、リモートから仮想マシンに接続する方法、データ ディスクを接続する方法について学習しました。Windows ベースの仮想マシンに MongoDB をインストールして構成する方法も学習しました。これで、[MongoDB のドキュメント][MongoDocs]の詳細なトピックに従って、Windows ベースの仮想マシンの MongoDB にアクセスできます。

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzurePortal]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0608_2016-->