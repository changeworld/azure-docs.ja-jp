<properties
	pageTitle="クラシック Azure VM へのログオン | Microsoft Azure"
	description="Azure クラシック ポータルを使用して、クラシック デプロイ モデルを使用して作成された Windows 仮想マシンにログオンします。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2016"
	ms.author="cynthn"/>


# Azure クラシック ポータルを使用して Windows 仮想マシンにログオンする

Azure クラシック ポータルの **[接続]** ボタンを使用して、リモート デスクトップ セッションを開始し、Windows VM にログオンします。

Linux VM に接続する場合は、 「[Linux を実行する仮想マシンにログオンする方法](virtual-machines-linux-mac-create-ssh-keys.md)」を参照してください。

[これらの手順は、新しい Azure ポータルで学習することができます](virtual-machines-windows-connect-logon.md)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## ビデオ チュートリアル

以下は、このチュートリアルのステップのビデオです。Azure で Windows VM に接続するために使用する、エンドポイント、パブリック ポート、およびプライベート ポートについても説明します。

[AZURE.VIDEO logging-on-to-vm-running-windows-server-on-azure]


## 仮想マシンへの接続

1. Azure クラシック ポータルにサインインします。

2. **[Virtual Machines]** をクリックし、仮想マシンを選択します。

3. ページの下部にあるコマンド バーで、**[接続]** をクリックします。

	![仮想マシンへのログオン](./media/virtual-machines-windows-classic-connect-logon/connectwindows.png)
	
> [AZURE.TIP] **[接続]** ボタンを使用できない場合は、この記事の最後にあるトラブルシューティングのヒントを参照してください。

## 仮想マシンへのログオン

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## 次のステップ

-	**[接続]** ボタンが非アクティブである場合、またはリモート デスクトップ接続に関するその他の問題が発生している場合は、構成をリセットします。仮想マシンのダッシュボードで、**[概要]** の **[リモート構成のリセット]** をクリックします。
-	パスワードに問題がある場合は、パスワードをリセットしてください。仮想マシンのダッシュボードで、**[概要]** の **[パスワードのリセット]** をクリックします。

これらのトピックで解決できない場合、または必要な情報が掲載されていない場合は、「[Windows ベースの Azure 仮想マシンへのリモート デスクトップ接続に関するトラブルシューティング](virtual-machines-windows-troubleshoot-rdp-connection.md)」を参照してください。この記事では、一般的な問題の診断と解決の手順について説明します。

<!---HONumber=AcomDC_0824_2016-->