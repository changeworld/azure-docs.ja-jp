---
title: Azure Cache for Redis の高可用性
description: Azure Cache for Redis の高可用性の機能とオプションについて学習する
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: yegu
ms.openlocfilehash: f0bb8fd2d0b0ac271a167ad5474a55646bdafc65
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/26/2020
ms.locfileid: "92536795"
---
# <a name="high-availability-for-azure-cache-for-redis"></a>Azure Cache for Redis の高可用性

Azure Cache for Redis には高可用性が組み込まれています。 高可用性アーキテクチャの目標は、基になる仮想マシン (VM) が計画的または計画外の停止の影響を受ける場合でも、マネージド Redis インスタンスが確実に機能するようにすることです。 これにより、単一の VM で Redis をホストすることで達成できるものよりもはるかに高い比率が実現されます。

Azure Cache for Redis により、キャッシュ用に " *ノード* " と呼ばれる複数の VM が使用され、高可用性が実装されます。 これらのノードは、状況に応じてデータのレプリケーションとフェールオーバーが行われるように構成されます。 また、Redis ソフトウェア修正プログラムの適用などのメンテナンス操作が調整されます。 Standard および Premium レベルで、さまざまな高可用性オプションを使用することができます。

| オプション | 説明 | 可用性 | Standard | Premium |
| ------------------- | ------- | ------- | :------: | :---: |
| [標準のレプリケーション](#standard-replication)| 単一のデータセンターまたは可用性ゾーン (AZ) でのデュアルノードのレプリケートされた構成 (自動フェールオーバーあり) | 99.9% |✔|✔|
| [複数のレプリカ](#multiple-replicas) | 1 つまたは複数の AZ でのマルチノードのレプリケートされた構成 (自動フェールオーバーあり) | 99.95% (ゾーン冗長性あり) |-|✔|
| [ゾーン冗長性](#zone-redundancy) | AZ 間でのマルチノードのレプリケートされた構成 (自動フェールオーバーあり) | 99.95% (複数のレプリカあり) |-|✔|
| [geo レプリケーション](#geo-replication) | 2 つのリージョンでのリンクされたキャッシュ インスタンス (ユーザー制御のフェールオーバーあり) | 99.9% (単一リージョンの場合) |-|✔|

## <a name="standard-replication"></a>標準のレプリケーション

Standard または Premium レベルの Azure Cache for Redis は、既定では 1 組の Redis サーバーで実行されます。 2 台のサーバーは専用の VM 上でホストされます。 オープンソースの Redis を使用すれば、1 台のサーバーだけでデータ書き込み要求を処理できます。 このサーバーは " *プライマリ* " ノードで、もう一方は " *レプリカ* " です。 サーバー ノードのプロビジョニング後に、Azure Cache for Redis によってプライマリおよびレプリカ ロールが割り当てられます。 通常、プライマリ ノードは、Redis クライアントからの読み取りだけでなく書き込み要求の処理も担当します。 書き込み操作で、新しいキーとキーの更新をその内部メモリにコミットし、クライアントにすぐに応答します。 操作をレプリカに非同期的に転送します。

:::image type="content" source="media/cache-high-availability/replication.png" alt-text="データ レプリケーションの設定&quot;:::
   
>[!NOTE]
>通常、Redis クライアントは、すべての読み取りおよび書き込み要求について、Redis Cache 内のプライマリ ノードと通信を行います。 レプリカ ノードから読み取るように、特定の Redis クライアントを構成することができます。
>
>

Redis Cache 内のプライマリ ノードが使用できない場合、レプリカは自動的に新しいプライマリになるように自身を昇格させます。 このプロセスは &quot; *フェールオーバー* " と呼ばれます。 レプリカは十分に長い時間待機してから引き継ぎます。その場合、プライマリ ノードは迅速に回復します。 フェールオーバーが発生すると、Azure Cache for Redis によって新しい VM がプロビジョニングされ、それがレプリカ ノードとしてキャッシュに参加させられます。 レプリカによって、キャッシュ データの別のコピーを保持するために、プライマリとの完全なデータ同期が行われます。

プライマリ ノードは、Redis ソフトウェアやオペレーティング システムの更新などの計画されたメンテナンス アクティビティの一部としてサービスを認識しないことがあります。 また、基になるハードウェア、ソフトウェア、またはネットワークでの障害など、計画外のイベントが原因で動作を停止することもあります。 Redis フェールオーバーの種類については、「[Azure Cache for Redis のフェールオーバーと修正プログラムの適用](cache-failover.md)」に詳しく説明されています。 Azure Cache for Redis により、その有効期間中に多くのフェールオーバーが確認されます。 高可用性アーキテクチャは、キャッシュ内でのこれらの変更を、そのクライアントに対して可能な限り透過的に行うように設計されています。

## <a name="multiple-replicas"></a>複数のレプリカ

>[!NOTE]
>これはプレビューとして利用できます。
>
>

Azure Cache for Redis によって、Premium レベルでの追加のレプリカ ノードが許可されます。 [複数レプリカのキャッシュ](cache-how-to-multi-replicas.md)は、最大 3 つのレプリカ ノードで構成できます。 一般に、レプリカが増えると、プライマリをバックアップするノードが追加されるため、回復性が向上します。 レプリカが増えても、Azure Cache for Redis インスタンスは、データセンターまたは AZ 全体の停止によって大きな影響を受ける可能性があります。 複数のレプリカを[ゾーン冗長性](#zone-redundancy)と組み合わせて使用することで、キャッシュの可用性を向上させることができます。

## <a name="zone-redundancy"></a>ゾーン冗長性

>[!NOTE]
>これはプレビューとして利用できます。
>
>

Azure Cache for Redis によって、Premium レベルでのゾーン冗長構成がサポートされます。 [ゾーン冗長キャッシュ](cache-how-to-zone-redundancy.md)を使用すると、同じリージョン内の異なる [Azure Availability Zones](../availability-zones/az-overview.md) にわたってノードを配置できます。 これにより、単一障害点としてデータセンターまたは AZ の停止が除去され、キャッシュの全体的な可用性が向上します。

次の図はゾーン冗長の構成を示しています。

:::image type="content" source="media/cache-high-availability/zone-redundancy.png" alt-text="データ レプリケーションの設定&quot;:::
   
>[!NOTE]
>通常、Redis クライアントは、すべての読み取りおよび書き込み要求について、Redis Cache 内のプライマリ ノードと通信を行います。 レプリカ ノードから読み取るように、特定の Redis クライアントを構成することができます。
>
>

Redis Cache 内のプライマリ ノードが使用できない場合、レプリカは自動的に新しいプライマリになるように自身を昇格させます。 このプロセスは &quot; *フェールオーバー* ":::
   
選択した AZ に、ラウンドロビン方式でゾーン冗長キャッシュ内のノードが Azure Cache for Redis によって分散されます。 また、最初にプライマリとして機能するノードが決定されます。

ゾーン冗長キャッシュによって自動フェールオーバーが提供されます。 現在のプライマリ ノードが使用できない場合、レプリカの 1 つに引き継がれます。 新しいプライマリ ノードが異なる AZ に配置されている場合、アプリケーションでのキャッシュ応答時間が長くなる可能性があります。 AZ は地理的に分離されています。 AZ を切り替えると、アプリケーションとキャッシュがホストされている場所の間の物理的な距離が変わります。 この変更は、アプリケーションからキャッシュへのラウンドトリップ ネットワークの待機時間に影響します。 余分な待機時間は、ほとんどのアプリケーションで許容範囲内に収まることが想定されます。 アプリケーションをテストし、確実にゾーン冗長キャッシュで適切に動作できるようにすることをお勧めします。

## <a name="geo-replication"></a>geo レプリケーション

geo レプリケーションは主にディザスター リカバリーのために設計されています。 これにより、異なる Azure リージョンで Azure Cache for Redis インスタンスを構成し、プライマリ キャッシュをバックアップすることができます。 geo レプリケーションのしくみについては、[Azure Cache for Redis の geo レプリケーションの設定](cache-how-to-geo-replication.md)に関するページで詳しく説明されています。

## <a name="next-steps"></a>次の手順

Azure Cache for Redis の高可用性オプションを構成する方法について学習します。

* [Azure Cache for Redis Premium サービス レベル](cache-overview.md#service-tiers)
* [Azure Cache for Redis にレプリカを追加する](cache-how-to-multi-replicas.md)
* [Azure Cache for Redis のゾーン冗長性を有効にする](cache-how-to-zone-redundancy.md)
* [Azure Cache for Redis の geo レプリケーションを設定する](cache-how-to-geo-replication.md)