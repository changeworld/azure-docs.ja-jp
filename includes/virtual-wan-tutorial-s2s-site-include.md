---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/11/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 708baa83ca919adcc374be36c229ce3ff30da384
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2021
ms.locfileid: "100362942"
---
1. 仮想 WAN のポータル ページの **[接続]** セクションで、 **[VPN サイト]** を選択して VPN サイト ページを開きます。
1. **[VPN サイト]** ページで **[+ サイトの作成]** をクリックします。
1. **[VPN サイトを作成する]** ページの **[基本]** タブで、次のフィールドを入力します。

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/site-basics.png" alt-text="[基本] タブ" lightbox="./media/virtual-wan-tutorial-site-include/site-basics.png":::

    * **[リージョン]** - 旧称は場所。 これは、このサイト リソースを作成する場所です。
    * **[名前]** - オンプレミスのサイトの呼称。
    * **[デバイス ベンダー]** - VPN デバイス ベンダーの名前 (例:Citrix、Cisco、Barracuda)。 デバイス ベンダーを追加すると、Azure チームがお客様の環境をよりよく理解し、将来の最適化の可能性を追加したり、トラブルシューティングを行ったりするのに役立ちます。
    * **[プライベート アドレス空間]** - オンプレミスのサイトにある IP アドレス空間です。 このアドレス空間宛てのトラフィックは、ローカル サイトにルーティングされます。 これは、サイトで BGP が有効になっていない場合に必要です。
    
      >[!NOTE]
      >サイトを作成した後にアドレス空間を編集した場合 (追加のアドレス空間を追加した場合など)、コンポーネントの再作成中での有効なルートの更新には、8 分から 10 分かかることがあります。
      >
1. ブランチにある物理的なリンクに関する情報を追加するには、 **[リンク]** を選択します。 Virtual WAN パートナーの CPE デバイスがある場合は、システムから設定されたブランチ情報のアップロードの一部として、この情報が Azure と交換されるかどうかを確認します。

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/site-links.png" alt-text="[リンク] タブ" lightbox="./media/virtual-wan-tutorial-site-include/site-links.png":::

   * **[リンク名]** - VPN サイトで物理リンクに付ける名前を指定します。 例: mylink1。
   * **[リンク速度]** - これは、ブランチの場所での VPN デバイスの速度です。 例:50。50 Mbps は、ブランチ サイトの VPN デバイスの速度を示します。
   * **[Link provider name]\(リンク プロバイダー名\)** - VPN サイトの物理リンクの名前。 例:ATT、Verizon。
   * **[Link IP address/FQDN]\(リンクの IP アドレスまたは FQDN\)** - このリンクを使用したオンプレミス デバイスのパブリック IP アドレス。 必要に応じて、ExpressRoute の背後にあるオンプレミスの VPN デバイスのプライベート IP アドレスを指定できます。 完全修飾ドメイン名を指定することもできます。 たとえば、「*something.contoso.com*」と入力します。 FQDN は VPN ゲートウェイから解決できる必要があります。 この FQDN のホストとなる DNS サーバーにインターネットから到達できれば、それが可能となります。 IP アドレスと FQDN の両方を指定した場合は、IP アドレスが優先されます。

     >[!NOTE]
     >
     >* サポートされる IPv4 アドレスは FQDN ごとに 1 つです。 FQDN が複数の IP アドレスに解決された場合、それらのアドレスから最初の IP4 アドレスが VPN ゲートウェイによって選択されます。 現時点では、IPv6 アドレスはサポートされていません。
     >
     >* VPN ゲートウェイには、5 分おきに更新される DNS キャッシュがあります。 ゲートウェイが FQDN の解決を試みるのは、切断されたトンネルのみです。 ゲートウェイのリセットや構成の変更でも、FQDN の解決がトリガーされます。
     >
   * **[Link Border Gateway Protocol]\(リンク Border Gateway Protocol\)** - 仮想 WAN リンクでの BGP の構成は、Azure 仮想ネットワーク ゲートウェイ VPN での BGP の構成と同等です。 オンプレミスの BGP ピア アドレスをデバイス側のご使用の VPN および VPN サイトの VNet アドレス空間のパブリック IP アドレスと同じにすることはできません。 VPN デバイスでは BGP ピア IP に別の IP アドレスを使用してください。 デバイスのループバック インターフェイスに割り当てられたアドレスを使用できます。 場所を表す対応する VPN サイトで、このアドレスを指定します。  BGP の前提条件については、「[BGP と Azure VPN Gateway について](../articles/vpn-gateway/vpn-gateway-bgp-overview.md)」を参照してください。 いつでも VPN リンク接続を編集して、BGP パラメーター (リンクのピアリング IP と AS 番号) を更新することができます。
1. リンクを追加したり削除したりすることができます。 VPN サイトごとに 4 つのリンクがサポートされています。 たとえば、ブランチの場所に ISP (インターネット サービス プロバイダー) が 4 つある場合は、各 ISP に 1 つずつ、合計 4 つのリンクを作成し、各リンクの情報を入力できます。
1. フィールドの入力が完了したら、 **[確認と作成]** を選択し、サイトを確認して作成します。
1. 目的の仮想ハブに移動し、 **[ハブの関連付け]** の選択を解除して VPN サイトをハブに接続します。

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/connect.png" alt-text="このハブに接続する" lightbox="./media/virtual-wan-tutorial-site-include/connect.png":::
