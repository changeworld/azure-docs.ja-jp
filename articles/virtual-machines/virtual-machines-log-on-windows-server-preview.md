<properties
	pageTitle="Windows Server が実行されている仮想マシンへのログオン"
	description="Azure プレビュー ポータルを使用して Windows Server が実行されている仮想マシンにログオンする方法について説明します。"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="cynthn"/>

# Windows Server が実行されている仮想マシンにログオンする方法#

Azure プレビュー ポータルの **[接続]** ボタンを使用して、リモート デスクトップ セッションを開始します。まず仮想マシンに接続してからログインします。

## 仮想マシンへの接続

1. まだサインインしていない場合は、[Azure プレビュー ポータル](https://portal.azure.com/)にサインインします

2.	ハブ メニューの **[参照]** をクリックします。

3.	検索ブレードを下にスクロールし、**[Virtual Machines]** をクリックします。

	![仮想マシンのサイズ](./media/virtual-machines-log-on-windows-server-preview/search-blade-preview-portal.png)

4.	一覧から仮想マシンを選択します。

5. 仮想マシンのブレードで、**[接続]** をクリックします。

	![仮想マシンへの接続](./media/virtual-machines-log-on-windows-server-preview/preview-portal-connect.png)

## 仮想マシンへのログオン

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## トラブルシューティング

ログオンのヒントで解決できない場合、または必要な情報が掲載されていない場合は、「[Windows ベースの Azure 仮想マシンへのリモート デスクトップ接続に関するトラブルシューティング](virtual-machines-troubleshoot-remote-desktop-connections.md)」を参照してください。この記事では、一般的な問題の診断と解決の手順について説明します。

<!---HONumber=Sept15_HO3-->