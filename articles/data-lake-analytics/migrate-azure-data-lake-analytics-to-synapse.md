---
title: Azure Data Lake Analytics を Azure Synapse Analytics に移行します。
description: この記事では、Azure Data Lake Analytics から Azure Synapse Analytics に移行する方法について説明します。
author: lingluo0531
ms.author: lingluo
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: migrate-azure-data-lake-analytics-to-synapse
ms.date: 08/25/2021
ms.openlocfilehash: 34a199fe8f488add4f1f2cdd9357dd9ac718d4f0
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123254968"
---
# <a name="migrate-azure-data-lake-analytics-to-azure-synapse-analytics"></a>Azure Data Lake Analytics を Azure Synapse Analytics に移行する

Microsoft は、データ レイクとデータ ウェアハウスの両方を統合して独自のビッグ データ分析エクスペリエンスを実現することを目的とした Azure Synapse Analytics を発表しました。 これを利用することで、さまざまなデータを収集して分析し、データの非効率性を解決し、共同作業できるようになります。 さらに、Synapse は Azure Machine Learning および Power BI と統合されているので、組織がデータから分析情報を取得できるだけでなく、すべてのスマート アプリに機械学習を実行する能力が高まります。 

このドキュメントでは、Azure Data Lake Analytics から Azure Synapse Analytics への移行方法について説明します。 

## <a name="recommended-approach"></a>推奨される方法
- 手順 1:適応性を評価する
- 手順 2:移行を準備する
- 手順 3: データとアプリケーション ワークロードを移行する
- 手順 4: Azure Data Lake Analytics を Azure Synapse Analytics に切り替える

### <a name="step-1-assess-readiness"></a>手順 1:適応性を評価する

1. 「[Azure Synapse Analytics での Apache Spark](../synapse-analytics/spark/apache-spark-overview.md)」を参照し、Azure Data Lake Analytics と Azure Synapse Analytics 上の Spark の主な違いを理解してください。 

    |Item | Azure Data Lake Analytics | Synapse 上の Spark |
    | --- | --- |--- |
    | 価格  |分析ユニット時間あたり |仮想コア時間あたり|
    |エンジン     |Azure Data Lake Analytics  |Apache Spark
    |既定のプログラミング言語    |U-SQL   |T-SQL、Python、Scala、Spark SQL、.NET
    |ソリューション エクスプローラー   |Azure Data Lake Storage    |Azure Blob Storage、Azure Data Lake Storage

2. 「<a href="#questionnaire">移行評価のためのアンケート</a>」を確認し、検討対象として考えられるリスクを列挙します。 

### <a name="step-2-prepare-to-migrate"></a>手順 2:移行を準備する

1.  移行するジョブとデータを特定します。
    -   この機会を利用して、使用しなくなったジョブをクリーンアップします。 すべてのジョブを一度に移行することを計画しているのでない限り、この時間を使って、段階的に移行できるジョブの論理グループを明らかにします。
    -   データのサイズを評価し、Apache Spark のデータ形式を把握します。 U-SQL スクリプトを確認し、スクリプトの書き換え作業を評価し、Apache Spark コードの概念を理解します。

2.  移行がビジネスに与える影響を決定します。 たとえば、移行の実行中にダウンタイムを許容できるかどうかなどです。

3.  移行計画を作成します。

### <a name="step-3-migrate-data-and-application-workload"></a>手順 3: データとアプリケーション ワークロードを移行する

1.  Azure Data Lake Storage Gen1 から Azure Data Lake Storage Gen2 にデータを移行します。 <br></br>
    Azure Data Lake Storage Gen1 は 2024 年 2 月に廃止される予定です。[公式発表](https://azure.microsoft.com/updates/action-required-switch-to-azure-data-lake-storage-gen2-by-29-february-2024/)のページを参照してください。 まず Gen2 にデータを移行することをお勧めします。 [Azure Data Lake Analytics U-SQL 開発者向けの Apache Spark データ形式の概要](understand-spark-data-formats.md)に関するページを参照し、ファイルと U-SQL テーブルに格納されているデータの両方を移動して、Azure Synapse Analytics からアクセスできるようにします。  移行ガイドの詳細については[こちら](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)を参照してください。 

2.  U-SQL スクリプトを Spark に変換します。 
    [Azure Data Lake Analytics U-SQL 開発者向けの Apache Spark コードの概念の概要](understand-spark-code-concepts.md)に関するページを参照し、U-SQL スクリプトを Spark に変換します。 

3.  ジョブ オーケストレーション パイプラインを新しい Spark プログラムに変換するか、作成し直します。

### <a name="step-4-cut-over-from-azure-data-lake-analytics-to-azure-synapse-analytics"></a>手順 4: Azure Data Lake Analytics を Azure Synapse Analytics に切り替える

アプリケーションとワークロードが安定していることを確認したら、Azure Synapse Analytics を使用してビジネス シナリオに対応できるようになります。 Azure Data Lake Analytics で実行されている残りのパイプラインをすべてオフにし、Azure Data Lake Analytics アカウントを停止します。

<a name="questionnaire"></a>
## <a name="questionnaire-for-migration-assessment"></a>移行評価のためのアンケート 

|カテゴリ   |質問  |リファレンス|
| --- | --- |--- |
|移行のサイズを評価する |Azure Data Lake Analytics アカウントはいくつありますか。 使用しているパイプライン数はいくつですか。 使用している U-SQL スクリプト数はいくつですか。| 移行するデータとスクリプトが多いほど、またスクリプトに使用されている UDO または UDF が多いほど、移行は難しくなります。 移行に必要な時間とリソースは、プロジェクトの規模に応じて適切に計画する必要があります。|
|データ ソース |データ ソースのサイズはどのくらいですか。 どのような種類のデータ形式を処理しますか。 |[Azure Data Lake Analytics U-SQL 開発者向けの Apache Spark データ形式の概要](understand-spark-data-formats.md)|
|データ出力 |出力データは後で使用するために保持しますか。 出力データが U-SQL テーブルに保存されている場合、それをどのように処理しますか。 | 出力データが頻繁に使用され、U-SQL テーブルに保存される場合は、スクリプトを変更し、出力データを Spark がサポートするデータ形式に変更する必要があります。|
|データ移行 |ストレージの移行計画は立てましたか。 |[Azure Data Lake Storage を Gen1 から Gen2 に移行する](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md) |
|U-SQL スクリプトの変換|UDO または UDF (.NET、python など) を使用していますか。この答えが "はい" の場合、UDO または UDF でどの言語を使用していますか。また、変換中に変換に関する問題はありますか。フェデレーション クエリは U-SQL で使用されていますか。|[Azure Data Lake Analytics U-SQL 開発者向けの Apache Spark コードの概念](understand-spark-code-concepts.md)|

## <a name="next-steps"></a>次のステップ

- [Azure Synapse Analytics](../synapse-analytics/get-started.md)
