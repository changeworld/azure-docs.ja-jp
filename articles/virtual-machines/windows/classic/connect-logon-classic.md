---
title: クラシック Azure VM へのログオン | Microsoft Docs
description: Azure Portal を使用して、クラシック デプロイ モデルを使用して作成された Windows 仮想マシンにログオンします。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 3c1239ed-07dc-48b8-8b3d-dc8c5f0ab20e
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 87ecc65d2d4802ae826f3260b66b26e0bbe414e6
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012361"
---
# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-portal"></a>Azure ポータルを使用して Windows 仮想マシンにログオンする
Azure ポータルの **[接続]** ボタンを使用して、リモート デスクトップ セッションを開始し、Windows VM にログオンします。

Linux VM に接続する場合は、 「[Linux が実行されている仮想マシンにログオンする方法](../../linux/mac-create-ssh-keys.md)」を参照してください。

<!--
Deleting, but not 100% sure
Learn how to [perform these steps using new Azure portal](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
-->

> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイメントでは、リソース マネージャー モデルを使用することをお勧めします。 Resource Manager モデルを使用した VM へのログオン方法については、[こちら](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)を参照してください。
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="connect-to-the-virtual-machine"></a>仮想マシンへの接続
1. Azure ポータルにサインインします。
2. アクセスする仮想マシンをクリックします。 名前は **[すべてのリソース]** ウィンドウに一覧表示されます。

    ![Virtual-machine-locations](./media/connect-logon/azureportaldashboard.png)

1. 仮想マシンのプロパティ ページで、**[接続]** ボタンをクリックします。 
2. **[仮想マシンへの接続]** ページで、適切なオプションを選択したままの状態で、**[RDP ファイルのダウンロード]** をクリックします。
2. ダウンロードした RDP ファイルを開き、プロンプトが表示されたら **[接続]** をクリックします。 
2. `.rdp` ファイルの発行元が不明であることを示す警告が表示されます。 問題はありません。 リモート デスクトップ ウィンドウで、 **[接続]** をクリックして続行します。
   
    ![Screenshot of a warning about an unknown publisher.](./media/connect-logon/rdp-warn.png)
3. **[Windows セキュリティ]** ウィンドウで、**[その他]**、**[別のアカウントを使用する]** の順に選択します。 仮想マシンのアカウントの資格情報を入力し、**[OK]** をクリックします。
   
     **ローカル アカウント** - 通常は、仮想マシンの作成時に指定したローカル アカウントのユーザー名とパスワードです。 この場合、ドメインは仮想マシンの名前です。これを *vmname*&#92;*username* の形式で入力します。  
   
    **ドメインに参加している VM** - VM がドメインに属している場合は、*Domain*&#92;*Username* の形式でユーザー名を入力します。 また、アカウントは管理者グループに属しているか、VM へのリモート アクセス特権が付与されている必要があります。
   
    **ドメイン コントローラー** - VM がドメイン コントローラーである場合は、そのドメインのドメイン管理者アカウントのユーザー名とパスワードを入力します。
4. **[はい]** をクリックして、目的の仮想マシンであることを確認し、ログオンを完了します。
   
   ![VM の ID の検証に関するメッセージが表示されているスクリーンショット。](./media/connect-logon/cert-warning.png)

## <a name="next-steps"></a>次の手順
* **[接続]** ボタンが非アクティブである場合、またはリモート デスクトップ接続に関するその他の問題が発生している場合は、構成をリセットします。 仮想マシンのダッシュボードで、**[リモート アクセスのリセット]** をクリックします。

    ![Reset-remote-access](./media/connect-logon/virtualmachine_dashboard_reset_remote_access.png)

* パスワードに問題がある場合は、パスワードをリセットしてください。 仮想マシンのダッシュボードの左側のウィンドウで、**[サポートとトラブルシューティング]** の下にある **[パスワードのリセット]** をクリックします。

    ![Reset-password](./media/connect-logon/virtualmachine_dashboard_reset_password.png)

これらのトピックで解決できない場合、または必要な情報が掲載されていない場合は、「[Windows ベースの Azure 仮想マシンへのリモート デスクトップ接続に関するトラブルシューティング](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。 この記事では、一般的な問題の診断と解決の手順について説明します。
