<properties linkid="manage-windows-common-task-mongodb-vm" urlDisplayName="MongoDB のインストール" pageTitle="Windows Server 仮想マシンへの MongoDB のインストール" metaKeywords="Azure vm, Azure MongoDB, Azure リモート デスクトップ" description="Windows Server 2008 R2 を実行する Azure の仮想マシンを作成し、リモート デスクトップを使用して MongoDB をインストールする方法について説明します。" metaCanonical="" services="virtual-machines" documentationCenter="" title="Azure で Windows Server を実行する仮想マシンへの MongoDB のインストール" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />




#Azure で Windows Server を実行する仮想マシンへの MongoDB のインストール

[MongoDB][MongoDB] は、高いパフォーマンスを特徴とし、広く普及しているオープン ソースの NoSQL データベースです。[Azure 管理ポータル][AzurePreviewPortal]では、Windows Server 2008 R2 を実行する仮想マシンをイメージ ギャラリーから作成できます。この仮想マシンには、MongoDB データベースをインストールして構成することができます。

このチュートリアルでは、次のことを説明します。

- 管理ポータルを使用してギャラリーから Windows Server 仮想マシンを作成する方法。
- リモート デスクトップを使用して仮想マシンに接続する方法。
- 仮想マシンに MongoDB をインストールする方法。

## Windows Server 2008 R2 を実行する仮想マシンの作成

[WACOM.INCLUDE [create-and-configure-windows-server-2008-vm-in-portal](../includes/create-and-configure-windows-server-2008-vm-in-portal.md)]

## データ ディスクの接続

[WACOM.INCLUDE [attach-data-disk-windows-server-2008-vm-in-portal](../includes/attach-data-disk-windows-server-2008-vm-in-portal.md)]

## 仮想マシンへの MongoDB のインストールと実行

[WACOM.INCLUDE [install-and-run-mongo-on-win2k8-vm](../includes/install-and-run-mongo-on-win2k8-vm.md)]

##まとめ
このチュートリアルでは、Windows Server 仮想マシンを作成し、リモート接続する方法について説明しました。Windows 仮想マシンに MongoDB をインストールして構成する方法も学習しました。MongoDB の詳細については、[MongoDB のドキュメント][MongoDocs]を参照してください。

[MongoDocs]: http://www.mongodb.org/display/DOCS/Home
[MongoDB]: http://www.mongodb.org/
[AzurePreviewPortal]: http://manage.windowsazure.com

