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
ms.openlocfilehash: b967c844ab145074490e931122cbe092d67de0c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75773742"
---
**ピアリング** リソースが正常にデプロイされたら、次の手順に従って表示することができます。

1. **[リソース グループ]** に移動し、**ピアリング** リソースの作成時に選択したリソース グループをクリックします。 リソース グループが多すぎる場合は、 *[フィルター]* フィールドを使用できます。

    > [!div class="mx-imgBorder"]
    > ![ピアリング リソース グループ](../media/setup-direct-get-resourcegroup.png)

1. 作成した**ピアリング** リソースをクリックします。

    > [!div class="mx-imgBorder"]
    > ![ピアリング リソース ビュー](../media/setup-direct-get-open.png)

1. **[概要]** ページには、概要情報が表示されます。 以下の強調表示されている情報を確認してください。

    > [!div class="mx-imgBorder"]
    > ![ピアリング リソース ビュー](../media/setup-exchange-get-overview.png)

1. 左側の **[ASN 情報]** をクリックすると、PeerAsn を作成しているときに送信された情報が表示されます

    > [!div class="mx-imgBorder"]
    > ![ピアリング リソース ビュー](../media/setup-direct-get-asninfo.png)

1. 左側の **[接続]** をクリックします。 上部で、Metro 内のさまざまな施設にわたる、ASN と Microsoft の間のピアリング接続の概要を確認します。 また、上で強調表示されている中央のペインで **[接続]** をクリックして、 **[概要]** ページから接続の概要を表示することもできます。

    > [!div class="mx-imgBorder"]
    > ![ピアリング リソース ビュー](../media/setup-exchange-get-connectionssummary.png)

    * **[接続状態]** は、ピアリング接続設定の状態に対応しています。 このフィールドに表示される状態は、[Exchange ピアリングのチュートリアル](../walkthrough-exchange-all.md)に示されている状態ダイアグラムに従います。
    * **IPv4 セッション状態**と **IPv6 セッション状態**は、それぞれ IPv4 と IPv6 BGP セッションの状態に対応しています。  
    * 上部の行を選択すると、下部の ***[接続]*** セクションに各接続の詳細が表示されます。 矢印マークをクリックすると、***[構成]***、***[IPv4 アドレス]***、および ***[IPv6 アドレス]*** のサブセクションを展開できます

    > [!div class="mx-imgBorder"]
    > ![ピアリング リソース ビュー](../media/setup-exchange-get-connectionsipv4.png)
