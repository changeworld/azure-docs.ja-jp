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
ms.openlocfilehash: 873549442284ede2e9f020bd90879f721b9c1a18
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38760362"
---
## <a name="scenario"></a>シナリオ
NSG の作成方法をわかりやすく説明するため、このドキュメントでは次のシナリオを使用しています。

![VNet のシナリオ](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

このシナリオでは、以下のように、**TestVNet** 仮想ネットワークの各サブネットに対して NSG を作成します。 

* **NSG-FrontEnd**。 以下の 2 つの規則を含むフロントエンド NSG が *FrontEnd* サブネットに適用されています。    
  * **rdp-rule**。 *FrontEnd* サブネットに対する RDP トラフィックを許可します。
  * **web-rule**。 *FrontEnd* サブネットに対する HTTP トラフィックを許可します。
* **NSG-BackEnd**。 以下の 2 つの規則を含むバックエンド NSG が *BackEnd* サブネットに適用されています。    
  * **sql-rule**。 *FrontEnd* サブネットからの SQL トラフィックのみを許可します。
  * **web-rule**。 *BackEnd* サブネットからのすべてのインターネット経由トラフィックを拒否します。

これらの規則の組み合わせによって、DMZ のようなシナリオが作成されます。このシナリオではバックエンドのサブネットはフロントエンドのサブネットから SQL の受信トラフィックのみを受信でき、インターネットへはアクセスできません。一方、フロントエンドのサブネットはインターネットと通信でき、受信 HTTP 要求のみを受信します。

