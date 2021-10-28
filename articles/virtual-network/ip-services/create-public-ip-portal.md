---
title: 'クイックスタート: パブリック IP アドレスの作成 - Azure portal'
titleSuffix: Azure Virtual Network
description: このクイックスタートでは、Standard または Basic SKU のパブリック IP アドレスを作成する方法について説明します。 また、ルーティング優先順位とレベルについても説明します。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: quickstart
ms.date: 10/01/2021
ms.custom: template-quickstart
ms.openlocfilehash: d45e48120c69eeb41ec8bf741e32437f77d2ec69
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130225026"
---
# <a name="quickstart-create-a-public-ip-address-using-the-azure-portal"></a>クイック スタート:Azure portal を使用してパブリック IP アドレスを作成する

このクイックスタートでは、Azure パブリック IP アドレスを作成する方法について説明します。 Azure のパブリック IP アドレスは、Azure リソースへのパブリック接続に使用されます。 パブリック IP アドレスは Basic と Standard の 2 つの SKU で使用できます。 リージョンとグローバルの 2 つのレベルのパブリック IP アドレスを使用できます。  パブリック IP アドレスのルーティング優先順位は、作成時に設定されます。 インターネット ルーティングと Microsoft ネットワーク ルーティングが利用可能な選択肢です。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

---

# <a name="standard-sku"></a>[**Standard SKU**](#tab/option-1-create-public-ip-standard)

>[!NOTE]
>運用環境のワークロードには Standard SKU のパブリック IP が推奨されます。  SKU の詳細については、「 **[パブリック IP アドレス](public-ip-addresses.md)** 」を参照してください。

## <a name="create-a-standard-sku-public-ip-address"></a>Standard SKU のパブリック IP アドレスを作成する

次の手順を使用して、**myStandardPublicIP** という名前の Standard パブリック IPv4 アドレスを作成します。 

> [!NOTE]
>IPv6 アドレスを作成するには、 **[IP バージョン]** パラメーターに **[IPv6]** を選択します。 デプロイでデュアル スタック構成 (IPv4 と IPv6 アドレス) が必要な場合は、 **[両方]** を選択します。

1. ポータルの上部にある検索ボックスに、「**パブリック IP**」と入力します。

2. 検索結果から **[パブリック IP アドレス]** を選択します。

3. **[+ 作成]** を選択します。

4. **[パブリック IP アドレスの作成]** で、次の情報を入力または選択します。

    | 設定                 | 値                       |
    | ---                     | ---                         |
    | IP バージョン              | [IPv4] を選択します。              |    
    | SKU                     | **[Standard]** を選択します。         |
    | レベル                   | **[地域]** を選択します。     |
    | 名前                    | 「**myStandardPublicIP**」と入力します。          |
    | IP アドレスの割り当て   | **[静的]** としてロックされます。                |
    | ルーティングの優先順位     | **[Microsoft ネットワーク]** を選択します。 |
    | アイドル タイムアウト (分)  | 既定値の **[4]** のままにします。        |
    | DNS 名ラベル          | この値は空白のままにします。    |
    | サブスクリプション            | サブスクリプションを選択します。   |
    | Resource group          | **[新規作成]** を選択し、「**QuickStartCreateIP-rg**」と入力します。 </br> **[OK]** を選択します。 |
    | 場所                | **[(米国) 米国東部 2]** を選択します。     |
    | 可用性ゾーン       | **[ゾーンなし]** を選択します。 |

5. **［作成］** を選択します

:::image type="content" source="./media/create-public-ip-portal/create-standard-ip.png" alt-text="Azure portal の Standard IP アドレス作成のスクリーンショット" border="false":::

> [!NOTE]
> [Availability Zones](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) があるリージョンでは、ゾーンなし (既定のオプション)、特定のゾーン、またはゾーン冗長を選択できます。 この選択は、特定のドメイン障害要件によって異なる場合があります。 Availability Zones がないリージョンでは、このフィールドは表示されません。 </br> 可用性ゾーンの詳細については、[可用性ゾーンの概要](../../availability-zones/az-overview.md)に関するページを参照してください。

