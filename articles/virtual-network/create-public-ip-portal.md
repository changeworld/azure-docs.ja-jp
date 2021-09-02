---
title: パブリック IP の作成 - Azure portal
titleSuffix: Azure Virtual Network
description: Azure portal を使用してパブリック IP を作成する方法について説明します
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/04/2021
ms.author: allensu
ms.openlocfilehash: 6cf9e86222d98835ea5355440343df96794dbfd4
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2021
ms.locfileid: "113435070"
---
# <a name="create-a-public-ip-address-using-the-azure-portal"></a>Azure portal を使用してパブリック IP アドレスを作成する

この記事では、Azure portal を使用してパブリック IP アドレス リソースを作成する方法について説明します。 

このパブリック IP を関連付けることができるリソース、Basic と Standard SKU の違いについては、「[パブリック IP アドレス](./public-ip-addresses.md)」を参照してください。 

## <a name="create-a-standard-sku-public-ip-address"></a>Standard SKU のパブリック IP アドレスを作成する

次の手順を使用して、**myStandardPublicIP** という名前の Standard パブリック IPv4 アドレスを作成します。  

> [!NOTE]
>IPv6 アドレスを作成するには、 **[IP バージョン]** パラメーターに **[IPv6]** を選択します。 デプロイでデュアル スタック構成 (IPv4 と IPv6 アドレス) が必要な場合は、 **[両方]** を選択します。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. ポータルの上部にある検索ボックスに、「**パブリック IP**」と入力します。

3. 検索結果から **[パブリック IP アドレス]** を選択します。

4. **[+ 作成]** を選択します。

