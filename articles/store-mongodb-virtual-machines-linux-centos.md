<properties linkid="store-mongodb-virtual-machines-linux-centos" urlDisplayName="Install MongoDB" pageTitle="nstall MongoDB on a virtual machine running CentOS Linux in Azure" metaKeywords="Azure, MongoDB" description="Learn how to install Mongo DB on a virtual machine in Azure." metaCanonical="" services="" documentationCenter="" title="Install MongoDB on a virtual machine running CentOS Linux in Azure" authors="bbenz, MSOpenTech" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang ms.topic="article" ms.date="01/01/1900" ms.author="bbenz, MSOpenTech"></tags>

# Azure 上で CentOS Linux を実行する仮想マシンへの MongoDB のインストール

[MongoDB][MongoDB] は、高いパフォーマンスを特徴とし、広く普及しているオープン ソースの NoSQL データベースです。[Azure の管理ポータル][Azure の管理ポータル]では、CentOS Linux を実行する仮想マシンをイメージ ギャラリーから作成できます。この仮想マシンには、MongoDB データベースをインストールして構成することができます。

学習内容:

-   管理ポータルを使用して CentOS Linux を実行する仮想マシンをギャラリーから作成する方法。
-   SSH または PuTTY を使用して仮想マシンに接続する方法。
-   仮想マシンに MongoDB をインストールする方法。

## CentOS Linux を実行する仮想マシンの作成

[WACOM.INCLUDE [create-and-configure-centos-vm-in-portal][create-and-configure-centos-vm-in-portal]]

## 仮想マシンへの MongoDB のインストールと実行

[WACOM.INCLUDE [install-and-run-mongo-on-centos-vm][install-and-run-mongo-on-centos-vm]]

## まとめ

このチュートリアルでは、Linux 仮想マシンを作成し、SSH または PuTTY を使用してその仮想マシンにリモート接続する方法について説明しました。Linux 仮想マシンに MongoDB をインストールして構成する方法についても説明しました。MongoDB の詳細については、[MongoDB のドキュメント][MongoDB のドキュメント]を参照してください。

  [MongoDB]: http://www.mongodb.org/
  [Azure の管理ポータル]: http://manage.windowsazure.com
  [create-and-configure-centos-vm-in-portal]: ../includes/create-and-configure-centos-vm-in-portal.md
  [install-and-run-mongo-on-centos-vm]: ../includes/install-and-run-mongo-on-centos-vm.md
  [MongoDB のドキュメント]: http://www.mongodb.org/display/DOCS/Home
