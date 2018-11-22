---
author: kumudD
ms.service: load-balancer
ms.topic: include
ms.date: 11/09/2018
ms.author: kumud
ms.openlocfilehash: bf3aee30460e052db23cf9090f13e2af3b22628b
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2018
ms.locfileid: "52270984"
---
このシナリオでは、次のタスクを実行します。

* ポート 80 でネットワーク トラフィックを受信するロード バランサーを作成し、負荷分散されたトラフィックを仮想マシン "web1" と "web2" に送信する
* ロード バランサーの背後にある仮想マシンのリモート デスクトップ アクセス/ SSH 用の NAT 規則を作成する
* 正常性プローブを作成する

![ロード バランサーのシナリオ](./media/load-balancer-get-started-internet-scenario-include/scenario-classic.png)
