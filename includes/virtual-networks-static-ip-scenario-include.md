---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 02/14/2020
ms.author: genli
ms.openlocfilehash: 280943bd965c4799ce294321129d1088be9c0caf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015930"
---
## <a name="scenario"></a>シナリオ

VM の静的 IP アドレスの構成方法をわかりやすく説明するために、このドキュメントでは次のシナリオを使用しています。

![仮想ネットワークのシナリオ:フロントエンド サブネットとバックエンド サブネット、フロントエンド サブネット用の静的 IP アドレス](./media/virtual-networks-static-ip-scenario-include/static-ip-scenario.png)

このシナリオでは、*FrontEnd* サブネット内に *DNS01* という名前の VM を作成してから、それを静的 IP アドレス *192.168.1.101* を使用するように設定します。
