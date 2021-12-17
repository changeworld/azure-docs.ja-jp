---
title: Azure Purview Data Lineage のベスト プラクティス
description: この記事では、Azure Purview データ Lineage のさまざまなデータ ソースを登録およびスキャンするためのベスト プラクティスについて説明します。
author: amberz
ms.author: amberz
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 10/25/2021
ms.openlocfilehash: 0036004e8222ce79fc43e5d6603abecc2e375b1a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477901"
---
# <a name="azure-purview-data-lineage-best-practices"></a>Azure Purview Data Lineage のベスト プラクティス

データ系列は、データの送信元にまたがるライフサイクルとして広く認識されており、データ資産間で時間の経過と共に移動します。 Purview では、次のように、組織のデータ資産のさまざまな部分から、さまざまな準備段階のデータの系列を取得できます。 
* さまざまなプラットフォームからステージングされる完全な生データ 
* 変換および準備されたデータ 
* 視覚化プラットフォームによって使用されるデータ

 
## <a name="intended-audience"></a>対象ユーザー

* データ エンジニア 
* データ サイエンティスト 
* データ所有者 

## <a name="why-do-you-need-adopt-lineage"></a>データ系列を採用する必要がある理由  

データ系列は、存在するデータ、格納場所、およびシステム間のフローを記述するプロセスです。 データ系列が重要である理由は多数ありますが、大まかにはこれらすべてが 3 つのカテゴリに分類される可能性があります。これらはすべて、ここで確認します。 
* レポート内のデータを追跡する 
* 影響分析 
* 変更をキャプチャし、データ ライフ サイクルを通じてデータが存在する場所をキャプチャする 

## <a name="azure-data-factory-lineage-best-practice-and-considerations"></a>Azure Data Factory のデータ系列のベスト プラクティスと考慮事項 

### <a name="azure-data-factory-instance"></a>Azure Data Factory インスタンス 

* データ系列は、Azure Data Factory の接続状態が [接続済み] に変わるまで、カタログに自動的には報告されません。 残りの状態は [切断] と [CannotAccess] では、データ系列をキャプチャできません。 

    :::image type="content" source="./media/how-to-link-azure-data-factory/data-factory-connection.png" alt-text="Data Factory 接続の一覧を示すスクリーンショット。" lightbox="./media/how-to-link-azure-data-factory/data-factory-connection.png":::

* 各 Azure Data Factory インスタンスは、1 つの Azure Purview アカウントにのみ接続できます。 別の Azure Purview アカウントで新しい接続を確立できますが、これにより既存の接続が切断されます。  

    :::image type="content" source="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png" alt-text="Azure Data Factory を切断するという警告を示すスクリーンショット。":::

* Azure Data Factory のマネージド ID は、Azure Purview アカウントでシステムを認証するために使用されます。Azure Purview ルート コレクションに対するデータ ファクトリのマネージド ID データ キュレーター ロールが必要です。 
* 一度にサポートするデータ工場は 10 を超える必要はありません。 一度に 10 個を超えるデータ ファクトリを追加する場合は、サポート チケットを提出してください。 

### <a name="azure-data-factory-activities"></a>Azure Data Factory アクティビティ  

* Azure Purview は、次の Azure Data Factory アクティビティからランタイムの系列をキャプチャします。 
    * [Copy アクティビティ](../data-factory/copy-activity-overview.md)
    * [Data Flow のアクティビティ](../data-factory/concepts-data-flow-overview.md)
    * [SSIS パッケージ アクティビティの実行](../data-factory/how-to-invoke-ssis-package-ssis-activity.md)

* ソースまたは宛先でサポートされていないデータ ストレージ システムが使用されている場合、Azure Purview は系列を削除します。  
    * Copy アクティビティでサポートされているデータ ソースは、「[Azure Data Factory に接続する](how-to-link-azure-data-factory.md)」の **Copy アクティビティのサポート** に一覧表示されます。
    * Data Flow アクティビティでサポートされているデータ ソースは、「[Azure Data Factory に接続する](how-to-link-azure-data-factory.md)」の **データ フローのサポート** に一覧表示されます。
    * SSIS でサポートされているデータ ソースは、「[SQL Server Integration Services のデータ系列](how-to-lineage-sql-server-integration-services.md)」の **SSIS 実行パッケージ アクティビティ サポート** に一覧表示されます。

* Azure Purview は、Azure Data Factory の Copy アクティビティが「[Azure Data Factory に接続する](how-to-link-azure-data-factory.md)」の「**コピー アクティビティの系列に関する制限事項**」に列挙された Copy アクティビティ機能を使用する場合、データ系列をキャプチャできません。  

* データフロー アクティビティの系列では、Azure Purview はソースとシンクのみをサポートしています。 データフロー変換の系列はまだサポートされていません。 

* Data Flow の系列は、Azure Purview のリソース セットと統合されていません。 

    **リソース セットの例 1**    

    修飾名: https://myblob.blob.core.windows.net/sample-data/data{N}.csv 

    表示名: "data" 

* SSIS パッケージの実行アクティビティの系列では、ソースと宛先のみがサポートされています。 変換の系列はまだサポートされていません。 

    :::image type="content" source="./media/concept-best-practices-lineage/ssis-lineage.png" alt-text="Purview での SSIS 実行の系列のスクリーンショット。" lightbox="./media/concept-best-practices-lineage/ssis-lineage.png":::

* [Azure Purview で Azure Data Factory のデータ系列をプッシュする](../data-factory/tutorial-push-lineage-to-purview.md)には、次のステップバイステップ ガイドを参照してください。  

## <a name="next-steps"></a>次のステップ
-  [データ ソースを管理する](./manage-data-sources.md)
