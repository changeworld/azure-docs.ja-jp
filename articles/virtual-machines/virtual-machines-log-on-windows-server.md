<properties 
	pageTitle="Windows Server が実行されている仮想マシンへのログオン" 
	description="Azure 管理ポータルを使用して Windows Server が実行されている仮想マシンにログオンする方法について説明します。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/12/2015" 
	ms.author="kathydav"/>


# Windows Server が実行されている仮想マシンにログオンする方法#

Azure ポータルの **[接続]** ボタンを使用して、リモート デスクトップ セッションを開始します (Linux VM の場合、「[Linux を実行する仮想マシンにログオンする方法](virtual-machines-linux-how-to-log-on.md)」を参照してください)。

## ログオンする方法

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## トラブルシューティングのヒント

すぐに試すことができるいくつかの操作を次に示します。

リモート デスクトップ接続に問題がある場合は、ポータルから構成をリセットしてください。仮想マシンのダッシュボードで、**[概要]** の **[リモート構成のリセット]** をクリックします。

パスワードに問題がある場合は、ポータルからパスワードをリセットしてください。仮想マシンのダッシュボードで、**[概要]** の **[パスワードのリセット]** をクリックします。

この操作を行っても問題が解決しない場合は、より広範なトラブルシューティングを行う必要があります。手順については、[Windows ベースの Azure 仮想マシンへのリモート デスクトップ接続に関するトラブルシューティング](virtual-machines-troubleshoot-remote-desktop-connections.md)に関するページを参照してください。
 
 

<!---HONumber=July15_HO4-->