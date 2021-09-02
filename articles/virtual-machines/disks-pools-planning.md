---
title: Azure ディスク プール (プレビュー) のパフォーマンスを最適化する
description: Azure ディスク プールのパフォーマンスを最大限に引き出す方法について説明します。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 74d50826811198811e6cea671641cae378d1235c
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2021
ms.locfileid: "122419587"
---
# <a name="azure-disk-pools-preview-planning-guide"></a>Azure ディスク プール (プレビュー) の計画ガイド

Azure ディスク プール (プレビュー) をデプロイする前に、ワークロードのパフォーマンス要件を理解しておくことが重要です。 事前に要件を決定することで、ディスク プールのパフォーマンスを最大限に引き出すことができます。 ディスク プールのパフォーマンスは、主に 3 つの要因によって決定されます。いずれかを調整して、ディスク プールのパフォーマンスを微調整します。

- ディスク プールのスケーラビリティ ターゲット
- ディスク プールに含まれる個々のディスクのスケーラビリティ ターゲット
- クライアント コンピューターとディスク プールとの間のネットワーク接続。

## <a name="optimize-for-low-latency"></a>待機時間を短くするために最適化する

待機時間の低減を優先するのであれば、ディスク プールに Ultra ディスクを追加します。 Ultra ディスクは、サブミリ秒のディスク待機時間を提供します。 可能な限り待機時間を低減するには、ネットワーク構成を評価し、最適なパスを使用していることを確認する必要もあります。 ネットワーク待機時間を最小限に抑えるには、 [ExpressRoute FastPath](../expressroute/about-fastpath.md) の使用を検討してください。

## <a name="optimize-for-high-throughput"></a>高スループットのための最適化

スループットを優先する場合は、まず、スループット ターゲットの配信に必要なディスク プールの数を評価します。 必要なターゲットが得られたら、各ディスクとその種類に分割できます。 現在、ディスク プール、Premium Ssd、および Ultra ディスクでは、2 つのディスクの種類を使用できます。 Premium Ssd は、記憶域容量に合わせてスケーリングする高 IOPS と MBps を提供できます。一方、Ultra ディスクは、記憶域容量に関係なく、パフォーマンスをスケーリングできます。 コストとパフォーマンスのバランスに最適な種類を選択します。 また、クライアントからディスク プールへのネットワーク接続がボトルネックになっていないことを確認します (特にスループット)。


## <a name="use-cases"></a>ユース ケース

次の表に、Azure VMware Solution でのディスク プールの一般的な使用例と推奨される構成を示します。


|Azure VMware Solution のユース ケース  |推奨されるディスクの種類  |推奨されるネットワーク構成  |
|---------|---------|---------|
|Azure VMware Solution vSAN の拡張機能など、アクティブなワーキング セットのブロック ストレージ。     |Ultra ディスク         |ExpressRoute 仮想ネットワーク ゲートウェイ: Ultra Performance または ErGw3AZ (10 Gbps) を使用して、ディスク プール仮想ネットワークを Azure VMware Solution クラウドに接続し、FastPath を有効にしてネットワーク待機時間を最小化します。         |
|階層化-頻繁にアクセスされないデータは、Azure VMware Solution vSAN からディスク プールに階層化されます。     |Premium SSD         |ExpressRoute virtual network gateway: Standard (1 Gbps) または ハイ パフォーマンス (2 Gbps) を使用して、ディスク プール仮想ネットワークを Azure VMware Solution クラウドに接続します。         |
|Azure VMware Solution 上のディザスター リカバリー サイトのデータ ストレージ: オンプレミスまたはプライマリ VMware 環境からセカンダリ サイトとしてディスク プールにデータをレプリケートします。     |Premium SSD         |ExpressRoute virtual network gateway: Standard (1 Gbps) または ハイ パフォーマンス (2 Gbps) を使用して、ディスク プール仮想ネットワークを Azure VMware Solution クラウドに接続します。         |

ネットワークのセットアップの計画については、[Azure VMware Solution のネットワーク計画チェックリスト](../azure-vmware/tutorial-network-checklist.md)を参照してください。またその他の Azure VMware Solution の考慮事項も確認してください。

## <a name="disk-pool-scalability-and-performance-targets"></a>ディスク プールのスケーラビリティとパフォーマンスのターゲット

|リソース  |制限  |
|---------|---------|
|ディスク プールあたりの最大ディスク数|8|
|ディスク プールあたりの最大 IOPS|25,600|
|ディスク プールあたりの最大 MBps|384|

次の例では、さまざまなパフォーマンス要因がどのように連携するかについて説明します。

たとえば、2 つの 1-TiB Premium Ssd (P30、プロビジョニングされたターゲットが 5000 IOPS、200 Mbps) をディスク プールに追加した場合、2 x 5000 = 10,000 IOPS を実現できます。 ただし、スループットはディスク プールによって 384 MBps に制限されます。 この 384 MBps という制限を超えるためには、さらに多くのディスク プールをデプロイしてスケールアウトし、スループットを向上できます。 ネットワーク スループットによって、スケールアウトの効果が制限されます。

## <a name="availability"></a>可用性

現在ディスク プールはプレビューであり、運用環境のワークロードには使用しないでください。

何らかの理由でディスク プールが Azure VMware Solution クラウドにアクセスできなくなった場合、次が発生します。

- ディスク プールに関連付けられているすべてのデータストアにアクセスできなくなります。
- 影響を受けたデータストアを使用しているこの Azure VMware Solution クラウドでホストされているすべての VMware VM は、不健全な状態になります。
- この Azure VMware Solution クラウド内のクラスターの正常性は、1 回の操作を除き、影響を受けません。ホストをメンテナンス モードにすることはできません。 Azure VMware Solution は、この障害を処理し、影響を受けたデータストアを切断することで復旧を試みます。

## <a name="next-steps"></a>次の手順

[ディスク プールをデプロイする](disks-pools-deploy.md)。
