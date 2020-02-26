---
title: Azure Firewall の強制トンネリング
description: 追加処理のためにインターネットへのトラフィックを別のファイアウォールまたはネットワーク仮想アプライアンスにルーティングするように、強制トンネリングを構成することができます。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 4093f91e55272a32ce7df4a78e2ee8b3ebed5fde
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444836"
---
# <a name="azure-firewall-forced-tunneling-preview"></a>Azure Firewall の強制トンネリング (プレビュー)

インターネットへのすべてのトラフィックを、インターネットに直接送信するのではなく、指定された次ホップにルーティングするように、Azure Firewall を構成することができます。 たとえば、インターネットに渡す前にネットワーク トラフィックを処理するために、オンプレミスのエッジ ファイアウォールや他のネットワーク仮想アプライアンス (NVA) があるような場合です。

> [!IMPORTANT]
> 現在、Azure Firewall の強制トンネリングはパブリック プレビュー段階にあります。
>
> このパブリック プレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することは避けてください。 特定の機能はサポート対象ではなく、機能が制限されることがあるか、Azure の場所によっては利用できない場合があります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

既定では、すべての送信 Azure 依存関係が満たされるようにするため、Azure Firewall では強制トンネリングは許可されていません。 既定でインターネットに直接ルーティングされないユーザー定義ルート (UDR) を *AzureFirewallSubnet* で構成することはできません。

## <a name="forced-tunneling-configuration"></a>強制トンネリング構成

強制トンネリングをサポートするため、サービス管理のトラフィックは顧客のトラフィックから分離されます。 *AzureFirewallManagementSubnet* という名前の追加の専用サブネットと、それ自体に関連付けられているパブリック IP アドレスが必要です。 このサブネットで許可される唯一のルートはインターネットへの既定のルートであり、BGP ルート伝達は無効になっている必要があります。

オンプレミスへのトラフィックを強制するために BGP 経由でアドバタイズされる既定のルートがある場合は、ファイアウォールを展開する前に *AzureFirewallSubnet* と *AzureFirewallManagementSubnet* を作成し、インターネットへの既定のルートを持つ UDR を作成して、仮想ネットワーク ゲートウェイのルート伝達を無効にする必要があります。

この構成では、インターネットに渡される前にトラフィックを処理するため、オンプレミスのファイアウォールまたは NVA へのルートを *AzureFirewallSubnet* に含めることができます。 また、このサブネットで仮想ネットワーク ゲートウェイのルート伝達が有効になっている場合は、BGP を使用してこれらのルートを *AzureFirewallSubnet* に公開することもできます。

強制トンネリングをサポートするように Azure Firewall を構成した後は、構成を元に戻すことはできません。 ファイアウォールで他のすべての IP 構成を削除すると、管理 IP 構成も削除され、ファイアウォールの割り当てが解除されます。 管理 IP 構成に割り当てられているパブリック IP アドレスを削除することはできませんが、異なるパブリック IP アドレスを割り当てることはできます。

## <a name="next-steps"></a>次のステップ

- [チュートリアル:Azure portal を使用してハイブリッド ネットワークに Azure Firewall をデプロイして構成する](tutorial-hybrid-portal.md)