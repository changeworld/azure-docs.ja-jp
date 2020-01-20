---
title: 'Virtual WAN: NVA への仮想ハブ ルート テーブルを作成する: Azure portal'
description: ポータルを使用して、トラフィックをネットワーク仮想アプライアンスに誘導するための Virtual WAN 仮想ハブ ルート テーブル。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: c0681024b60827cf589906041c264d912ab209bb
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2020
ms.locfileid: "75612362"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>NVA 用の Virtual WAN ハブ ルート テーブルを作成する:Azure portal

この記事では、Virtual WAN ハブに接続されているブランチ (オンプレミス サイト) から、ネットワーク仮想アプライアンス (NVA) を介してスポーク VNet にトラフィックを誘導する方法について説明します。

![Virtual WAN のダイアグラム](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>開始する前に

以下の条件を満たしていることを確認します。

*  ネットワーク仮想アプライアンス (NVA) があります。 ネットワーク仮想アプライアンス (NVA) とは、仮想ネットワークの Azure Marketplace から通常プロビジョニングする、ご自分で選択するサードパーティ製のソフトウェアです。

    * プライベート IP アドレスが NVA ネットワーク インターフェイスに割り当てられています。

    * NVA が仮想ハブにデプロイされていません。 独立した VNet 内にデプロイする必要があります。

    *  NVA VNet に、1 つまたは多数の仮想ネットワークが接続されています。 この記事では、NVA VNet を '間接スポーク VNet' と呼びます。 これらの VNet は、VNet ピアリングを使用して NVA VNet に接続できます。 VNet ピアリング リンクは、上の図の Vnet 1、Vnet 2 と、NVA Vnet との間の黒い矢印によって示されています。
*  2 つ VNet を作成しています。 これらは、スポーク VNet として使用されます。

    * この演習では、VNet スポークのアドレス空間は次のとおりです。VNet1:10.0.2.0/24 と VNet2:10.0.3.0/24。 VNet の作成方法に関する情報が必要な場合は、[仮想ネットワークの作成](../virtual-network/quick-create-portal.md)に関するページを参照してください。

    * すべての VNet 内に仮想ネットワーク ゲートウェイが存在しないようにします。
    * この構成では、これらの VNet にゲートウェイ サブネットは必要ありません。

## <a name="signin"></a>1.サインイン

ブラウザーから [Azure ポータル](https://portal.azure.com) に移動し、Azure アカウントでサインインします。

## <a name="vwan"></a>2.仮想 WAN を作成する

仮想 WAN を作成します。 この演習のために、次の値を使用できます。

* **Virtual WAN 名:** myVirtualWAN
* **リソース グループ:** testRG
* **[場所]:** 米国西部

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3.ハブを作成する

ハブを作成します。 この演習のために、次の値を使用できます。

* **[場所]:** 米国西部
* **名前:** westushub
* **ハブのプライベート アドレス空間:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="route"></a>4.ハブのルート テーブルの作成および適用

ハブ ルート テーブルを使用して、ハブを更新します。 この演習のために、次の値を使用できます。

* **スポーク VNet アドレス空間:** (VNet1 と VNet2) 10.0.2.0/24 と 10.0.3.0/24
* **DMZ NVA ネットワーク インターフェイスのプライベート IP アドレス:** 10.0.4.5

1. お使いの仮想 WAN に移動します。
2. ルート テーブルを作成するハブをクリックします。
3. **[...]** 、 **[仮想ハブを編集する]** の順にクリックします。
4. **[仮想ハブを編集する]** ページを下にスクロールして、 **[ルーティングにテーブルを使用]** のチェックボックスをオンにします。
5. **[次の宛先プレフィックスの場合]** 列にアドレス空間を追加します。 **[次ホップ アドレスに送信する]** 列で DMZ NVA ネットワーク インターフェイスのプライベート IP アドレスを追加します。
6. **[確認]** をクリックして、ルート テーブルの設定を使用してハブ リソースを更新します。

## <a name="connections"></a>5.VNet 接続を作成する

それぞれの間接スポーク VNet (VNet1 と VNet2) からハブに VNet 接続を作成します。 これらの VNet 接続は、上の図の青い矢印で示されています。 次に、NVA VNet からハブへの VNet 接続 (図の黒い矢印) を作成します。 

 この手順では、次の値を使用します。

| VNet の名前| [接続名]|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

接続する VNet ごとに次の手順を繰り返します。

1. 仮想 WAN のページで、 **[仮想ネットワーク接続]** をクリックします。
2. 仮想ネットワーク接続のページで、 **[+ 接続の追加]** をクリックします。
3. **[接続の追加]** ページで、次のフィールドに入力します。

    * **[接続名]** - 接続に名前を付けます。
    * **[ハブ]** - この接続に関連付けるハブを選択します。
    * **[サブスクリプション]** - サブスクリプションを確認します。
    * **[仮想ネットワーク]** - このハブに接続する仮想ネットワークを選択します。 仮想ネットワークに既存の仮想ネットワーク ゲートウェイを設定することはできません。
4. **[OK]** をクリックして、接続を作成します。

## <a name="next-steps"></a>次のステップ

Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。
