---
title: 共有メタデータ モデル
description: Azure Synapse Analytics では、さまざまなワークスペース計算エンジンが、サーバーレス Apache Spark プールとサーバーレス SQL プールの間でデータベースとテーブルを共有できます。
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 10/05/2021
author: ma77b
ms.author: maburd
ms.reviewer: wiassaf
ms.openlocfilehash: 4f9feff8a88e5f8058af808b22ca07191193e41d
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129987121"
---
# <a name="azure-synapse-analytics-shared-metadata"></a>Azure Synapse Analytics の共有メタデータ

Azure Synapse Analytics では、さまざまなワークスペース計算エンジンが、Apache Spark プールとサーバーレス SQL プール間でデータベースとテーブルを共有できます。

この共有では、いわゆる最新のデータ ウェアハウス パターンがサポートされており、ワークスペースの SQL エンジンは、Spark で作成されたデータベースとテーブルにアクセスすることができます。 また、SQL エンジンは、他のエンジンと共有されていない独自のオブジェクトを作成することもできます。

## <a name="support-the-modern-data-warehouse"></a>最新のデータ ウェアハウスをサポートする

共有メタデータ モデルでは、次の方法で最新のデータウェアハウス パターンがサポートされます。

1. データ レイクのデータは、準備されたデータを、場合によっては複数のデータベースに含まれる (パーティション分割されている可能性のある)、Parquet でサポートされたテーブルに格納することにより、Spark を使用して効率的に準備され、構造化されます。

2. Spark によって作成されたデータベースとすべてのテーブルは、どの Azure Synapse ワークスペースの Spark プール インスタンスでも可視となり、どの Spark ジョブからも使用できます。 ワークスペース内のすべての Spark プールは、基になる同じカタログ メタ ストアを共有しているため、この機能は[アクセス許可](#security-model-at-a-glance)の対象になります。

3. Spark によって作成されたデータベースと Parquet または CSV でサポートされるテーブルは、ワークスペースのサーバーレス SQL プールに表示されるようになります。 [データベース](database.md)は、サーバーレス SQL プール メタデータに自動的に作成されます。また、Spark ジョブによって作成された[外部テーブルとマネージド テーブル](table.md)の両方に、対応するデータベースの `dbo` スキーマのサーバーレス SQL プール メタデータで外部テーブルとしてアクセスできるようになります。 

<!--[INSERT PICTURE]-->

<!--__Figure 1 -__ Supporting the Modern Data Warehouse Pattern with shared metadata-->

オブジェクトの同期は非同期に行われます。 オブジェクトが SQL コンテキストに表示されるまでには、数秒のわずかな遅延が発生します。 一度表示されると、それに対してクエリを実行できますが、それに対するアクセス権を持つ SQL エンジンによって更新または変更されることはありません。

## <a name="shared-metadata-objects"></a>共有メタデータ オブジェクト

Spark を使用すると、データベース、外部テーブル、マネージド テーブル、およびビューを作成できます。 Spark ビューでは、定義する Spark SQL ステートメントを処理するために Spark エンジンが必要であり、SQL エンジンでは処理できないため、Parquet または CSV ストレージ形式を使用するデータベースおよび含まれている外部テーブルとマネージド テーブルのみが、ワークスペースの SQL エンジンと共有されます。 Spark ビューは、Spark プール インスタンス間でのみ共有されます。

## <a name="security-model-at-a-glance"></a>セキュリティ モデルの概要

Spark データベースとテーブルは、SQL エンジンの同期された表現と共に、基になるストレージ レベルで保護されます。 クエリ送信者が使用権限を持つ任意のエンジンによってテーブルに対してクエリが実行されると、クエリ送信者のセキュリティ プリンシパルが、基になるファイルに渡されます。 アクセス許可は、ファイル システム レベルで確認されます。

詳細については、「[Azure Synapse Analytics の共有データベース](database.md)」を参照してください。

## <a name="change-maintenance"></a>メンテナンスを変更する

メタデータ オブジェクトが Spark で削除または変更されると、その変更が取得され、サーバーレス SQL プールに反映されます。 同期は非同期であり、変更は少し遅れて SQL エンジンに反映されます。

## <a name="next-steps"></a>次のステップ

- [Azure Synapse Analytics の共有メタデータ データベースの詳細情報](database.md)
- [Azure Synapse Analytics の共有メタデータ テーブルの詳細情報](table.md)

