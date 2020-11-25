---
title: パブリック IP の作成 - Azure portal
description: Azure portal でパブリック IP を作成する方法について説明します。
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: add763b713b93604e089d7aec586876fecd2887c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "95895640"
---
# <a name="quickstart-create-a-public-ip-address-using-the-azure-portal"></a>クイック スタート:Azure portal を使用してパブリック IP アドレスを作成する

この記事では、Azure portal を使用してパブリック IP アドレス リソースを作成する方法について説明します。 これを関連付けることができるリソース、Basic SKU と Standard SKU の違い、およびその他の関連情報については、「[パブリック IP アドレス](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses)」を参照してください。  この例では、IPv4 アドレスのみに焦点を当てます。IPv6 アドレスの詳細については、[Azure VNet の IPv6](https://docs.microsoft.com/azure/virtual-network/ipv6-overview) に関するページを参照してください。

# <a name="standard-sku---using-zones"></a>[**Standard SKU - ゾーンの使用**](#tab/option-create-public-ip-standard-zones)

以下の手順では、**myStandardZRPublicIP** という名前の Standard ゾーン冗長パブリック IP アドレスを作成します。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. **[リソースの作成]** を選択します。 
3. 検索ボックスに「*パブリック IP アドレス*」と入力します。
4. 検索結果から **[パブリック IP アドレス]** を選択します。 次に、 **[パブリック IP アドレス]** ページで、 **[作成]** を選択します。
5. **[パブリック IP アドレスの作成]** ページで、次の情報を入力または選択します。 

    | 設定                 | 値                       |
    | ---                     | ---                         |
    | IP バージョン              | [IPv4] を選択します。                 |    
    | SKU                     | **[Standard]** を選択します。         |
    | 名前                    | 「*myStandardZRPublicIP*」と入力します。          |
    | IP アドレスの割り当て   | これは "静的" としてロックされることに注意してください。                                        |
    | アイドル タイムアウト (分)  | 値は 4 のままにします。        |
    | DNS 名ラベル          | 値は空白のままにします。    |
    | サブスクリプション            | サブスクリプションを選択します。   |
    | Resource group          | **[新規作成]** を選択し、「myResourceGroup」と入力して、 **[OK]** を選択します。 |
    | 場所                | **[米国東部 2]** を選択します      |
    | 可用性ゾーン       | **[ゾーン冗長]** を選択するか、特定のゾーンを選択します (下記の注を参照)。 |

これらは、[Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones) が利用できるリージョンでのみ有効な選択であることに注意してください。  (これらのリージョンでは特定のゾーンも選択できますが、ゾーン障害に対する回復性はありません。)

# <a name="basic-sku"></a>[**Basic SKU**](#tab/option-create-public-ip-basic)

次の手順を使用して、**myBasicPublicIP** という名前の Basic 静的パブリック IP アドレスを作成します。  Basic パブリック IP には、可用性ゾーンという概念はありません。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. **[リソースの作成]** を選択します。 
3. 検索ボックスに「*パブリック IP アドレス*」と入力します。
4. 検索結果から **[パブリック IP アドレス]** を選択します。 次に、 **[パブリック IP アドレス]** ページで、 **[作成]** を選択します。
5. **[パブリック IP アドレスの作成]** ページで、次の情報を入力または選択します。 

    | 設定                 | 値                       |
    | ---                     | ---                         |
    | IP バージョン              | [IPv4] を選択します。                 |    
    | SKU                     | **[Standard]** を選択します。         |
    | 名前                    | 「*myBasicPublicIP*」と入力します。          |
    | IP アドレスの割り当て   | **[静的]** を選択します (下記の注を参照)                                     |
    | アイドル タイムアウト (分)  | 値は 4 のままにします。        |
    | DNS 名ラベル          | 値は空白のままにします。    |
    | サブスクリプション            | サブスクリプションを選択します。   |
    | Resource group          | **[新規作成]** を選択し、「myResourceGroup」と入力して、 **[OK]** を選択します。 |
    | 場所                | **[米国東部 2]** を選択します      |

時間の経過と共に IP アドレスが変更されても問題ない場合は、**動的** IP の割り当てを選択できます。

---

## <a name="additional-information"></a>関連情報 

上に示した個々のフィールドの詳細については、「[パブリック IP アドレスの管理](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#create-a-public-ip-address)」を参照してください。

## <a name="next-steps"></a>次のステップ
- [仮想マシンにパブリック IP アドレス](https://docs.microsoft.com/azure/virtual-network/associate-public-ip-address-vm#azure-portal)を関連付ける
- Azure の[パブリック IP アドレス](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)について詳しく学習する。
- すべての[パブリック IP アドレスの設定](virtual-network-public-ip-address.md#create-a-public-ip-address)について詳しく学習する。