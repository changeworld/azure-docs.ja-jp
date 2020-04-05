---
title: ノード - Hyperscale (Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL のサーバー グループに含まれる 2 種類のノード (コーディネーターとワーカー) について説明します。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 04ebb4298f8a5398b0aa9921d740e3eaacfd8e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974004"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL - Hyperscale (Citus) のノード

Hyperscale (Citus) のホスティング タイプは、Azure Database for PostgreSQL サーバー (ノードと呼ばれます) が、「シェアード ナッシング方式」のアーキテクチャで互いに調和できるようにします。 サーバー グループ内のノードは集合的に、単一サーバーで可能性のある分量より多くのデータを保持し、より多くの CPU コアを使用します。 このアーキテクチャでは、サーバー グループにノードを追加してデータベースを拡張することもできます。

## <a name="coordinator-and-workers"></a>コーディネーターとワーカー

すべてのサーバー グループに、1 つのコーディネーター ノードと複数のワーカーが含まれます。 アプリケーションは、クエリをコーディネーター ノードに送信し、このノードが関連するワーカーにリレーして、結果を累積します。 アプリケーションは、ワーカーに直接接続することはできません。

クエリごとに、コーディネーターは、必要なデータが単一のノードと複数のノードのどちらに存在するかによって、単一のワーカー ノードにルーティングするか、複数のノードにわたって並列処理します。 コーディネーターは、メタデータ テーブルを参照して、どのように処理するかを決定します。 これらのテーブルは、ワーカー ノードの DNS 名およびヘルスと、ノード間でのデータの分布を追跡します。

## <a name="next-steps"></a>次のステップ
- ノードで[分散データ](concepts-hyperscale-distributed-data.md)を格納する方法について学習する
