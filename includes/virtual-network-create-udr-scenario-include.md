---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 40b81904daabfdad7e45571d8ab86cf32cac8964
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181104"
---
## <a name="scenario"></a>シナリオ
UDR の作成方法をわかりやすく説明するため、このドキュメントでは次のシナリオを使用しています。

![イメージの説明](./media/virtual-network-create-udr-scenario-include/figure1.png)

このシナリオでは、以下に示すように、*フロントエンドのサブネット*用に UDR を 1 つ作成し、*バックエンドのサブネット*用に別の UDR を作成します。 

* **UDR-FrontEnd**。 次のルートを 1 つ含むフロントエンド UDR が *FrontEnd* サブネットに適用されています。    
  * **RouteToBackend**。 このルートは、バックエンドのサブネットへのすべてのトラフィックを **FW1** 仮想マシンに送信します。
* **UDR-BackEnd**。 次のルートを 1 つ含むバックエンド UDR が *BackEnd* サブネットに適用されています。    
  * **RouteToFrontend**。 このルートは、フロントエンドのサブネットへのすべてのトラフィックを **FW1** 仮想マシンに送信します。

これらのルートの組み合わせにより、あるサブネットから別のサブネットに宛てたすべてのトラフィックが、確実に **FW1** 仮想マシン (仮想アプライアンスとして使用されている) にルーティングされます。 また、**FW1** VM の IP 転送を有効にして、他の VM 宛てのトラフィックを確実に受信できるようにする必要があります。

