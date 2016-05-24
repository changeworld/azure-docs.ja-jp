<properties
	pageTitle="Windows Server VM に接続する | Microsoft Azure"
	description="Azure ポータルと Resource Manager デプロイメント モデルを使用して Windows VM に接続し、ログオンする方法について説明します。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/05/2016"
	ms.author="cynthn"/>

# Windows が実行されている Azure 仮想マシンに接続してログオンする方法 


Azure ポータルの **[接続]** ボタンを使用して、リモート デスクトップ (RDP) セッションを開始します。まず、仮想マシンに接続して、ログオンします。

## 仮想マシンへの接続

1. まだサインインしていない場合は、[Azure ポータル](https://portal.azure.com/)にサインインします。

2.	ハブ メニューで **[Virtual Machines]** をクリックします。

3.	一覧から仮想マシンを選択します。

4. 仮想マシンのブレードで、**[接続]** をクリックします。

	![VM に接続する方法を示す Azure ポータルのスクリーンショット。](./media/virtual-machines-windows-connect-logon/connect.png)
	
 > [AZURE.TIP] ポータルの [接続] ボタンが灰色表示され、[Express Route](../expressroute/expressroute-introduction.md) や[サイト間 VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) 接続で Azure に接続されていない場合、VM を作成してパブリック IP アドレスを割り当ててから RDP を使用する必要があります。[Azure におけるパブリック IP アドレス](../virtual-network/virtual-network-ip-addresses-overview-arm.md)に関するページをご覧ください。

## 仮想マシンへのログオン

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]


## 次のステップ

接続時に問題が発生した場合は、「[Windows ベースの Azure 仮想マシンへのリモート デスクトップ接続に関するトラブルシューティング](virtual-machines-windows-troubleshoot-rdp-connection.md)」を参照してください。この記事では、一般的な問題の診断と解決の手順について説明します。

<!---HONumber=AcomDC_0518_2016-->