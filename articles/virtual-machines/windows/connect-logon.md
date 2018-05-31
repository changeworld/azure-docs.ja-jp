---
title: Windows Server VM に接続する | Microsoft Docs
description: Azure ポータルと Resource Manager デプロイ モデルを使用して Windows VM に接続し、ログオンする方法について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: cynthn
ms.openlocfilehash: 7c495a74ccbcad3ee18147726742ee59b65f1c0e
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012643"
---
# <a name="how-to-connect-and-log-on-to-an-azure-virtual-machine-running-windows"></a>Windows が実行されている Azure 仮想マシンに接続してログオンする方法
Azure Portal の **[接続]** ボタンを使用して、Windows デスクトップからリモート デスクトップ (RDP) セッションを開始します。 まず、仮想マシンに接続して、ログオンします。

Mac から Windows 仮想マシンに接続する場合は、[Microsoft リモート デスクトップ](https://itunes.apple.com/app/microsoft-remote-desktop/id715768417)など、Mac 用の RDP クライアントをインストールする必要があります。

## <a name="connect-to-the-virtual-machine"></a>仮想マシンへの接続
1. まだサインインしていない場合は、 [Azure ポータル](https://portal.azure.com/)にサインインします。
2. 左側のメニューで **[仮想マシン]** をクリックします。
3. 一覧から仮想マシンを選択します。
4. 仮想マシンのページの上部にある接続ボタンの ![イメージをクリックします。](./media/connect-logon/connect.png) ボタンを選択します。
2. **[仮想マシンへの接続]** ページで、適切なオプションを選択して、**[RDP ファイルのダウンロード]** をクリックします。
2. ダウンロードした RDP ファイルを開き、プロンプトが表示されたら **[接続]** をクリックします。 
2. `.rdp` ファイルの発行元が不明であることを示す警告が表示されます。 問題はありません。 リモート デスクトップ ウィンドウで、 **[接続]** をクリックして続行します。
   
    ![Screenshot of a warning about an unknown publisher.](./media/connect-logon/rdp-warn.png)
3. **[Windows セキュリティ]** ウィンドウで、**[その他]**、**[別のアカウントを使用する]** の順に選択します。 仮想マシンのアカウントの資格情報を入力し、**[OK]** をクリックします。
   
     **ローカル アカウント** - 通常は、仮想マシンの作成時に指定したローカル アカウントのユーザー名とパスワードです。 この場合、ドメインは仮想マシンの名前です。これを *vmname*&#92;*username* の形式で入力します。  
   
    **ドメインに参加している VM** - VM がドメインに属している場合は、*Domain*&#92;*Username* の形式でユーザー名を入力します。 また、アカウントは管理者グループに属しているか、VM へのリモート アクセス特権が付与されている必要があります。
   
    **ドメイン コントローラー** - VM がドメイン コントローラーである場合は、そのドメインのドメイン管理者アカウントのユーザー名とパスワードを入力します。
4. **[はい]** をクリックして、目的の仮想マシンであることを確認し、ログオンを完了します。
   
   ![VM の ID の検証に関するメッセージが表示されているスクリーンショット。](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > ポータルの **[接続]** ボタンが淡色表示され、[Express Route](../../expressroute/expressroute-introduction.md) や[サイト間 VPN 接続](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)で Azure に接続されていない場合は、VM を作成してパブリック IP アドレスを割り当ててから RDP を使用する必要があります。 [Azure におけるパブリック IP アドレス](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)に関するページをご覧ください。
   > 
   > 


## <a name="next-steps"></a>次の手順
接続時に問題が発生した場合は、 [リモート デスクトップ接続のトラブルシューティング](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページを参照してください。 この記事では、一般的な問題の診断と解決の手順について説明します。

