---
title: マッピング データ フローのソース変換
description: マッピング データ フローのソース変換を設定する方法について説明します。
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: b2f533e8bd9199025260aaca9cff587b13adce64
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606321"
---
# <a name="source-transformation-in-mapping-data-flow"></a>マッピング データ フローのソース変換 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

ソース変換は、データ フローのデータ ソースを構成します。 データ フローを設計する際、最初の手順では、常にソース変換を構成します。 ソースを追加するには、データ フローのキャンバスにある **[Add Source]\(ソースの追加\)** ボックスをクリックします。

各データ フローには少なくとも 1 つのソース変換が必要ですが、データ変換を完了するために必要な数だけソースを追加できます。 これらのソースは、結合、参照、または和集合変換を使用して結合できます。

各ソース変換が関連付けられる Data Factory データセットは 1 つだけです。 データセットは、書き込みまたは読み取りを行うデータの形状と場所を定義します。 ファイルベースのデータセットを使用している場合は、ソース内でワイルドカードやファイル リストを使用すると、一度に複数のファイルを操作できます。

## <a name="supported-source-connectors-in-mapping-data-flow"></a>マッピング データ フローでサポートされているソース コネクタ

Mapping Data Flow は、抽出、読み込み、変換 (ELT) のアプローチに従い、すべて Azure に存在する "*ステージング*" データセットを操作します。 現在は、次のデータセットをソース変換で使用できます。
    
* [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON、Avro、テキスト、Parquet)
* [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON、Avro、テキスト、Parquet)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON、Avro、テキスト、Parquet)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

これらのコネクタに固有の設定は、 **[Source options]\(ソース オプション\)** タブにあります。これらの設定に関する情報は、コネクタのドキュメントに記載されています。 

Azure Data Factory は、[90 を超えるネイティブ コネクタ](connector-overview.md)にアクセスできます。 それらの他のソースからのデータをデータ フローに含めるには、コピー アクティビティを使用して、サポートされているステージング領域のいずれかにそのデータを読み込みます。

## <a name="source-settings"></a>ソースの設定

ソースを追加したら、 **[Source Settings]** タブを使用して構成します。ここでは、ソースが指すデータセットを選択または作成できます。 また、データのスキーマとサンプリング オプションも選択できます。

![[Source Settings] タブ](media/data-flow/source1.png "[Source Settings] タブ")

**テスト接続:** ソース データセットで使用されているリンクされたサービスにデータ フローの Spark サービスが正常に接続できるかどうかをテストします。 この機能を有効にするにはデバッグ モードをオンにする必要があります。

**スキーマの誤差:** [スキーマの誤差](concepts-data-flow-schema-drift.md)は、データ フロー内の柔軟なスキーマをネイティブに処理するデータ ファクトリの機能であり、列の変更を明示的に定義する必要はありません。

* ソース列が頻繁に変更される場合は、 **[Allow schema drift]\(スキーマの誤差を許可\)** チェック ボックスをオンにします。 この設定により、すべての受信ソース フィールドが変換を通してシンクに流れることができます。

* **[Infer drifted column types]\(誤差のある列の型を推論\)** を選択すると、データ ファクトリは、検出された新しい列ごとにデータ型を検出して定義するよう指示されます。 この機能が無効になっている場合、誤差の列はすべて文字列型になります。

**[Validate schema]:** [Validate schema] を選択した場合、受信したソース データがデータセットの定義済みスキーマと一致しなければ、データ フローの実行は失敗します。

**[Skip line count]\(スキップ行数\):** この [Skip line count]\(スキップ行数\) フィールドでは、データセットの先頭で無視する行数を指定します。

**[Sampling]:** ソースからの行数を制限するには、サンプリングを有効にします。 デバッグの目的でソースのデータをテストまたはサンプリングする場合は、この設定を使用します。

**[Multiline rows]\(複数行\):** 値の途中に改行が含まれるなど、複数の行にわたる文字列値がソース テキスト ファイルに含まれる場合は、複数行を選択します。 この設定は、DelimitedText データセットでのみ使用できます。

ソースが正しく構成されていることを確認するには、デバッグ モードを有効にし、データ プレビューを取り込みます。 詳細については、[デバッグ モード](concepts-data-flow-debug-mode.md)に関するページを参照してください。

> [!NOTE]
> デバッグ モードを有効にすると、データ プレビュー時に、デバッグの設定での行数上限の構成によってソースのサンプリング設定が上書きされます。

## <a name="projection"></a>Projection

データセット内のスキーマと同様に、ソース内のプロジェクションでは、ソース データのデータの列、型、および形式が定義されます。 SQL や Parquet など、ほとんどのデータセットの種類では、ソース内のプロジェクトは、データセットで定義されたスキーマを反映するように固定されています。 ソース ファイルが厳密に型指定されていない場合 (たとえば、Parquet ファイルでなく csv ファイル)、このソース変換では各フィールドのデータ型を定義できます。

![プロジェクション タブでの設定](media/data-flow/source3.png "Projection")

テキスト ファイルが定義済みのスキーマを含まない場合は、Data Factory がデータ型をサンプリングして推論するように、 **[Detect data type]** を選択します。 **[Define default format]\(既定の形式の定義\)** を選択して、既定のデータ形式を自動検出します。

**スキーマをリセットする**と、参照されているデータベースにある定義にプロジェクションがリセットされます。

列のデータ型は、下流の派生列変換で変更できます。 選択変換を使用して、列の名前を変更します。

### <a name="import-schema"></a>Import schema

**[プロジェクション]** タブの **[スキーマのインポート]** ボタンでは、アクティブなデバッグ クラスターを使用し、スキーマ プロジェクションを作成できます。 あらゆるソースの種類で利用できますが、スキーマをここでインポートすると、データセットに定義されているプロジェクションがオーバーライドされます。 データセット オブジェクトは変更されません。

これは、複雑なデータ構造をサポートし、スキーマ定義を必要としない Avro や CosmosDB のようなデータセットで便利です。

## <a name="optimize-the-source-transformation"></a>ソース変換を最適化する

ソース変換の **[Optimize]** タブに、 **[Source]** というパーティションの種類が表示されることがあります。 このオプションは、ソースが Azure SQL Database の場合にのみ使用できます。 これは、Data Factory が SQL Database ソースに対して大規模なクエリを実行するために、接続を並列化しようとするためです。

![ソース パーティション設定](media/data-flow/sourcepart3.png "パーティション分割")

SQL Database ソースのデータをパーティション分割する必要はありませんが、パーティションは大規模なクエリの場合に便利です。 列やクエリに基づいてパーティションを作成できます。

### <a name="use-a-column-to-partition-data"></a>列を使用してデータをパーティション分割する

ソース テーブルから、パーティション分割する列を選択します。 パーティション数も設定します。

### <a name="use-a-query-to-partition-data"></a>クエリを使用してデータをパーティション分割する

クエリに基づいて接続をパーティション分割することを選択できます。 WHERE 述語の内容を入力します。 たとえば、「year > 1980」と入力します。

マッピング データ フロー内での最適化の詳細については、[[Optimize] タブ](concepts-data-flow-overview.md#optimize)に関する説明を参照してください。

## <a name="next-steps"></a>次のステップ

[Derived Column](data-flow-derived-column.md)と[Select](data-flow-select.md)の作成を開始します。
