---
title: Azure Gateway Load Balancer (プレビュー)
titleSuffix: Azure Load Balancer
description: Azure Load Balancer 用ゲートウェイ ロード バランサー SKU の概要。
ms.service: load-balancer
author: asudbring
ms.author: allensu
ms.date: 11/02/2021
ms.topic: conceptual
ms.custom: ignite-fall-2021
ms.openlocfilehash: 22548d5bb75bb70c20296ddd68fde5d232213403
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092513"
---
# <a name="gateway-load-balancer-preview"></a>Azure Gateway Load Balancer (プレビュー)

Azure Gateway Load Balancer は、サードパーティのネットワーク仮想アプライアンス (NVA) を使用するハイ パフォーマンスと高可用性のシナリオに対応する、Azure Load Balancer ポートフォリオの SKU です。 Azure Gateway Load Balancer 機能を使用すると、NVA を簡単にデプロイ、スケーリング、管理できます。 パブリック エンドポイントに対して Azure Gateway Load Balancer をチェーンするには、1 回のクリックしか必要ありません。 

アプライアンスは、次のようなさまざまな種類のシナリオに対して透過的に挿入できます。

* ファイアウォール
* 高度なパケット分析
* 侵入の検出と防止システム
* トラフィックのミラーリング
* インライン DDoS
* カスタム アプライアンス

Azure Gateway Load Balancer を使用すると、管理上のオーバーヘッドが増えずに、高度なネットワーク機能を簡単に追加または削除できます。 これにより、パブリック エンドポイントへのすべてのトラフィックがアプリケーションの前にアプライアンスに最初に送信されるのを確認するために必要な、ネットワーク内の Bump-in-the-wire テクノロジが提供されます。 NVA を使用するシナリオでは、フローが対称的である点が特に重要です。 Azure Gateway Load Balancer は、バックエンド プール内の特定のインスタンスへのフローの保持性と、フローの対称性を維持します。 その結果、手動で構成することなく、追加ネットワーク仮想アプライアンスへの一貫したルートが保証されます。 その結果、パケットは両方向に同じネットワーク パスを通過し、このキー機能を必要とするアプライアンスはシームレスに機能できます。

正常性プローブは、すべてのポートをリッスンし、HA ポート規則を使用してバックエンド インスタンスにトラフィックをルーティングします。 Azure Gateway Load Balancer との間で送信されるトラフィックは、VXLAN プロトコルを使用します。 

> [!IMPORTANT]
> Azure Gateway Load Balancer は、現在プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="benefits"></a>メリット

Azure Gateway Load Balancer には、次の利点があります。

* 仮想アプライアンスをネットワーク パスに透過的に統合します。

* ネットワーク パス内のネットワーク仮想アプライアンスを簡単に追加または削除できます。 

* コストを管理しながら簡単にスケーリングできます。

* ネットワーク仮想アプライアンスの可用性を向上させます。

* リージョンとサブスクリプション間でアプリケーションをチェーンする

Standard Public Load Balancer または仮想マシンの IP 構成は、Azure Gateway Load Balancer にチェーンできます。 Azure Gateway Load Balancer が仮想マシン上の Standard Public Load Balancer フロントエンドまたは IP 構成にチェーンされた後、アプリケーション エンドポイント間のトラフィックが Azure Gateway Load Balancer に送信されるのを確認するために、追加の構成は必要はありません。

トラフィックは、コンシューマー仮想ネットワークからプロバイダー仮想ネットワークに移動します。 その後、トラフィックはコンシューマー仮想ネットワークに返されます。 コンシューマー仮想ネットワークとプロバイダー仮想ネットワークは、異なるサブスクリプション、テナント、またはリージョンに含めることができ、管理オーバーヘッドが低減されます。

:::image type="content" source="./media/gateway-overview/gateway-load-balancer-diagram.png" alt-text="Azure Gateway Load Balancer の図":::

*図: Azure Gateway Load Balancer の図。*

## <a name="components"></a>Components

Azure Gateway Load Balancer は、次のコンポーネントで構成されます。

* **フロントエンド IP 構成** - Azure Gateway Load Balancer の IP アドレス。 この IP はプライベートのみです。 

* **負荷分散規則** - ロード バランサーの規則は、バックエンド プール内のすべてのインスタンスに受信トラフィックを分散させる方法を定義するために使用されます。 負荷分散規則により、フロントエンドの特定の IP 構成およびポートがバックエンドの複数の IP アドレスおよびポートにマップされます。 

    * Azure Gateway Load Balancer 規則は、HA ポート規則のみを指定できます。 

    * Azure Gateway Load Balancer 規則は、最大 2 つのバックエンド プールに関連付けできます。 

* **バックエンド プール** - 受信要求を処理する仮想マシンのグループまたは仮想マシン スケール セット内のインスタンスのグループ。 コスト効果に優れた方法でスケーリングして大量の受信トラフィックに対処するために、コンピューティングのガイドラインでは通常、バックエンド プールにインスタンスを追加することが推奨されます。 Load Balancer は、インスタンスがスケールアップまたはスケールダウンされると、自動再構成を通じてすぐに自身を再構成します。 バックエンド プールの VM を追加または削除すると、追加操作なしに、ロード バランサーが再構成されます。 バックエンド プールのスコープは、1 つの仮想ネットワーク内の任意の仮想マシンです。 

* **Tunnel インターフェイス** - Azure Gateway Load Balancer バックエンド プールには、Tunnel インターフェイスと呼ばれる別のコンポーネントがあります。 Tunnel インターフェイスを使用すると、バックエンド内のアプライアンスで、ネットワーク フローが想定した方法で確実に処理されます。 各バックエンド プールには、最大 2 つの Tunnel インターフェイスを使用できます。 Tunnel インターフェイスは、内部または外部のいずれかにできます。 バックエンド プールに送信されるトラフィックの場合は、外部の種類を使用する必要があります。 アプライアンスからアプリケーションに送信されるトラフィックの場合は、内部の種類を使用する必要があります。

* **チェーン** - Azure Gateway Load Balancer は、Standard Public Load Balancer フロントエンドまたは仮想マシン上のパブリック IP 構成によって参照できます。 特定のシーケンスでの高度なネットワーク機能の追加は、サービス チェーンと呼ばれます。 その結果、この参照はチェーンと呼ばれます。

## <a name="pricing"></a>価格

プレビュー期間中は、Azure Gateway Load Balancer の料金は発生しません。 

一般公開リリース中に有効になる価格については、[Azure Load Balancer の価格](https://azure.microsoft.com/pricing/details/load-balancer/)に関する記事を参照してください。

## <a name="limitations"></a>制限事項

* Azure Gateway Load Balancer は、Global Load Balancer レベルでは機能しません。
* Azure Gateway Load Balancer ポータルのサポートは、現在、中国のクラウドおよび政府機関のクラウド リージョンでは利用できません。 CLI、PowerShell、テンプレート、または代替手段を使用できます。
* テナント間のチェーンは、Azure portal を介してはサポートされません。

## <a name="next-steps"></a>次のステップ

- ゲートウェイ ロード バランサーを作成するには、「[Azure portal を使用してゲートウェイ ロード バランサーを作成する](tutorial-gateway-portal.md)」を参照してください。
- [Azure Load Balancer](load-balancer-overview.md) についてさらに詳しく学習する。
