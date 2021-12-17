---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/04/2020
ms.author: alkohli
ms.openlocfilehash: 2b01b4f11ac7777075848287626e021b89254acb
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108758297"
---
VM の作成時に渡した IP 経由でリモート デスクトップ プロトコル (RDP) を使用して、Windows VM に接続します。

1. お使いのクライアントで、RDP を開きます。 
1. **[スタート]** に移動し、「**mstsc**」と入力します。
1. **[リモート デスクトップ接続]** ウィンドウで、VM テンプレート パラメーター ファイルで使用した VM の IP アドレスとアクセス資格情報を入力します。 次に、 **[接続]\(Connect\)** を選択します。

   ![RDP 経由で Windows VM に接続するための [リモート デスクトップ接続] ウィンドウのスクリーンショット。](media/azure-stack-edge-gateway-connect-vm-windows/connect-vm-rdp-1.png)

   > [!NOTE]
   > 信頼されていないコンピューターへの接続の承認が必要となる場合があります。 

これで、アプライアンスで実行されている VM にサインインしました。 
