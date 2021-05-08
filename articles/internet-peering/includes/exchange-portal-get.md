---
title: インクルード ファイル
titleSuffix: Azure
description: インクルード ファイル
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e139954e6550e33edb75d01ab9dbec0bba543ea6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92548226"
---
**ピアリング** リソースが正常にデプロイされたら、次の手順に従って表示することができます。

1. **[リソース グループ]** に移動し、**ピアリング** リソースの作成時に選択したリソース グループを選択します。 リソース グループが多すぎる場合は、**フィルター** ボックスを使用します。

    > [!div class="mx-imgBorder"]
    > ![リソース グループ](../media/setup-direct-get-resourcegroup.png)

1. 作成した **ピアリング** リソースを選択します。

    > [!div class="mx-imgBorder"]
    > ![作成したピアリング リソースを示すスクリーンショット。](../media/setup-direct-get-open.png)

1. **[概要]** ページには、次に示すように概要情報が表示されます。

    > [!div class="mx-imgBorder"]
    > ![ピアリング リソースの [概要] ウィンドウ](../media/setup-exchange-get-overview.png)

1. 左側で、 **[ASN information]\(ASN 情報\)** を選択して、PeerAsn を作成したときに送信された情報を表示します。

    > [!div class="mx-imgBorder"]
    > ![ピアリング リソースの [ASN information]\(ASN 情報\)](../media/setup-direct-get-asninfo.png)

1. 左側で、 **[接続]** を選択します。 画面上部で、都市内のさまざまな施設にわたる、ASN と Microsoft の間のピアリング接続の概要を確認します。 また、次のように中央のウィンドウで **[接続]** を選択することで、 **[概要]** ページから接続の概要にアクセスすることもできます。

    > [!div class="mx-imgBorder"]
    > ![ピアリング リソースの [接続]](../media/setup-exchange-get-connectionssummary.png)

    * **[接続状態]** は、ピアリング接続の設定の状態に対応しています。 このフィールドに表示される状態は、「[Exchange ピアリングのチュートリアル](../walkthrough-exchange-all.md)」に示されている状態ダイアグラムに従います。
    * **IPv4 セッション状態** と **IPv6 セッション状態** は、それぞれ IPv4 と IPv6 BGP のセッション状態に対応しています。  
    * 画面上部の行を選択すると、下部の **[接続]** セクションに各接続の詳細が表示されます。 矢印を選択して、 **[構成]** 、 **[IPv4 アドレス]** 、 **[IPv6 アドレス]** を展開します。

    > [!div class="mx-imgBorder"]
    > ![セクションを展開する矢印が強調表示されているスクリーンショット。](../media/setup-exchange-get-connectionsipv4.png)
