<properties 
	pageTitle="Azure 上で OpenSUSE Linux を実行する仮想マシンへの MySQL のインストール" 
	description="Azure の仮想マシンに MySQL をインストールする方法について説明します。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/12/2014" 
	ms.author="kathydav"/>

# Azure 上で OpenSUSE Linux を実行する仮想マシンへの MySQL のインストール

[MySQL][MySQL] は広く普及しているオープンソースの SQL データベースです。このチュートリアルは、以下について説明します。

- [Azure 管理ポータル][AzurePortal]を使用して Azure で入手できるイメージから OpenSUSE Linux 仮想マシンを作成する方法。
- SSH または PuTTY を使用して仮想マシンに接続する方法。
- 仮想マシンに MySQL をインストールする方法。

[AZURE.INCLUDE [antares-iaas-signup-iaas](../includes/antares-iaas-signup-iaas.md)]

## OpenSUSE Linux を実行する仮想マシンの作成

[AZURE.INCLUDE [create-and-configure-opensuse-vm-in-portal](../includes/create-and-configure-opensuse-vm-in-portal.md)]

##仮想マシンへの MySQL のインストールと実行

[AZURE.INCLUDE [install-and-run-mysql-on-opensuse-vm](../includes/install-and-run-mysql-on-opensuse-vm.md)]

##まとめ
このチュートリアルでは、OpenSUSE Linux を実行する仮想マシンを作成し、SSH または PuTTY を使用してその仮想マシンにリモート接続する方法について説明しました。Linux 仮想マシンに MySQL をインストールして構成する方法についても説明しました。MySQL の詳細については、[MySQL のドキュメント][MySQLDocs]を参照してください。

[MySQLDocs]: http://dev.mysql.com/doc/
[MySQL]: http://www.mysql.com
[AzurePortal]: http://manage.windowsazure.com


<!--HONumber=42-->
