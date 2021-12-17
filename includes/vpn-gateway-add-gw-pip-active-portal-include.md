---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/16/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ff30bf22f0f435db8ae0888440bb0ea26948bef3
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114481650"
---
4. **[パブリック IP アドレス]** の各値を指定します。 これらの設定では、VPN ゲートウェイに関連付けられるパブリック IP アドレス オブジェクトを指定します。 パブリック IP アドレスは、VPN ゲートウェイの作成時に、このオブジェクトに対して動的に割り当てられます。 [ゾーン冗長](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md)でないゲートウェイの場合、パブリック IP アドレスが変わるのは、ゲートウェイが削除され、再度作成されるときのみです。 VPN ゲートウェイのサイズ変更、リセット、その他の内部メンテナンス/アップグレードでは、IP アドレスは変わりません。

   :::image type="content" source="./media/vpn-gateway-add-gw-pip-active-portal/pip-details.png" alt-text="[パブリック IP アドレス] フィールドのスクリーンショット。":::

     * **[パブリック IP アドレス]** : **[新規作成]** を選択しておいてください。
     * **パブリック IP アドレス名**:このテキスト ボックスに、パブリック IP アドレス インスタンスの名前を入力します。
     * **割り当て**: VPN ゲートウェイでは動的のみがサポートされます。
     * **[アクティブ/アクティブ モードの有効化]** : **[有効]** を選択します。
     * **[Second Public IP Address]\(セカンド パブリック IP アドレス\)** : **[新規作成]** を選択します。
     * **[パブリック IP アドレス名]** : セカンド パブリック IP アドレスの名前です。
     * **[Configure BGP]\(BGP の構成\)** の設定は、実際の構成で特に必要でない限り、 **[無効]** のままにしておいてください。 この設定が必要である場合、既定の ASN は 65515 です。ただし、これは変わる場合があります。
5. **[確認と作成]** を選択して検証を実行します。
6. 検証に合格したら、 **[作成]** を選択して VPN ゲートウェイをデプロイします。