上で作成したパブリック IP アドレスを、[Windows](../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Linux](../../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の仮想マシンに関連付けることができます。 チュートリアル ページ「[仮想マシンへのパブリック IP アドレスの関連付け](./associate-public-ip-address-vm.md#azure-cli)」の CLI セクションを使用して、パブリック IP アドレスを VM に関連付けます。 上で作成したパブリック IP アドレスを、ロード バランサーの **フロントエンド** 構成に割り当てることで、[Azure Load Balancer](../../load-balancer/load-balancer-overview.md) と関連付けることもできます。 このパブリック IP アドレスは、負荷分散された仮想 IP アドレス (VIP) として機能します。

# <a name="basic-sku"></a>[**Basic SKU**](#tab/option-1-create-public-ip-basic)

>[!NOTE]
>運用環境のワークロードには Standard SKU のパブリック IP が推奨されます。  SKU の詳細については、「 **[パブリック IP アドレス](public-ip-addresses.md)** 」を参照してください。

## <a name="create-a-basic-sku-public-ip-address"></a>Basic SKU のパブリック IP アドレスを作成する

このセクションでは、**myBasicPublicIP** という名前の Basic パブリック IPv4 アドレスを作成します。 

> [!NOTE]
> Basic パブリック IP では、Availability Zones はサポートされません。

1. ポータルの上部にある検索ボックスに、「**パブリック IP**」と入力します。

2. 検索結果から **[パブリック IP アドレス]** を選択します。

3. **[+ 作成]** を選択します。

4. **[パブリック IP アドレスの作成]** ページで、次の情報を入力または選択します。 

    | 設定                 | 値                       |
    | ---                     | ---                         |
    | IP バージョン              | **[IPv4]** を選択します                |    
    | SKU                     | **[Basic]** を選択します         |
    | 名前                    | 「**myBasicPublicIP**」と入力します。          |
    | IP アドレスの割り当て   | **[静的]** を選択します。            |
    | アイドル タイムアウト (分)  | 既定値の **[4]** のままにします。       |
    | DNS 名ラベル          | この値は空白のままにします。    |
    | サブスクリプション            | サブスクリプションを選択します。   |
    | Resource group          | **[新規作成]** を選択し、「**QuickStartCreateIP-rg**」と入力します。 </br> **[OK]** を選択します。 |
    | 場所                | **[(米国) 米国東部 2]** を選択します。      |

5. **［作成］** を選択します

:::image type="content" source="./media/create-public-ip-portal/create-basic-ip.png" alt-text="Azure portal の Basic IP アドレス作成のスクリーンショット" border="true":::

時間の経過と共に IP アドレスが変更されても問題ない場合は、AllocationMethod を **Dynamic** に変更することで **動的** IP の割り当てを選択できます。 

# <a name="routing-preference"></a>[**ルーティングの優先順位**](#tab/option-1-create-public-ip-routing-preference)

このセクションでは、パブリック IP アドレスに対して ISP ネットワーク ( **[インターネット]** オプション) を介した [ルーティングの優先順位](routing-preference-overview.md)を構成する方法について示します。 パブリック IP アドレスを作成すると、次の Azure リソースに関連付けることができます。

* 仮想マシン
* 仮想マシン スケール セット
* Azure Kubernetes Service (AKS)
* インターネットに接続するロード バランサー
* Application Gateway
* Azure Firewall

既定では、パブリック IP アドレスに対するルーティング優先設定は、すべての Azure サービスについて Microsoft グローバル ネットワークに設定され、任意の Azure サービスと関連付けることができます。

> [!NOTE]
>IPv6 アドレスを作成するには、 **[IP バージョン]** パラメーターに **[IPv6]** を選択します。 デプロイでデュアル スタック構成 (IPv4 と IPv6 アドレス) が必要な場合は、 **[両方]** を選択します。

## <a name="create-a-public-ip-with-internet-routing"></a>インターネット ルーティングを使用してパブリック IP アドレスを作成する

1. ポータルの上部にある検索ボックスに、「**パブリック IP**」と入力します。

2. 検索結果から **[パブリック IP アドレス]** を選択します。

3. **[+ 作成]** を選択します。

4. **[パブリック IP アドレスの作成]** で、次の情報を入力または選択します。

    | 設定                 | 値                       |
    | ---                     | ---                         |
    | IP バージョン              | [IPv4] を選択します。              |    
    | SKU                     | **[Standard]** を選択します。         |
    | レベル                   | **[地域]** を選択します。     |
    | 名前                    | 「**myStandardPublicIP-RP**」と入力します          |
    | IP アドレスの割り当て   | **[静的]** としてロックされます。                |
    | ルーティングの優先順位     | **[インターネット]** を選択します。 |
    | アイドル タイムアウト (分)  | 既定値の **[4]** のままにします。        |
    | DNS 名ラベル          | この値は空白のままにします。    |
    | サブスクリプション            | サブスクリプションを選択します。   |
    | Resource group          | **[新規作成]** を選択し、「**QuickStartCreateIP-rg**」と入力します。 </br> **[OK]** を選択します。 |
    | 場所                | **[(米国) 米国東部 2]** を選択します。     |
    | 可用性ゾーン       | **[ゾーン冗長]** を選択します |

5. **［作成］** を選択します

:::image type="content" source="./media/create-public-ip-portal/routing-preference.png" alt-text="Azure portal でルーティングの優先順位を構成するスクリーンショット" border="true":::

> [!NOTE]
> パブリック IP アドレスは、IPv4 または IPv6 アドレスを使用して作成されます。 ただし、現在、ルーティング優先設定では IPV4 のみがサポートされています。

> [!NOTE]
> [Availability Zones](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) があるリージョンでは、ゾーンなし (既定のオプション)、特定のゾーン、またはゾーン冗長を選択できます。 この選択は、特定のドメイン障害要件によって異なる場合があります。 Availability Zones がないリージョンでは、このフィールドは表示されません。 </br> 可用性ゾーンの詳細については、[可用性ゾーンの概要](../../availability-zones/az-overview.md)に関するページを参照してください。

上で作成したパブリック IP アドレスを、[Windows](../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Linux](../../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の仮想マシンに関連付けることができます。 チュートリアル ページ「[仮想マシンへのパブリック IP アドレスの関連付け](./associate-public-ip-address-vm.md#azure-cli)」の CLI セクションを使用して、パブリック IP アドレスを VM に関連付けます。 上で作成したパブリック IP アドレスを、ロード バランサーの **フロントエンド** 構成に割り当てることで、[Azure Load Balancer](../../load-balancer/load-balancer-overview.md) と関連付けることもできます。 このパブリック IP アドレスは、負荷分散された仮想 IP アドレス (VIP) として機能します。

# <a name="tier"></a>[**階層**](#tab/option-1-create-public-ip-tier)

パブリック IP アドレスは、1 つのリージョンに関連付けられています。 **グローバル** 階層では 1 つの IP アドレスが、複数のリージョンにまたがって使用されます。 **グローバル** 階層は、リージョン間ロード バランサーのフロントエンドに必要です。  

詳細については、「[リージョン間ロード バランサー](../../load-balancer/cross-region-overview.md)」を参照してください。

## <a name="create-a-global-tier-public-ip"></a>グローバル レベルのパブリック IP を作成する

1. ポータルの上部にある検索ボックスに、「**パブリック IP**」と入力します。

2. 検索結果から **[パブリック IP アドレス]** を選択します。

3. **[+ 作成]** を選択します。

4. **[パブリック IP アドレスの作成]** で、次の情報を入力または選択します。

    | 設定                 | 値                       |
    | ---                     | ---                         |
    | IP バージョン              | [IPv4] を選択します。              |    
    | SKU                     | **[Standard]** を選択します。         |
    | レベル                   | **[グローバル]** を選択します     |
    | 名前                    | 「**myStandardPublicIP-Global**」と入力します          |
    | IP アドレスの割り当て   | **[静的]** としてロックされます。                |
    | ルーティングの優先順位     | **[Microsoft]** を選択します。 |
    | アイドル タイムアウト (分)  | 既定値の **[4]** のままにします。        |
    | DNS 名ラベル          | この値は空白のままにします。    |
    | サブスクリプション            | サブスクリプションを選択します。   |
    | Resource group          | **[新規作成]** を選択し、「**QuickStartCreateIP-rg**」と入力します。 </br> **[OK]** を選択します。 |
    | 場所                | **[(米国) 米国東部 2]** を選択します。     |
    | 可用性ゾーン       | **[ゾーン冗長]** を選択します |

5. **［作成］** を選択します

:::image type="content" source="./media/create-public-ip-portal/tier.png" alt-text="Azure portal でレベルを構成するスクリーンショット" border="true":::

上記で作成した IP アドレスを、リージョン間のロード バランサーに関連付けることができます。 詳細については、「[チュートリアル:Azure portal を使用してリージョン間 Azure ロード バランサーを作成する](../../load-balancer/tutorial-cross-region-portal.md)」をご覧ください。

---

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションの使用を継続しない場合は、次の手順でパブリック IP アドレスを削除します。

1. ポータル上部の [検索] ボックスに「**リソース グループ**」と入力します。

2. 検索結果から **[リソース グループ]** を選択します。

3. **QuickStartCreateIP-rg** を選択します

4. **[リソース グループの削除]** を選択します。

5. **[TYPE THE RESOURCE GROUP NAME]\(リソース グループ名を入力してください\)** に「**myResourceGroup**」と入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

次の記事に進んで、パブリック IP プレフィックスの作成方法を学習してください。
> [!div class="nextstepaction"]
> [Azure portal を使用してパブリック IP プレフィックスを作成する](create-public-ip-prefix-portal.md)