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
ms.openlocfilehash: 29b2ac1a5dc128028dbd40e683c1b45e6208d124
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
## <a name="scenario"></a>シナリオ

VNet とサブネットの作成方法をわかりやすく説明するため、このドキュメントでは次のシナリオを使用しています。

![VNet のシナリオ](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

このシナリオでは、予約済み CIDR ブロック **192.168.0.0./16** を含む **TestVNet** という名前の VNet を作成しています。 この VNet には次のサブネットが含まれています。 

* CIDR ブロックとして **192.168.1.0/24** を使用する **FrontEnd**。
* CIDR ブロックとして **192.168.2.0/24** を使用する **BackEnd**。

