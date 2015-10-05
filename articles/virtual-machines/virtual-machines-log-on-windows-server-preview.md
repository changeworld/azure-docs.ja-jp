<properties
	pageTitle="Windows Server VM へのログオン | Microsoft Azure"
	description="Azure プレビュー ポータルと、リソース マネージャー デプロイ モデルを使用して、Windows Server VM にログオンする方法について説明します。"
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

# Windows Server が実行されている仮想マシンにログオンする方法 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]この記事では、リソース マネージャー デプロイ モデルを使用した Windows VM へのログオンについて説明します。また、[クラシック デプロイ モデル](virtual-machines-log-on-windows-server.md)を使用してリソースを作成することもできます。

Azure プレビュー ポータルの **[接続]** ボタンを使用して、リモート デスクトップ セッションを開始します。まず仮想マシンに接続してからログインします。

## 仮想マシンへの接続

1. まだサインインしていない場合は、[Azure プレビュー ポータル](https://portal.azure.com/)にサインインします。

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

<!---HONumber=Sept15_HO4-->