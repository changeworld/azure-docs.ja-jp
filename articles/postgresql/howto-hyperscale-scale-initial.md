---
title: 初期サーバー グループ サイズ - Hyperscale (Citus) - Azure Database for PostgreSQL
description: ユース ケースに適切な初期サイズを選択する
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 8b87cfc8276d13ccc7e12a4901489ea0b1e770a5
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012508"
---
# <a name="pick-initial-size-for-hyperscale-citus-server-group"></a>Hyperscale (Citus) サーバー グループの初期サイズを選択する

サーバー グループのサイズは、ノードの数とそのハードウェア容量のどちらも[簡単に変更できます](howto-hyperscale-scale-grow.md)。 ただし、新しいサーバー グループの初期サイズを選択する必要があります。 ここでは、適切な選択に向けたヒントをいくつか紹介します。

## <a name="use-cases"></a>ユース ケース

Hyperscale (Citus) は、次の方法で多く使用されます。

### <a name="multi-tenant-saas"></a>マルチテナント SaaS

既存の単一ノード PostgreSQL データベース インスタンスから Hyperscale (Citus) への移行時には、ワーカー仮想コアの数と RAM の合計が、元のインスタンスでの合計と等しいクラスターを選択します。 このようなシナリオでは、シャーディングによるリソース使用率の向上や、より小さいインデックスの許可などのため、パフォーマンスが 2 倍から 3 倍向上しています。

実際には、仮想コアの数を決定するだけです。 RAM 割り当ては、現在、[Hyperscale (Citus) 構成オプション](concepts-hyperscale-configuration-options.md)のページに記載されているように、仮想コアの数に基づいて決定されます。
コーディネーター ノードにはワーカーと同量の RAM は必要ありませんが、RAM と仮想コアを別々に選択する方法はありません。

### <a name="real-time-analytics"></a>リアルタイム分析

仮想コアの合計: 作業データが RAM に収まる場合、Hyperscale (Citus) では、ワーカー コアの数に比例してパフォーマンスが直線的に向上することが期待できます。 ニーズに合った適切な仮想コア数を決定するには、単一ノード データベースでのクエリの現在の待機時間と、Hyperscale (Citus) で必要な待機時間を考慮します。 現在の待機時間を必要な待機時間で除算し、結果を丸めます。

ワーカー RAM: 最適なケースは、ワーキング セットのほとんどがメモリに収まる十分なメモリを提供することです。 アプリケーションによって使用されるクエリの種類は、メモリ要件に影響します。 クエリで EXPLAIN ANALYZE を実行して、必要なメモリの量を確認できます。 [Hyperscale (Citus) 構成オプション](concepts-hyperscale-configuration-options.md)に関する記事で説明されているように、仮想コアと RAM はまとめてスケーリングされることに注意してください。

## <a name="choosing-a-hyperscale-citus-tier"></a>Hyperscale (Citus) レベルを選択する

> [!IMPORTANT]
> Hyperscale (Citus) Basic レベルは現在プレビュー段階です。  このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
>
> その他の新機能については、[Hyperscale (Citus) のプレビュー機能](hyperscale-preview-features.md)に関するページで全一覧をご覧いただけます。

上記のセクションでは、各ユース ケースに必要な仮想コア数と RAM 容量について説明しています。 これらの要求を満たすには、Basic レベルと Standard レベルの 2 つの Hyperscale (Citus) レベルのいずれかを選択します。

Basic レベルでは 1 つのデータベース ノードを使用して処理を実行するのに対し、Standard レベルではより多くのノードを使用できます。 それ以外の点ではこれらのレベルは同じであり、同じ機能を提供します。 場合によっては、1 つのノードの仮想コアとディスク領域を拡張して十分な量にすることができ、それ以外の場合は複数のノードを連携させる必要があります。

レベルの比較については、「[Basic レベル](concepts-hyperscale-tiers.md)」の概念ページを参照してください。

## <a name="next-steps"></a>次のステップ

- [サーバー グループをスケーリングする](howto-hyperscale-scale-grow.md)
- サーバー グループの[パフォーマンス オプション](concepts-hyperscale-configuration-options.md)の詳細を確認します。
