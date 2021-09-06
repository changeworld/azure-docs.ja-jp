---
title: スキャンとインジェスト
description: この記事では、Azure Purview でのスキャンとインジェストについて説明します。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: 140e87f4e03081825fe75ba73b7c5ebd33b20ada
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2021
ms.locfileid: "122398070"
---
# <a name="scans-and-ingestion-in-azure-purview"></a>Azure Purview でのスキャンとインジェスト

この記事では、Azure Purview のスキャンとインジェストの機能について説明します。 これらの機能では、Purview アカウントをソースに接続して、データ マップとデータ カタログを設定し、Purview からデータの探索と管理を開始できるようにします。

## <a name="scanning"></a>スキャン

Purview アカウントでデータ ソースを[登録](manage-data-sources.md)したら、次の手順ではデータ ソースをスキャンします。 スキャン プロセスでは、データ ソースへの接続を確立して、名前、ファイル サイズ、列など、技術的なメタデータをキャプチャします。 また、構造化データ ソースのスキーマを抽出して、スキーマの分類を適用します。[Purview アカウントが Microsoft 365 セキュリティ/コンプライアンス センター (SCC) に接続されている場合は、秘密度ラベルを適用します](create-sensitivity-label.md)。 スキャン プロセスをトリガーしてすぐに実行したり、定期的に実行したりして、Purview アカウントを最新の状態に保つことができます。

各スキャンには、必要な情報のソースのみをスキャンするために適用できるカスタマイズがあります。

### <a name="scope-your-scan"></a>スキャンの範囲を指定する

ソースをスキャンする場合は、データ ソース全体をスキャンするか、スキャンする特定のエンティティ (フォルダー/テーブル) のみを選択できます。 使用可能なオプションは、スキャンするソースによって異なり、1 回限りのスキャンと、スケジュールされたスキャンの両方で定義できます。

たとえば、[Azure SQL Database のスキャンを作成して実行する](register-scan-azure-sql-database.md#creating-and-running-a-scan)場合、スキャンするテーブルを選択するか、データベース全体を選択できます。

### <a name="scan-rule-set"></a>スキャン ルール セット

スキャン ルール セットでは、ソースのいずれかに対してスキャンを実行するときに検索する情報の種類を決定します。 使用可能なルールは、スキャンするソースの種類によって異なりますが、スキャンする[ファイルの種類](sources-and-scans.md#file-types-supported-for-scanning)や必要な[分類](supported-classifications.md)の種類などの情報が含まれます。

多くのデータ ソースの種類で既に使用できる[システム スキャン ルールセット](create-a-scan-rule-set.md#system-scan-rule-sets)がありますが、[独自のスキャン ルール セットを作成して](create-a-scan-rule-set.md)、組織に合わせてスキャンを調整することもできます。

## <a name="ingestion"></a>データの取り込み

スキャン プロセスによって識別される技術的なメタデータまたは分類は、その後インジェストに送信されます。 インジェスト プロセスでは、データ マップを設定し、Purview によって管理されます。  インジェストでは、スキャンからの入力を分析して、[リソース セット パターンを適用し](concept-resource-sets.md#how-azure-purview-detects-resource-sets)、使用可能な[データ系列](concept-data-lineage.md)情報を設定してから、データ マップを自動的に読み込みます。 資産/スキーマは、インジェストの完了後にのみ検出またはキュレーションできます。 そのため、スキャンが完了したが、データ マップまたはカタログに資産が表示されていない場合は、インジェスト プロセスが完了するまで待つ必要があります。

## <a name="next-steps"></a>次の手順

詳細またはソースのスキャンに関する具体的な手順については、次のリンクを参照してください。

* リソース セットについては、[リソース セットに関する記事](concept-resource-sets.md)を参照してください。
* [Azure SQL Database の登録とスキャンの方法](register-scan-azure-sql-database.md#creating-and-running-a-scan)
* [Azure Purview のデータ系列](catalog-lineage-user-guide.md)
