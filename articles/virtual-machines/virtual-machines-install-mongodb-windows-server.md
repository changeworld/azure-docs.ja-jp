<properties 
	pageTitle="Windows Server 仮想マシンに MongoDB をインストールする" 
	description="Windows Server を実行する Azure VM に MongoDB をインストールする方法について説明します。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/06/2015" 
	ms.author="kathydav"/>

#Windows Server を実行する仮想マシンに MongoDB をインストールする

[MongoDB][MongoDB] は、高いパフォーマンスを特徴とし、広く普及しているオープン ソースの NoSQL データベースです。[Azure 管理ポータル][AzureManagementPortal]では、Windows Server 2008 R2 を実行する仮想マシンをイメージ ギャラリーから作成できます。この仮想マシンには、MongoDB データベースをインストールして構成することができます。

この記事では、次の方法について説明します。

- 管理ポータルを使用してギャラリーから Windows Server 仮想マシンを作成する。
- リモート デスクトップを使用して仮想マシンに接続する。
- 仮想マシンにデータ ディスクを接続する。
- 仮想マシンに MongoDB をインストールする。

## Windows Server を実行する仮想マシンの作成

次に示すのは、一般的な方法です。この方法は、エンドポイントを作成することによって、MongoDB にリモートからアクセスできるように変更できます(エンドポイントの作成は後からでもできます。これについては、MongoDB のインストール方法の後で説明します)。 ウィザードの最後のページでエンドポイントを追加し、次のように構成します。

- 仮想マシンに **Mongo** という名前を付けます。
- プロトコルとして **TCP** を使用します。
- パブリック ポートもプライベート ポートも **27017** に設定します。
 
[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-WindowsVM.md)]

## データ ディスクの接続
仮想マシン用のストレージを用意するため、データ ディスクを接続し、Windows で使用できるように初期化します。使用するデータがある場合には既存のディスクを接続できますが、空のディスクも接続できます。

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

ディスクを初期化する方法については、「[データ ディスクを Windows 仮想マシンに追加する方法](storage-windows-attach-disk.md)」を参照してください。

## 仮想マシンへの MongoDB のインストールと実行 

[AZURE.INCLUDE [install-and-run-mongo-on-win2k8-vm](../../includes/install-and-run-mongo-on-win2k8-vm.md)]

##概要
このチュートリアルでは、Windows Server 仮想マシンを作成する方法、リモートから仮想マシンに接続する方法、データ ディスクを接続する方法について学習しました。Windows 仮想マシンに MongoDB をインストールして構成する方法も学習しました。MongoDB の詳細については、[MongoDB のドキュメント][MongoDocs]を参照してください。

[MongoDocs]: http://www.mongodb.org/display/DOCS/Home
[MongoDB]: http://www.mongodb.org/
[AzureManagementPortal]: http://manage.windowsazure.com
 

<!---HONumber=July15_HO1-->