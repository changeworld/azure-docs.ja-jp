---
title: Azure ディスク プール (プレビュー) のパフォーマンスを最適化する
description: Azure ディスク プールのパフォーマンスを最大限に引き出す方法について説明します。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: ignite-fall-2021
ms.openlocfilehash: 801930dc58993a81c29969f602da64f201919bd0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131428009"
---
# <a name="azure-disk-pools-preview-planning-guide"></a>Azure ディスク プール (プレビュー) の計画ガイド

Azure ディスク プール (プレビュー) をデプロイする前に、ワークロードのパフォーマンス要件を理解しておくことが重要です。 事前に要件を決定することで、ディスク プールのパフォーマンスを最大限に引き出すことができます。 ディスク プールのパフォーマンスは、主に 3 つの要因によって決定されます。いずれかを調整して、ディスク プールのパフォーマンスを微調整します。

- ディスク プールのスケーラビリティ ターゲット
- ディスク プールに含まれる個々のディスクのスケーラビリティ ターゲット
- クライアント コンピューターとディスク プールとの間のネットワーク接続。

## <a name="optimize-for-low-latency"></a>待機時間を短くするために最適化する

待機時間の低減を優先するのであれば、ディスク プールに Ultra ディスクを追加します。 Ultra ディスクは、サブミリ秒のディスク待機時間を提供します。 可能な限り待機時間を低減するには、ネットワーク構成を評価し、最適なパスを使用していることを確認する必要もあります。 ネットワーク待機時間を最小限に抑えるには、 [ExpressRoute FastPath](../expressroute/about-fastpath.md) の使用を検討してください。

## <a name="optimize-for-high-throughput"></a>高スループットのための最適化

スループットを優先する場合は、まず、さまざまなディスク プール SKU のパフォーマンス ターゲットと、スループット ターゲットの配信に必要なディスク プールの数を評価します。 必要なパフォーマンスが Premium ディスク プールで提供できるものを超える場合は、複数のディスク プールにデプロイを分割できます。 その後、ディスク プールの個々のディスクとその種類で提供されるパフォーマンスを最大限に活用する方法を決定できます。 ディスク プールの場合は、Premium と Standard の SSD を組み合わせるか、Ultra Disk のみを使用することができます。 Ultra Disk を Premium または Standard の SSD と共に使用することはできません。 ニーズに最適なディスクの種類を選びます。 また、クライアントからディスク プールへのネットワーク接続がボトルネックになっていないことを確認します (特にスループット)。


## <a name="use-cases"></a>ユース ケース

次の表に、Azure VMware Solution でのディスク プールの一般的な使用例と推奨される構成を示します。


|Azure VMware Solution のユース ケース  |推奨されるディスクの種類  |推奨されるディスク プール SKU  |推奨されるネットワーク構成  |
|---------|---------|---------|---------|
|Azure VMware Solution vSAN の拡張機能など、アクティブなワーキング セットのブロック ストレージ。     |Ultra ディスク         |Premium         |ExpressRoute 仮想ネットワーク ゲートウェイ: Ultra Performance または ErGw3AZ (10 Gbps) を使用して、ディスク プール仮想ネットワークを Azure VMware Solution クラウドに接続し、FastPath を有効にしてネットワーク待機時間を最小化します。         |
|階層化-頻繁にアクセスされないデータは、Azure VMware Solution vSAN からディスク プールに階層化されます。     |Premium SSD、Standard SSD         |Standard         |ExpressRoute virtual network gateway: Standard (1 Gbps) または ハイ パフォーマンス (2 Gbps) を使用して、ディスク プール仮想ネットワークを Azure VMware Solution クラウドに接続します。         |
|Azure VMware Solution 上のディザスター リカバリー サイトのデータ ストレージ: オンプレミスまたはプライマリ VMware 環境からセカンダリ サイトとしてディスク プールにデータをレプリケートします。     |Premium SSD、Standard SSD         |Standard、Basic         |ExpressRoute virtual network gateway: Standard (1 Gbps) または ハイ パフォーマンス (2 Gbps) を使用して、ディスク プール仮想ネットワークを Azure VMware Solution クラウドに接続します。         |


ネットワークのセットアップの計画については、[Azure VMware Solution のネットワーク計画チェックリスト](../azure-vmware/tutorial-network-checklist.md)を参照してください。またその他の Azure VMware Solution の考慮事項も確認してください。

## <a name="disk-pool-scalability-and-performance-targets"></a>ディスク プールのスケーラビリティとパフォーマンスのターゲット

|リソース  |Basic ディスク プール  |Standard ディスク プール  |Premium ディスク プール  |
|---------|---------|---------|---------|
|ディスク プールあたりの最大ディスク数     |16         |32         |32         |
|ディスク プールあたりの最大 IOPS     |12,800         |25,600         |51,200         |
|ディスク プールあたりの最大 MBps     |192         |384         |768         |

次の例では、さまざまなパフォーマンス要因がどのように連携するかについて説明します。

たとえば、2 つの 1 TiB Premium SSD (P30、プロビジョニングされたターゲットが 5000 IOPS、200 Mbps) を Standard ディスク プールに追加した場合、2 x 5000 = 10,000 IOPS を実現できます。 ただし、スループットはディスク プールによって 384 MBps に制限されます。 この 384 MBps という制限を超えるためには、さらに多くのディスク プールをデプロイしてスケールアウトし、スループットを向上できます。 ネットワーク スループットによって、スケールアウトの効果が制限されます。

REST API で SKU を指定せずに作成されたディスク プールは、既定では標準のディスク プールになります。

## <a name="availability"></a>可用性

現在ディスク プールはプレビューであり、運用環境のワークロードには使用しないでください。 既定状態のディスク プールでは、Premium および Standard の SSD のみがサポートされます。 代わりに、ディスク プールで Ultra Disk のサポートを有効にできますが、Ultra Disk を使うディスク プールには Premium または Standard の SSD との互換性がありません。

Premium および Standard の SSD をサポートするディスク プールは、iSCSI エンドポイントをホストする複数のものを使用する高可用性アーキテクチャに基づいています。 Ultra Disk をサポートするディスク プールは、1 つのインスタンス デプロイでホストされます。

何らかの理由でディスク プールが Azure VMware Solution クラウドにアクセスできなくなった場合、次が発生します。

- ディスク プールに関連付けられているすべてのデータストアにアクセスできなくなります。
- 影響を受けたデータストアを使っている Azure VMware Solution クラウドでホストされているすべての VMware VM は、不健全な状態になります。
- Azure VMware Solution クラウド内のクラスターの正常性は、1 回の操作を除き、影響を受けません。ホストをメンテナンス モードにすることはできません。 Azure VMware Solution は、この障害を処理し、影響を受けたデータストアを切断することで復旧を試みます。

## <a name="next-steps"></a>次の手順

- [ディスク プールをデプロイする](disks-pools-deploy.md)。
- Azure VMware Solution によるディスク プールの統合方法については、「[ディスク プールを Azure VMware Solution ホストにアタッチする (プレビュー)](../azure-vmware/attach-disk-pools-to-azure-vmware-solution-hosts.md)」をご覧ください。
