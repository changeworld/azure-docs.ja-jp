---
title: ノード - Hyperscale (Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL のサーバー グループに含まれる 2 種類のノード (コーディネーターとワーカー) について説明します。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: af743ca56572f507091db01f11d3283294a9e3d5
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87382799"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL - Hyperscale (Citus) のノード

Hyperscale (Citus) のホスティング タイプは、Azure Database for PostgreSQL サーバー (ノードと呼ばれます) が、「シェアード ナッシング方式」のアーキテクチャで互いに調和できるようにします。 サーバー グループ内のノードは集合的に、単一サーバーで可能性のある分量より多くのデータを保持し、より多くの CPU コアを使用します。 このアーキテクチャでは、サーバー グループにノードを追加してデータベースを拡張することもできます。

## <a name="coordinator-and-workers"></a>コーディネーターとワーカー

すべてのサーバー グループに、1 つのコーディネーター ノードと複数のワーカーが含まれます。 アプリケーションは、クエリをコーディネーター ノードに送信し、このノードが関連するワーカーにリレーして、結果を累積します。 アプリケーションは、ワーカーに直接接続することはできません。

Hyperscale (Citus) を使用すると、データベース管理者は、異なる行を異なるワーカー ノード上に格納して、テーブルを "*分散*" させることができます。 分散テーブルは、Hyperscale パフォーマンスにとって重要です。 テーブルの分散に失敗すると、コーディネーター ノード上に完全に残り、マシン間の並列処理を利用できません。

分散テーブル上の各クエリに対して、コーディネーターは、必要なデータが単一または複数のノードのどちらに存在するかに応じて、単一のワーカー ノードにルーティングするか、または複数にわたって並列処理を行います。 コーディネーターは、メタデータ テーブルを参照して、どのように処理するかを決定します。 これらのテーブルは、ワーカー ノードの DNS 名およびヘルスと、ノード間でのデータの分布を追跡します。

## <a name="next-steps"></a>次のステップ
- 「[分散テーブル](concepts-hyperscale-distributed-data.md)」を参照してください
