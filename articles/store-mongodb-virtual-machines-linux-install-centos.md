<properties linkid="manage-linux-common-task-mongodb-virtual-machine" urlDisplayName="Install MongoDB" pageTitle="Install MongoDB on a Linux virtual machine in Azure" metaKeywords="Azure vm CentOS, Azure vm Linux, Linux vm, Linux MongoDB" description="Learn how to create an Azure virtual machine with CentOS Linux, and then use SSH or PuTTY to install MongoDB." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MongoDB on a virtual machine running CentOS Linux in Azure" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Azure 上で CentOS Linux を実行する仮想マシンへの MongoDB のインストール

[MongoDB][MongoDB] は、高いパフォーマンスを特徴とし、広く普及しているオープン ソースの NoSQL データベースです。Azure 管理ポータルでは、CentOS Linux を実行する仮想マシンをイメージ ギャラリーから作成できます。この仮想マシンには、MongoDB データベースをインストールして構成することができます。

学習内容:

-   Windows Azure 管理ポータルを使用して、CentOS Linux を実行する Linux 仮想マシンをギャラリーから選択してインストールする方法について説明します。

-   SSH または PuTTY を使用して仮想マシンに接続する方法。
-   仮想マシンに MongoDB をインストールする方法。

## CentOS Linux を実行する仮想マシンの作成

[WACOM.INCLUDE [create-and-configure-centos-vm-in-portal][create-and-configure-centos-vm-in-portal]]

## データ ディスクの接続

[WACOM.INCLUDE [attach-data-disk-centos-vm-in-portal][attach-data-disk-centos-vm-in-portal]]

## 仮想マシンへの MongoDB のインストールと実行

[WACOM.INCLUDE [install-and-run-mongo-on-centos-vm][install-and-run-mongo-on-centos-vm]]

## まとめ

このチュートリアルでは、Linux 仮想マシンを作成し、SSH または PuTTY を使用してその仮想マシンにリモート接続する方法について説明しました。Linux 仮想マシンに MongoDB をインストールして構成する方法についても説明しました。MongoDB の詳細については、[MongoDB のドキュメント][MongoDB のドキュメント]を参照してください。

  [MongoDB]: http://www.mongodb.org/
  [create-and-configure-centos-vm-in-portal]: ../includes/create-and-configure-centos-vm-in-portal.md
  [attach-data-disk-centos-vm-in-portal]: ../includes/attach-data-disk-centos-vm-in-portal.md
  [install-and-run-mongo-on-centos-vm]: ../includes/install-and-run-mongo-on-centos-vm.md
  [MongoDB のドキュメント]: http://www.mongodb.org/display/DOCS/Home
