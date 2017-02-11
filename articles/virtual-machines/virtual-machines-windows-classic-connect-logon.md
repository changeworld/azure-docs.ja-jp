---
title: "クラシック Azure VM へのログオン | Microsoft Docs"
description: "Azure クラシック ポータルを使用して、クラシック デプロイ モデルを使用して作成された Windows 仮想マシンにログオンします。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 3c1239ed-07dc-48b8-8b3d-dc8c5f0ab20e
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: b2a66b134d42e0b2d965c1f3ae83f93bed9cdfba


---
# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-classic-portal"></a>Azure クラシック ポータルを使用して Windows 仮想マシンにログオンする
Azure クラシック ポータルの **[接続]** ボタンを使用して、リモート デスクトップ セッションを開始し、Windows VM にログオンします。

Linux VM に接続する場合は、 「[Linux が実行されている仮想マシンにログオンする方法](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

[これらの手順は、新しい Azure Portal で学習することができます](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Resource Manager モデルを使用した VM へのログオン方法については、[こちら](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)を参照してください。

## <a name="video-walkthrough"></a>ビデオ チュートリアル
以下は、このチュートリアルのステップのビデオです。 Azure で Windows VM に接続するために使用する、エンドポイント、パブリック ポート、およびプライベート ポートについても説明します。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Logging-On-To-VM-Running-Windows-Server-on-Azure/player]



## <a name="connect-to-the-virtual-machine"></a>仮想マシンへの接続
1. Azure クラシック ポータルにサインインします。
2. **[Virtual Machines]**をクリックし、仮想マシンを選択します。
3. ページの下部にあるコマンド バーで、 **[接続]**をクリックします。
   
    ![仮想マシンへのログオン](./media/virtual-machines-windows-classic-connect-logon/connectwindows.png)

> [!TIP]
> **[接続]** ボタンを使用できない場合は、この記事の最後にあるトラブルシューティングのヒントを参照してください。
> 
> 

## <a name="log-on-to-the-virtual-machine"></a>仮想マシンへのログオン
[!INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>次のステップ
* **[接続]** ボタンが非アクティブである場合、またはリモート デスクトップ接続に関するその他の問題が発生している場合は、構成をリセットします。 仮想マシンのダッシュボードで、**[概要]** の **[リモート構成のリセット]** をクリックします。
* パスワードに問題がある場合は、パスワードをリセットしてください。 仮想マシンのダッシュボードで、**[概要]** の **[パスワードのリセット]** をクリックします。

これらのトピックで解決できない場合、または必要な情報が掲載されていない場合は、「[Windows ベースの Azure 仮想マシンへのリモート デスクトップ接続に関するトラブルシューティング](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。 この記事では、一般的な問題の診断と解決の手順について説明します。




<!--HONumber=Nov16_HO3-->


