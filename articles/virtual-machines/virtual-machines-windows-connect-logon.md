---
title: "Windows Server VM に接続する | Microsoft Docs"
description: "Azure ポータルと Resource Manager デプロイメント モデルを使用して Windows VM に接続し、ログオンする方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/28/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: db417fb72442ea8a5cd4ef882eb657b08bebaa0a


---
# <a name="how-to-connect-and-log-on-to-an-azure-virtual-machine-running-windows"></a>Windows が実行されている Azure 仮想マシンに接続してログオンする方法
Azure ポータルの **[接続]** ボタンを使用して、リモート デスクトップ (RDP) セッションを開始します。 まず、仮想マシンに接続して、ログオンします。

## <a name="connect-to-the-virtual-machine"></a>仮想マシンへの接続
1. まだサインインしていない場合は、 [Azure ポータル](https://portal.azure.com/)にサインインします。
2. ハブ メニューで **[Virtual Machines]**をクリックします。
3. 一覧から仮想マシンを選択します。
4. 仮想マシンのブレードで、 **[接続]**をクリックします。
   
    ![VM に接続する方法を示す Azure ポータルのスクリーンショット。](./media/virtual-machines-windows-connect-logon/connect.png)
   
   > [!TIP]
   > ポータルの **[接続]** ボタンが淡色表示され、[Express Route](../expressroute/expressroute-introduction.md) や[サイト間 VPN 接続](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)で Azure に接続されていない場合は、VM を作成してパブリック IP アドレスを割り当ててから RDP を使用する必要があります。 [Azure におけるパブリック IP アドレス](../virtual-network/virtual-network-ip-addresses-overview-arm.md)に関するページをご覧ください。
   > 
   > 

## <a name="log-on-to-the-virtual-machine"></a>仮想マシンへのログオン
[!INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>次のステップ
接続時に問題が発生した場合は、 [リモート デスクトップ接続のトラブルシューティング](virtual-machines-windows-troubleshoot-rdp-connection.md)に関するページを参照してください。 この記事では、一般的な問題の診断と解決の手順について説明します。




<!--HONumber=Nov16_HO2-->


