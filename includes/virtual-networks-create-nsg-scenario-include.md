---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-network
author: genli
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 09c6871fc5243296da2f2defd594afb80c62ac95
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
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

