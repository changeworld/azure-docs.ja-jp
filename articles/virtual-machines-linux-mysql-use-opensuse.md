<properties urlDisplayName="Install MySQL" pageTitle="Azure 上で OpenSUSE Linux を実行する仮想マシンへの MySQL のインストール" metaKeywords="Azure, MySQL" description="Learn to install MySQL on a virtual machine in Azure." metaCanonical="" services="" documentationCenter="" title="Install MongoDB on a virtual machine running CentOS Linux in Azure" authors="kathydav" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="kathydav" />

# Azure 上で OpenSUSE Linux を実行する仮想マシンへの MySQL のインストール

[MySQL][MySQL] は広く普及しているオープンソースの SQL データベースです。[Azure の管理ポータル][AzurePortal]では、OpenSUSE Linux を実行する仮想マシンを作成できます。この仮想マシンには、MySQL データベースをインストールして構成することができます。

このチュートリアルは、以下について説明します。

- 管理ポータルを使用して Azure で入手できるイメージから OpenSUSE Linux 仮想マシンを作成する方法。
- SSH または PuTTY を使用して仮想マシンに接続する方法。
- 仮想マシンに MySQL をインストールする方法。

[WACOM.INCLUDE [antares-iaas-signup-iaas](../includes/antares-iaas-signup-iaas.md)]

## OpenSUSE Linux を実行する仮想マシンの作成

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal](../includes/create-and-configure-opensuse-vm-in-portal.md)]

##仮想マシンへの MySQL のインストールと実行

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm](../includes/install-and-run-mysql-on-opensuse-vm.md)]

##まとめ
このチュートリアルでは、OpenSUSE Linux を実行する仮想マシンを作成し、SSH または PuTTY を使用してその仮想マシンにリモート接続する方法について説明しました。Linux 仮想マシンに MySQL をインストールして構成する方法についても説明しました。MySQL の詳細については、[MySQL のドキュメント][MySQLDocs]を参照してください。

[MySQLDocs]: http://dev.mysql.com/doc/
[MySQL]: http://www.mysql.com
[AzurePortal]: http://manage.windowsazure.com

<!--HONumber=35_1-->
