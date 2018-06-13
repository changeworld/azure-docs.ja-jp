---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 158400645423d2e9fe92768786b570e917907d98
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
ms.locfileid: "30921518"
---
1. ポータルで、仮想ネットワーク ゲートウェイを作成する仮想ネットワークに移動します。
2. VNet のページの **[設定]** セクションで、**[サブネット]** をクリックして [サブネット] ページを展開します。
3. **[サブネット]** ページの上部にある **[+ゲートウェイ サブネット]** をクリックして、**[サブネットの追加]** ページを開きます。

  ![ゲートウェイ サブネットの追加](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet.png "ゲートウェイ サブネットの追加")
4. サブネットの **[名前]** には、"GatewaySubnet" という値が自動的に入力されます。 この値 GatewaySubnet は、Azure がゲートウェイ サブネットとしてこのサブネットを認識するために必要になります。 自動入力される **[アドレス範囲]** の値は、実際の構成要件に合わせて調整してください。

  ![ゲートウェイ サブネットの追加](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "ゲートウェイ サブネットの追加")
5. サブネットを作成するには、ページ下部の **[OK]** をクリックします。