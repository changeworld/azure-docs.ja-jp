<properties linkid="virtual-machines-linux-mysql-use-opensuse" urlDisplayName="Install MongoDB" pageTitle="nstall MongoDB on a virtual machine running CentOS Linux in Azure" metaKeywords="Azure, MongoDB" description="Learn how to install Mongo DB on a virtual machine in Azure." metaCanonical="" services="" documentationCenter="" title="Install MongoDB on a virtual machine running CentOS Linux in Azure" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt"></tags>

# Azure 上で OpenSUSE Linux を実行する仮想マシンへの MySQL のインストール

[MySQL][MySQL] は広く普及しているオープン ソースの SQL データベースです。[Azure の管理ポータル][Azure の管理ポータル]では、OpenSUSE Linux を実行する仮想マシンをイメージ ギャラリーから作成できます。この仮想マシンには、MySQL データベースをインストールして構成することができます。

このチュートリアルでは、次のことを説明します。

-   管理ポータルを使用してギャラリーから OpenSUSE Linux 仮想マシンを作成する方法。
-   SSH または PuTTY を使用して仮想マシンに接続する方法。
-   仮想マシンに MySQL をインストールする方法。

## Azure サブスクリプションの作成

Azure サブスクリプションをまだ取得していない場合でも、[無料][無料]でサインアップできます。サインアップした後で、次の手順に従ってこのチュートリアルを続行します。

[WACOM.INCLUDE [antares-iaas-signup-iaas][antares-iaas-signup-iaas]]

## OpenSUSE Linux を実行する仮想マシンの作成

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal][create-and-configure-opensuse-vm-in-portal]]

## 仮想マシンへの MySQL のインストールと実行

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm][install-and-run-mysql-on-opensuse-vm]]

## まとめ

このチュートリアルでは、OpenSUSE Linux を実行する仮想マシンを作成し、SSH または PuTTY を使用してその仮想マシンにリモート接続する方法について説明しました。Linux 仮想マシンに MySQL をインストールして構成する方法についても説明しました。MySQL の詳細については、[MySQL のドキュメント][MySQL のドキュメント]を参照してください。

  [MySQL]: http://www.mysql.com
  [Azure の管理ポータル]: http://manage.windowsazure.com
  [無料]: http://azure.microsoft.com
  [antares-iaas-signup-iaas]: ../includes/antares-iaas-signup-iaas.md
  [create-and-configure-opensuse-vm-in-portal]: ../includes/create-and-configure-opensuse-vm-in-portal.md
  [install-and-run-mysql-on-opensuse-vm]: ../includes/install-and-run-mysql-on-opensuse-vm.md
  [MySQL のドキュメント]: http://dev.mysql.com/doc/