5. **[パブリック IP アドレスの作成]** で、次の情報を入力または選択します。

    | 設定                 | 値                       |
    | ---                     | ---                         |
    | IP バージョン              | [IPv4] を選択します。              |    
    | SKU                     | **[Standard]** を選択します。         |
    | レベル                   | **[地域]** を選択します。    </br> 詳細については、「[ルーティングの優先順位と階層](#routing-preference-and-tier)」を参照してください。     |
    | 名前                    | 「**myStandardPublicIP**」と入力します。          |
    | IP アドレスの割り当て   | **[静的]** としてロックされます。                |
    | ルーティングの優先順位     | **[Microsoft ネットワーク]** を選択します。 </br> 詳細については、「[ルーティングの優先順位と階層](#routing-preference-and-tier)」を参照してください。 |
    | アイドル タイムアウト (分)  | 既定値の **[4]** のままにします。        |
    | DNS 名ラベル          | この値は空白のままにします。    |
    | サブスクリプション            | サブスクリプションを選択します。   |
    | Resource group          | **[新規作成]** を選択し、「**MyResourceGroup**」と入力します。 </br> **[OK]** を選択します。 |
    | 場所                | **[(米国) 米国東部 2]** を選択します。     |
    | 可用性ゾーン       | **[ゾーンなし]** を選択します。 |

6. **［作成］** を選択します

:::image type="content" source="./media/create-public-ip-portal/create-standard-ip.png" alt-text="Azure portal での Standard IP アドレスの作成" border="false":::

> [!NOTE]
> [Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) があるリージョンでは、ゾーンなし (既定のオプション)、特定のゾーン、またはゾーン冗長を選択できます。 この選択は、特定のドメイン障害要件によって異なる場合があります。 Availability Zones がないリージョンでは、このフィールドは表示されません。 </br> 可用性ゾーンの詳細については、[可用性ゾーンの概要](../availability-zones/az-overview.md)に関するページを参照してください。

> [!NOTE]
> IPv6 は現在、ルーティングの優先順位またはリージョン間の負荷分散 (グローバル階層) ではサポートされていません。

## <a name="create-a-basic-sku-public-ip-address"></a>Basic SKU のパブリック IP アドレスを作成する

このセクションでは、**myBasicPublicIP** という名前の Basic パブリック IPv4 アドレスを作成します。 

> [!NOTE]
> Basic パブリック IP では、Availability Zones はサポートされません。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. **[リソースの作成]** を選択します。 
3. 検索ボックスに「**パブリック IP アドレス**」と入力します。 検索結果から **[パブリック IP アドレス]** を選択します。
4. **[パブリック IP アドレス]** ページで、 **[作成]** を選択します。
5. **[パブリック IP アドレスの作成]** ページで、次の情報を入力または選択します。 

    | 設定                 | 値                       |
    | ---                     | ---                         |
    | IP バージョン              | [IPv4] を選択します。                 |    
    | SKU                     | **[Basic]** を選択します         |
    | 名前                    | 「**myBasicPublicIP**」と入力します。          |
    | IP アドレスの割り当て   | **[静的]** を選択します。            |
    | アイドル タイムアウト (分)  | 既定値の **[4]** のままにします。       |
    | DNS 名ラベル          | この値は空白のままにします。    |
    | サブスクリプション            | サブスクリプションを選択します。   |
    | Resource group          | **[新規作成]** を選択し、「**MyResourceGroup**」と入力します。 </br> **[OK]** を選択します。 |
    | 場所                | **[(米国) 米国東部 2]** を選択します。      |

6. **［作成］** を選択します

:::image type="content" source="./media/create-public-ip-portal/create-basic-ip.png" alt-text="Azure portal での Basic IP アドレスの作成" border="false":::

時間の経過と共に IP アドレスが変更されても問題ない場合は、AllocationMethod を **Dynamic** に変更することで **動的** IP の割り当てを選択できます。 

---

## <a name="routing-preference-and-tier"></a>ルーティングの優先順位と階層

Standard SKU の静的パブリック IPv4 アドレスでは、ルーティングの優先順位またはグローバル階層の機能がサポートされます。 新しいパブリック IP アドレスを作成するときに、ルーティングの優先順位と階層を選択します。

### <a name="routing-preference"></a>ルーティングの優先順位

既定では、パブリック IP アドレスのルーティングの優先順位は "Microsoft ネットワーク" に設定され、Microsoft のグローバル ワイド エリア ネットワークを通してユーザーにトラフィックが配信されます。  

**インターネット** を選択すると、Microsoft のネットワーク上での移動が最小限に抑えられます。その代わりに、トラフィックをコスト最適化された速度で配信するために、転送 ISP ネットワークを使用します。  

ルーティングの優先順位の詳細については、「[ルーティングの優先順位とは (プレビュー)](./routing-preference-overview.md)」を参照してください。

:::image type="content" source="./media/create-public-ip-portal/routing-preference.png" alt-text="Azure portal でルーティングの優先順位を構成する" border="false":::

### <a name="tier"></a>階層

パブリック IP アドレスは、1 つのリージョンに関連付けされます。 **グローバル** 階層では、1 つの IP アドレスが複数のリージョンにまたがって使用されます。 リージョン間ロード バランサーのフロントエンドには、**グローバル** 階層が必要です。  

詳細については、[リージョン間ロード バランサー](../load-balancer/cross-region-overview.md)に関するページを参照してください。

:::image type="content" source="./media/create-public-ip-portal/tier.png" alt-text="Azure portal で階層を構成する" border="false":::

## <a name="additional-information"></a>関連情報 

上に示した個々のフィールドの詳細については、「[パブリック IP アドレスの管理](./virtual-network-public-ip-address.md#create-a-public-ip-address)」を参照してください。

## <a name="next-steps"></a>次のステップ
- [仮想マシンにパブリック IP アドレス](./associate-public-ip-address-vm.md#azure-portal)を関連付ける
- Azure の[パブリック IP アドレス](./public-ip-addresses.md#public-ip-addresses)について詳しく学習する。
- すべての[パブリック IP アドレスの設定](virtual-network-public-ip-address.md#create-a-public-ip-address)について詳しく学習する。