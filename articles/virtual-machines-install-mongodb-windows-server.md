<properties linkid="manage-windows-common-task-mongodb-vm" urlDisplayName="Install MongoDB" pageTitle="Install MongoDB on a Windows Server virtual machine" metaKeywords="Azure vm, Azure MongoDB, Azure remote desktop" description="Learn how to install MongoDB on an Azure VM running Windows Server." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MongoDB on a virtual machine running Windows Server in Azure" authors="kathydav" solutions="" manager="dongill" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# Windows Server を実行する仮想マシンに MongoDB をインストールする

[MongoDB][MongoDB] は、高いパフォーマンスを特徴とし、広く普及しているオープン ソースの NoSQL データベースです。[Azure 管理ポータル][Azure 管理ポータル]では、Windows Server 2008 R2 を実行する仮想マシンをイメージ ギャラリーから作成できます。この仮想マシンには、MongoDB データベースをインストールして構成することができます。

この記事では、次の方法について説明します。

-   管理ポータルを使用してギャラリーから Windows Server 仮想マシンを作成する。
-   リモート デスクトップを使用して仮想マシンに接続する。
-   仮想マシンにデータ ディスクを接続する。
-   仮想マシンに MongoDB をインストールする。

## Windows Server を実行する仮想マシンの作成

次に示すのは、一般的な方法です。この方法は、エンドポイントを作成することによって、MongoDB にリモートからアクセスできるように変更できます (エンドポイントの作成は後からでもできます。これについては、MongoDB のインストール方法の後で説明します)。ウィザードの最後のページでエンドポイントを追加し、次のように構成します。

-   仮想マシンに **Mongo** という名前を付けます。
-   プロトコルとして **TCP** を使用します。
-   パブリック ポートもプライベート ポートも **27017** に設定します。

[WACOM.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## データ ディスクの接続

仮想マシン用のストレージを用意するため、データ ディスクを接続し、Windows で使用できるように初期化します。使用するデータがある場合には既存のディスクを接続できますが、空のディスクも接続できます。

[WACOM.INCLUDE [howto-attach-disk-windows-linux](../includes/howto-attach-disk-windows-linux.md)]

ディスクを初期化する方法については、「[データ ディスクを Virtual Machine に接続する方法][データ ディスクを Virtual Machine に接続する方法]」を参照してください。

## 仮想マシンへの MongoDB のインストールと実行

[WACOM.INCLUDE [install-and-run-mongo-on-win2k8-vm](../includes/install-and-run-mongo-on-win2k8-vm.md)]

## まとめ

このチュートリアルでは、Windows Server 仮想マシンを作成する方法、リモートから仮想マシンに接続する方法、データ ディスクを接続する方法について学習しました。Windows 仮想マシンに MongoDB をインストールして構成する方法も学習しました。MongoDB の詳細については、[MongoDB のドキュメント][MongoDB のドキュメント]を参照してください。

  [MongoDB]: http://www.mongodb.org/
  [Azure 管理ポータル]: http://manage.windowsazure.com
  [データ ディスクを Virtual Machine に接続する方法]: http://azure.microsoft.com/ja-jp/documentation/articles/storage-windows-attach-disk/
  [MongoDB のドキュメント]: http://www.mongodb.org/display/DOCS/Home
