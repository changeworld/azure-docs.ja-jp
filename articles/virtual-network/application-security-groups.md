---
title: Azure アプリケーション セキュリティ グループの概要
titlesuffix: Azure Virtual Network
description: アプリケーション セキュリティ グループの使用方法について説明します。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 775ef92a0ca486d1f8a6c44c78a4df04cd5ef467
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274547"
---
# <a name="application-security-groups"></a>アプリケーション セキュリティ グループ

アプリケーション セキュリティ グループを使用すると、ネットワーク セキュリティをアプリケーションの構造の自然な拡張として構成でき、仮想マシンをグループ化して、それらのグループに基づくネットワーク セキュリティ ポリシーを定義できます。 明示的な IP アドレスを手動でメンテナンスせずに、大きなセキュリティ ポリシーを再利用することができます。 プラットフォームが明示的な IP アドレスと複数の規則セットの複雑さを処理するので、ユーザーはビジネス ロジックに専念することができます。 アプリケーション セキュリティ グループをよりよく理解するために、次の例について考えてください。

![アプリケーション セキュリティ グループ](./media/security-groups/application-security-groups.png)

前の図では、*NIC1* と *NIC2* が *AsgWeb* アプリケーション セキュリティ グループのメンバーです。 *NIC3* は、*AsgLogic* アプリケーション セキュリティ グループのメンバーです。 *NIC4* は、*AsgDb* アプリケーション セキュリティ グループのメンバーです。 この例の各ネットワーク インターフェイスは 1 つのアプリケーション セキュリティ グループだけのメンバーですが、ネットワーク インターフェイスは複数のアプリケーション セキュリティ グループのメンバーにすることができます (最大数については、[Azure の制限](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)を参照してください)。 ネットワーク セキュリティ グループが関連付けられているネットワーク インターフェイスはありません。 *NSG1* は両方のサブネットに関連付けられており、次の規則を含んでいます。

## <a name="allow-http-inbound-internet"></a>Allow-HTTP-Inbound-Internet

この規則は、インターネットから Web サーバーへのトラフィックを許可するために必要です。 インターネットからの受信トラフィックは **DenyAllInbound** 既定セキュリティ規則によって拒否されるため、*AsgLogic* または *AsgDb* アプリケーション セキュリティ グループでは追加の規則は必要ありません。

|Priority|source|ソース ポート| 宛先 | 宛先ポート | Protocol | アクセス |
|---|---|---|---|---|---|---|
| 100 | インターネット | * | AsgWeb | 80 | TCP | Allow |

## <a name="deny-database-all"></a>Deny-Database-All

**AllowVNetInBound** 既定セキュリティ規則では、同じ仮想ネットワーク上にあるリソース間の通信がすべて許可されるため、この規則はすべてのリソースからのトラフィックを拒否するために必要です。

|Priority|source|ソース ポート| 宛先 | 宛先ポート | Protocol | アクセス |
|---|---|---|---|---|---|---|
| 120 | * | * | AsgDb | 1433 | Any | 拒否 |

## <a name="allow-database-businesslogic"></a>Allow-Database-BusinessLogic

この規則は、*AsgLogic* アプリケーション セキュリティ グループから *AsgDb* アプリケーション セキュリティ グループへのトラフィックを許可します。 この規則の優先度は、*Deny-Database-All* 規則の優先度よりも高くなっています。 その結果、この規則は *Deny-Database-All* 規則の前に処理されるため、*AsgLogic* アプリケーション セキュリティ グループからのトラフィックは許可されますが、他のすべてのトラフィックはブロックされます。

|Priority|source|ソース ポート| 宛先 | 宛先ポート | Protocol | アクセス |
|---|---|---|---|---|---|---|
| 110 | AsgLogic | * | AsgDb | 1433 | TCP | Allow |

アプリケーション セキュリティ グループを送信元または送信先として指定されている規則は、アプリケーション セキュリティ グループのメンバーであるネットワーク インターフェイスにのみ適用されます。 ネットワーク インターフェイスがアプリケーション セキュリティ グループのメンバーでない場合、ネットワーク セキュリティ グループがサブネットに関連付けられていても、規則はネットワーク インターフェイスに適用されません。

アプリケーション セキュリティ グループには、次の制約があります。

-    アプリケーション セキュリティ グループに関しては他にもいくつかの制限がありますが、サブスクリプションに含めることができるアプリケーション セキュリティ グループの数にも制限があります。 詳細については、[Azure の制限](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) に関する記事をご覧ください。
- セキュリティ規則のソースおよび宛先として、1 つのアプリケーション セキュリティ グループを指定できます。 送信元と送信先に複数のアプリケーション セキュリティ グループを指定することはできません。
- アプリケーション セキュリティ グループに最初に割り当てられたネットワーク インターフェイスが存在する仮想ネットワークに、そのアプリケーション セキュリティ グループに割り当てられたすべてのネットワーク インターフェイスが存在する必要があります。 たとえば、*AsgWeb* という名前のアプリケーション セキュリティ グループに最初に割り当てられたネットワーク インターフェイスが *VNet1* という名前の仮想ネットワークにある場合、*AsgWeb* に以降に割り当てられるすべてのネットワーク インターフェイスが *VNet1* に存在する必要があります。 異なる仮想ネットワークからのネットワーク インターフェイスを同じアプリケーション セキュリティ グループに追加することはできません。
- セキュリティ規則のソースおよび宛先としてアプリケーション セキュリティ グループを指定する場合、両方のアプリケーション セキュリティ グループのネットワーク インターフェイスが、同じ仮想ネットワークに存在している必要があります。 たとえば、*VNet1* のネットワーク インターフェイスが *AsgLogic*、*VNet2* のネットワーク インターフェイスが *AsgDb* に存在する場合、規則の送信元として *AsgLogic*、送信先として *AsgDb* を割り当てることはできません。 送信元と送信先の両方のアプリケーション セキュリティ グループ内のすべてのネットワーク インターフェイスは、同じ仮想ネットワークに存在している必要があります。

> [!TIP]
> 必要なセキュリティ規則の数と、規則を変更する必要性を最小限に抑えるには、必要なアプリケーション セキュリティ グループを綿密に計画し、できる限り個々の IP アドレスまたは IP アドレスの範囲ではなく、サービス タグまたはアプリケーション セキュリティ グループを使用して規則を作成します。

## <a name="next-steps"></a>次のステップ

* [ネットワーク セキュリティ グループの作成](tutorial-filter-network-traffic.md)方法を学習します。
