---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/04/2020
ms.author: alkohli
ms.openlocfilehash: 8d6c3125d0109ae9005414add27f9aa08e932e49
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89082035"
---
リモート デスクトップ プロトコル (RDP) を使用して、VM の作成時に渡した IP を経由して Windows VM に接続します。

1. クライアントで RDP を起動します。 **[スタート]** に移動し、「**mstsc**」と入力します。
1. VM の IP アドレスと、VM テンプレートのパラメーター ファイルで使用したアクセス資格情報を入力します。

    ![RDP 経由で Windows VM に接続する](media/azure-stack-edge-gateway-connect-vm-windows/connect-vm-rdp-1.png)
1. 信頼されていないコンピューターへの接続を承認する必要がある場合があります。 
1. これで、アプライアンスで実行されている VM にログインされました。 