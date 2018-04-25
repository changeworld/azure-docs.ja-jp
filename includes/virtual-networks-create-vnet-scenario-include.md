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
ms.openlocfilehash: cff737b8c79c44494cb0151d6a6281550401b26e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2018
---
## <a name="scenario"></a>シナリオ

VNet とサブネットの作成方法をわかりやすく説明するため、このドキュメントでは次のシナリオを使用しています。

![VNet のシナリオ](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

このシナリオでは、予約済み CIDR ブロック **192.168.0.0./16** を含む **TestVNet** という名前の VNet を作成しています。 この VNet には次のサブネットが含まれています。 

* CIDR ブロックとして **192.168.1.0/24** を使用する **FrontEnd**。
* CIDR ブロックとして **192.168.2.0/24** を使用する **BackEnd**。

