---
title: Azure Firewall の強制トンネリング
description: 追加処理のためにインターネットへのトラフィックを別のファイアウォールまたはネットワーク仮想アプライアンスにルーティングするように、強制トンネリングを構成することができます。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 05/18/2020
ms.author: victorh
ms.openlocfilehash: cf0af93d95c5af56be6168bc8e4f79d3005e2ec2
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649597"
---
# <a name="azure-firewall-forced-tunneling"></a>Azure Firewall の強制トンネリング

新しい Azure ファイアウォールを構成するときに、インターネットへのすべてのトラフィックを、インターネットに直接送信するのではなく、指定された次ホップにルーティングすることができます。 たとえば、インターネットに渡す前にネットワーク トラフィックを処理するために、オンプレミスのエッジ ファイアウォールや他のネットワーク仮想アプライアンス (NVA) があるような場合です。 ただし、既存のファイアウォールを強制トンネリング用に構成することはできません。

既定では、すべての送信 Azure 依存関係が満たされるようにするため、Azure Firewall では強制トンネリングは許可されていません。 既定でインターネットに直接ルーティングされないユーザー定義ルート (UDR) を *AzureFirewallSubnet* で構成することはできません。

## <a name="forced-tunneling-configuration"></a>強制トンネリング構成

強制トンネリングをサポートするため、サービス管理のトラフィックは顧客のトラフィックから分離されます。 *AzureFirewallManagementSubnet* (最小サブネット サイズ /26) という名前の追加の専用サブネットと、それ自体に関連付けられているパブリック IP アドレスが必要です。 このサブネットで許可される唯一のルートはインターネットへの既定のルートであり、BGP ルート伝達は無効になっている必要があります。

オンプレミスへのトラフィックを強制するために BGP 経由でアドバタイズされる既定のルートがある場合は、ファイアウォールをデプロイする前に *AzureFirewallSubnet* と *AzureFirewallManagementSubnet* を作成し、インターネットへの既定のルートを持つ UDR を作成して、**仮想ネットワーク ゲートウェイのルート伝達**を無効にする必要があります。

この構成では、インターネットに渡される前にトラフィックを処理するため、オンプレミスのファイアウォールまたは NVA へのルートを *AzureFirewallSubnet* に含めることができます。 また、このサブネットで**仮想ネットワーク ゲートウェイのルート伝達**が有効になっている場合は、BGP を使用してこれらのルートを *AzureFirewallSubnet* に公開することもできます。

たとえば、お使いのオンプレミスのデバイスに到達するための次ホップとして VPN ゲートウェイを使用して、*AzureFirewallSubnet* に既定のルートを作成できます。 または、**仮想ネットワーク ゲートウェイのルート伝達**を有効にして、オンプレミス ネットワークへの適切なルートを取得することもできます。

![仮想ネットワーク ゲートウェイのルート伝達](media/forced-tunneling/route-propagation.png)

強制トンネリングをサポートするように Azure Firewall を構成した後は、構成を元に戻すことはできません。 ファイアウォールで他のすべての IP 構成を削除すると、管理 IP 構成も削除され、ファイアウォールの割り当てが解除されます。 管理 IP 構成に割り当てられているパブリック IP アドレスを削除することはできませんが、異なるパブリック IP アドレスを割り当てることはできます。

## <a name="next-steps"></a>次のステップ

- [チュートリアル:Azure portal を使用してハイブリッド ネットワークに Azure Firewall をデプロイして構成する](tutorial-hybrid-portal.md)
