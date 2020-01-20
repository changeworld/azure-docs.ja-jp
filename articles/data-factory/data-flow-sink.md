---
title: マッピング データ フローでのシンク変換
description: マッピング データ フローでのシンク変換を構成する方法について説明します。
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: 1c65a456270cdca345504c07b927a7ef7e1f725b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440289"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>マッピング データ フローでのシンク変換

データを変換した後、そのデータを変換先のデータセットにシンクできます。 各データ フローには少なくとも 1 つのシンク変換が必要ですが、変換フローを完了するために必要な数だけのシンクに書き込むことができます。 追加のシンクに書き込むには、新しい分岐と条件分割によって新しいストリームを作成します。

各シンク変換は、厳密に 1 つの Data Factory データセットに関連付けられます。 データセットでは、データの形状と書き込みを行う場所が定義されます。

## <a name="supported-sink-connectors-in-mapping-data-flow"></a>マッピング データ フローでサポートされているシンク コネクタ

現在、シンク変換には次のデータセットを使用できます。
    
* [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON、Avro、テキスト、Parquet)
* [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON、Avro、テキスト、Parquet)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON、Avro、テキスト、Parquet)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

これらのコネクタに固有の設定は、 **[設定]** タブにあります。これらの設定に関する情報は、コネクタのドキュメントに記載されています。 

Azure Data Factory は、[90 を超えるネイティブ コネクタ](connector-overview.md)にアクセスできます。 それらの他のソースにデータ フローからデータを書き込むには、データ フローの完了後に、コピー アクティビティを使用して、サポートされているステージング領域のいずれかからそのデータを読み込みます。

## <a name="sink-settings"></a>シンクの設定

シンクを追加したら、 **[シンク]** タブから構成を行います。ここでは、シンクを書き込むデータセットを選択して作成できます 

![シンク設定](media/data-flow/sink-settings.png "シンクの設定")

**スキーマの誤差:** [スキーマの誤差](concepts-data-flow-schema-drift.md)は、データ フロー内の柔軟なスキーマをネイティブに処理するデータ ファクトリの機能であり、列の変更を明示的に定義する必要はありません。 シンク データ スキーマで定義されている内容の上に追加の列を記述するには、 **[Allow schema drift]\(スキーマの誤差を許可する\)** を有効にします。

**[Validate schema]:** [スキーマの検証] が選択されると、データセットの定義済みスキーマ内に列が見つからない場合、そのデータ フローはエラーになります。

## <a name="field-mapping"></a>フィールドのマッピング

シンクの **[マッピング]** タブ内では、Select 変換とほぼ同様に、受信列が書き込まれるかどうかを決定できます。 既定では、誤差のある列を含め、すべての入力列がマップされます。 これは、**自動マッピング**と呼ばれます。

自動マッピングを無効にすると、固定列ベースのマッピングまたはルールベースのマッピングのいずれかを追加するオプションを利用できます。 ルールベースのマッピングでは、パターン マッチングを使用した式を作成でき、固定マッピングでは、論理列名と物理列名がマップされます。 ルールベースのマッピングの詳細については、[マッピング データ フローの列パターン](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink)に関する記事を参照してください。

## <a name="data-preview-in-sink"></a>シンクでのデータのプレビュー

デバッグ クラスター上でデータ プレビューをフェッチすると、データはシンクに書き込まれません。 データの外観を示すスナップショットが返されますが、指定した変換先には何も書き込まれません。 シンクへのデータの書き込みをテストするには、パイプライン キャンバスからパイプラインのデバッグを実行します。

## <a name="next-steps"></a>次のステップ
これでデータ フローが作成されたので、[Data Flow アクティビティをパイプラインに](concepts-data-flow-overview.md)追加します。
