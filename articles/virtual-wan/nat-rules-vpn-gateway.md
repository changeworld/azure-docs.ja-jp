---
title: ゲートウェイの VPN NAT 規則を構成する
titleSuffix: Azure Virtual WAN
description: VWAN VPN ゲートウェイの NAT 規則を構成する方法について説明します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 02/17/2021
ms.author: cherylmc
ms.openlocfilehash: a31b3718eb1baa32aef39474383924efe8cf93b6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101743936"
---
# <a name="configure-nat-rules-for-your-virtual-wan-vpn-gateway---preview"></a>仮想 WAN VPN ゲートウェイの NAT 規則を構成する - プレビュー

> [!IMPORTANT]
> NAT 規則は現在、パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

仮想 WAN VPN ゲートウェイを静的な 1 対 1 の NAT 規則を使用して構成できます。 NAT 規則には、IP アドレスの 1 対 1 の変換を設定するメカニズムが用意されています。 NAT を使用して、互換性のないまたは重複する IP アドレスを持つ 2 つの IP ネットワークを相互接続することができます。 代表的なシナリオは、重複する IP を持つブランチが Azure VNet リソースへのアクセスを必要とすることです。

この構成では、フロー テーブルを使用して、外部 (ホスト) IP アドレスから仮想ネットワーク内のエンドポイント (仮想マシン、コンピューター、コンテナーなど) に関連する内部 IP アドレスにトラフィックをルーティングします。

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram.png" alt-text="アーキテクチャを示す図。":::

## <a name="configure-and-view-rules"></a><a name="view"></a>規則の構成と表示

VPN ゲートウェイ設定における NAT 規則を、いつでも構成および表示できます。

   :::image type="content" source="./media/nat-rules-vpn-gateway/edit-rules.png" alt-text="編集規則を示すスクリーンショット。":::

1. ご使用の仮想ハブに移動します。
1. **[VPN (サイト対サイト)]** を選択します。
1. **[NAT 規則 (編集)]** を選択します。
1. **[Edit NAT Rule]\(NAT 規則の編集\)** ページで、次の値を使用して NAT 規則の **追加、編集、削除** を行うことができます。

   * **名前:** NAT 規則の一意の名前。
   * **タイプ:** 静的。 静的な 1 対 1 の NAT では、内部アドレスと外部アドレスの間に 1 対 1 のリレーションシップが確立されます。
   * **モード:** IngressSnat または EgressSnat。  
      * IngressSnat モード (イングレス ソース NAT とも呼ばれます) は、Azure ハブのサイト対サイト VPN ゲートウェイに入るトラフィックに適用されます。
      * EgressSnat モード (エグレス ソース NAT とも呼ばれます) は、Azure ハブのサイト対サイト VPN ゲートウェイを出ていくトラフィックに適用されます。
   * **InternalMapping:** 外部 IP のセットにマップされる、内部ネットワーク上の送信元 IP のアドレス プレフィックス範囲。 言い換えると、NAT 前のアドレス プレフィックス範囲です。
   * **ExternalMapping:** 送信元 IP がマップされる、外部ネットワーク上の宛先 IP のアドレス プレフィックス範囲。 言い換えると、NAT 後のアドレス プレフィックス範囲です。
   * **リンク接続:** VPN サイトを Azure ハブのサイト対サイト VPN ゲートウェイに仮想的に接続する接続リソース。

### <a name="configuration-considerations"></a>構成に関する考慮事項

* 内部と外部の両方のマッピングのサブネット サイズは、静的な 1 対の 1 NAT の場合は同じである必要があります。
* 必ず、Azure portal で VPN サイトを編集して、**ExternalMapping** プレフィックスを プライベート アドレス空間］ フィールドに追加してください。 現在、BGP が有効になっているサイトは、オンプレミス BGP アナウンサー (デバイスの BGP 設定) に外部マッピング プレフィックスのエントリが含まれていることを確認する必要があります。

## <a name="next-steps"></a>次のステップ

サイト対サイト構成について詳しくは、 [仮想 WAN サイト対サイト接続の構成](virtual-wan-site-to-site-portal.md)に関するページをご覧ください。
