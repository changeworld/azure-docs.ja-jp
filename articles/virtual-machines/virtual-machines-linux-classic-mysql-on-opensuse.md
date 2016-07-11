<properties
	pageTitle="Microsoft Azure の OpenSUSE Linux VM に MySQL をインストールする"
	description="Azure の仮想マシンに MySQL をインストールする方法について説明します。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2016"
	ms.author="cynthn"/>

# Azure 上で OpenSUSE Linux を実行する仮想マシンへの MySQL のインストール

[MySQL][MySQL] は広く普及しているオープン ソースの SQL Database です。このチュートリアルでは、OpenSUSE Linux を実行する仮想マシンを作成してから、MySQL をインストールする方法を説明します。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


<br>


## OpenSUSE Linux を実行する仮想マシンの作成

[AZURE.INCLUDE [create-and-configure-opensuse-vm-in-portal](../../includes/create-and-configure-opensuse-vm-in-portal.md)]

## 仮想マシンへの MySQL のインストールと実行

[AZURE.INCLUDE [install-and-run-mysql-on-opensuse-vm](../../includes/install-and-run-mysql-on-opensuse-vm.md)]

## 次のステップ
MySQL の詳細については、[MySQL のドキュメント][MySQLDocs]を参照してください。

[MySQLDocs]: http://dev.mysql.com/doc/index-topic.html
[MySQL]: http://www.mysql.com

<!---HONumber=AcomDC_0629_2016-->