---
title: Azure Data Factory でのデータ ラングリング
description: Azure Data Factory でのデータ ラングリングの概要
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: 996b91040d16a3c2b966bb1b68e83f578d42f583
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129354988"
---
# <a name="what-is-data-wrangling"></a>データ ラングリングとは

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

組織は、日々増加し続ける複雑なデータを正確に分析するために、データの準備とラングリングの目的で重要なビジネス データを調査する能力を必要としています。 さまざまなビジネス プロセスのデータを使用して、価値創造までの時間を短縮できるようにするには、データの準備が不可欠です。

Data Factory では、Power Query を使用して、クラウド規模でコードフリーのデータ準備を繰り返し行うことができます。 Data Factory と [Power Query Online](/power-query/) の統合により、Power Query M の関数をパイプライン アクティビティとして使用できるようになります。

Data Factory は、Power Query Online マッシュアップ エディターによって生成された M を、Azure Data Factory データ フローに変換することによって、クラウド規模で実行するための Spark コードに変換します。 Power Query とデータ フローを使用したデータのラングリングは、データ エンジニアや "シティズン データ インテグレーター" にとっては特に便利です。

## <a name="use-cases"></a>ユース ケース

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFkW]

### <a name="fast-interactive-data-exploration-and-preparation"></a>迅速な対話型のデータ探索と準備

複数のデータ エンジニアやシティズン データ インテグレーターが、データセットをクラウド規模で対話的に探索し、準備することができます。 データ レイク内のデータの量、種類、速さが増大するなか、ユーザーは、データ セットを探索して準備するための効果的な方法を必要としています。 たとえば、2017 年以降の新規顧客を対象に、すべての顧客人口統計情報を含んだデータセットを作成するようなケースもあるでしょう。 既知のターゲットにマッピングするのでなければ、 レイクに公開する前に、データセットの探索、ラングリング、準備を行って、要件を満たすことになります。 ラングリングは、それほど厳格ではない分析シナリオでよく使用されます。 準備されたデータセットは、変換や機械学習のために使用できます。

### <a name="code-free-agile-data-preparation"></a>コードフリーのアジャイルなデータ準備

シティズン データ インテグレーターは、データを探して準備するのに、業務時間の 60% 以上を費やしています。 業務の生産性を向上させるため、彼らはこれらの作業をコーディングなし行う方法を探しています。 シティズン データ インテグレーターが Power Query Online などの既知のツールを使用して、データをスケーラブルに強化、整形、公開できるようになれば、生産性は大幅に向上します。 Azure Data Factory のラングリングを使用すれば、シティズン データ インテグレーターは使い慣れた Power Query Online マッシュアップ エディターを使用して、エラーを迅速に修正し、データを標準化し、高品質なデータを生成して、ビジネス上の意思決定をサポートできるようになります。

### <a name="data-validation-and-exploration"></a>データの検証と調査

コードを使用せずにデータを視覚的にスキャンして、外れ値や異常を除外し、データを高速分析用に整形することができます。

## <a name="supported-sources"></a>サポートされているソース

| コネクタ | データ形式 | 認証の種類 |
| -- | -- | --|
| [Azure Blob Storage](connector-azure-blob-storage.md) | CSV、Parquet、Excel | アカウント キー、サービス プリンシパル、MSI |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | CSV、Parquet、Excel | サービス プリンシパル、MSI |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | CSV、Parquet、Excel | アカウント キー、サービス プリンシパル、MSI |
| [Azure SQL Database](connector-azure-sql-database.md) | - | SQL 認証、MSI、サービス プリンシパル |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | SQL 認証、MSI、サービス プリンシパル |

## <a name="the-mashup-editor"></a>マッシュアップ エディター

Power Query アクティビティを作成すると、すべてのソース データセットがデータセット クエリになり、 **ADFResource** フォルダーに配置されます。 既定では、UserQuery は最初のデータセット クエリを指します。 データセット クエリに対する変更はサポートされておらず、永続化もされないため、すべての変換は UserQuery で実行される必要があります。 クエリの名前変更、追加、削除は、現在サポートされていません。

:::image type="content" source="media/wrangling-data-flow/editor.png" alt-text="ラングリング":::

現時点では、Power Query M 関数は、作成中に利用可能であっても、そのすべてがデータ ラングリングでサポートされているわけではありません。 Power Query のアクティビティの作成中、関数がサポートされていない場合は、次のエラー メッセージが表示されます。

`The Power Query Spark Runtime does not support the function`

サポートされている変換の詳細については、[Power Query データ ラングリングの関数](wrangling-functions.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

[データ ラングリング Power Query マッシュアップを作成する](wrangling-tutorial.md)方法について確認します。
