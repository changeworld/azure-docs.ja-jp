<properties urlDisplayName="Install MongoDB" pageTitle="Azure 上の Linux 仮想マシンへの MongoDB のインストール" metaKeywords="Azure vm CentOS, Azure vm Linux, Linux vm, Linux MongoDB" description="CentOS Linux を実行する Azure の仮想マシンを作成し、SSH または PuTTY を使用して MongoDB をその仮想マシンにインストールする方法について説明します。" metaCanonical="" services="virtual-machines" documentationCenter="" title="Azure 上で CentOS Linux を実行する仮想マシンへの MongoDB のインストール" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# Azure 上で CentOS Linux を実行する仮想マシンへの MongoDB のインストール

[MongoDB][MongoDB] は、高いパフォーマンスを特徴とし、広く普及しているオープン ソースの NoSQL データベースです。Azure 管理ポータルでは、CentOS Linux を実行する仮想マシンをイメージ ギャラリーから作成できます。この仮想マシンには、MongoDB データベースをインストールして構成することができます。

学習内容:

-   Windows Azure 管理ポータルを使用して、CentOS Linux を実行する Linux 仮想マシンをギャラリーから選択してインストールする方法について説明します。

-   SSH または PuTTY を使用して仮想マシンに接続する方法。
-   仮想マシンに MongoDB をインストールする方法。

## CentOS Linux を実行する仮想マシンの作成

[WACOM.INCLUDE [create-and-configure-centos-vm-in-portal](../includes/create-and-configure-centos-vm-in-portal.md)]

## データ ディスクの接続

[WACOM.INCLUDE [attach-data-disk-centos-vm-in-portal](../includes/attach-data-disk-centos-vm-in-portal.md)]

## 仮想マシンへの MongoDB のインストールと実行

[WACOM.INCLUDE [install-and-run-mongo-on-centos-vm](../includes/install-and-run-mongo-on-centos-vm.md)]

## まとめ

このチュートリアルでは、Linux 仮想マシンを作成し、SSH または PuTTY を使用してその仮想マシンにリモート接続する方法について説明しました。Linux 仮想マシンに MongoDB をインストールして構成する方法についても説明しました。MongoDB の詳細については、[MongoDB のドキュメント][MongoDB のドキュメント]を参照してください。

  [MongoDB]: http://www.mongodb.org/
  [MongoDB のドキュメント]: http://www.mongodb.org/display/DOCS/Home
