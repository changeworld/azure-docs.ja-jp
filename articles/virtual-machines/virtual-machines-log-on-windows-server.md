<properties
	pageTitle="VM へのログオン | Microsoft Azure"
	description="ポータルを使用して、クラシック デプロイ モデルを使用して作成された Windows 仮想マシンにログオンします。"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="cynthn"/>


# クラシック デプロイ モデルを使用して作成された Windows 仮想マシンにログオンする

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]この記事では、クラシック デプロイ モデルでのリソースの管理について説明します。

Azure プレビュー ポータルの **[接続]** ボタンを使用して、リモート デスクトップ セッションを開始します。まず仮想マシンに接続してからログインします。

Linux VM に接続する場合は、 「[Linux を実行する仮想マシンにログオンする方法](virtual-machines-linux-how-to-log-on.md)」を参照してください。

## 仮想マシンへの接続

以下には、このチュートリアルのステップが記載されています。

[AZURE.VIDEO logging-on-to-vm-running-windows-server-on-azure]

1. まだサインインしていない場合は、[Azure ポータル](http://manage.windowsazure.com)にサインインします。

2. **[仮想マシン]** をクリックし、適切な仮想マシンを選択します。

3. コマンド バーで、**[接続]** をクリックします。

	![仮想マシンへのログオン](./media/virtual-machines-log-on-windows-server/connectwindows.png)

## 仮想マシンへのログオン

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## トラブルシューティングのヒント

ポータルですぐに試すことができるいくつかの操作を次に示します。

-	リモート デスクトップ接続に問題がある場合は、構成をリセットしてください。仮想マシンのダッシュボードで、**[概要]** の **[リモート構成のリセット]** をクリックします。
-	パスワードに問題がある場合は、パスワードをリセットしてください。仮想マシンのダッシュボードで、**[概要]** の **[パスワードのリセット]** をクリックします。

これらのトピックで解決できない場合、または必要な情報が掲載されていない場合は、「[Windows ベースの Azure 仮想マシンへのリモート デスクトップ接続に関するトラブルシューティング](virtual-machines-troubleshoot-remote-desktop-connections.md)」を参照してください。この記事では、一般的な問題の診断と解決の手順について説明します。

<!---HONumber=Sept15_HO4-->