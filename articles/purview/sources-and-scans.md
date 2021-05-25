---
title: サポートされているデータ ソースとファイルの種類
description: この記事では、Purview でサポートされているデータ ソースとファイルの種類に関する概念について説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/24/2020
ms.custom: references_regions
ms.openlocfilehash: 15ade6fca3885bfabba7a23e2c3d8e561a9e6a0c
ms.sourcegitcommit: eda26a142f1d3b5a9253176e16b5cbaefe3e31b3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/11/2021
ms.locfileid: "109738466"
---
# <a name="supported-data-sources-and-file-types-in-azure-purview"></a>Azure Purview でサポートされているデータ ソースとファイルの種類

この記事では、Purview でサポートされているデータ ソース、ファイルの種類、およびスキャンの概念について説明します。

## <a name="supported-data-sources"></a>サポートされるデータ ソース

Purview では、[こちら](purview-connector-overview.md)に示すすべてのデータ ソースがサポートされています。

## <a name="file-types-supported-for-scanning"></a>スキャンがサポートされているファイルの種類

次のファイルの種類は、スキャンと、該当する場合はスキーマの抽出と分類がサポートされています。

- 拡張子でサポートされる構造化ファイル形式: AVRO、ORC、PARQUET、CSV、JSON、PSV、SSV、TSV、TXT、XML、GZIP
- 拡張子でサポートされるドキュメント ファイル形式: DOC、DOCM、DOCX、DOT、ODP、ODS、ODT、PDF、POT、PPS、PPSX、PPT、PPTM、PPTX、XLC、XLS、XLSB、XLSM、XLSX、XLT
- Purview では、カスタム ファイル拡張子とカスタム パーサーもサポートされています。
 
> [!Note]
> すべての Gzip ファイルは、内部の 1 つの csv ファイルにマップする必要があります。 Gzip ファイルは、システムおよびカスタムの分類ルールの対象となります。 現在、内部の複数のファイルにマップされた gzip ファイル、または csv 以外の任意のファイルの種類のスキャンはサポートされていません。 また、Purview スキャナーでは、スキーマの抽出と分類のために、スナップ圧縮されたファイルの種類が PARQUET や AVRO のスキャンがサポートされています。

> [!Note]
> Purview スキャナーでは、ファイルの種類が AVRO、ORC、PARQUET の複雑なデータ型のスキーマ抽出はサポートされていません。   

## <a name="sampling-within-a-file"></a>ファイル内のサンプリング

Purview の用語では、
- L1 スキャン: ファイル名、サイズ、完全修飾名などの基本的な情報とメタ データが抽出されます
- L2 スキャン: 構造化されたファイルの種類およびデータベース テーブルのスキーマが抽出されます
- L3 スキャン: 可能な場合スキーマが抽出され、サンプリングされたファイルにシステムおよびカスタムの分類ルールが適用されます

すべての構造化されたファイル形式について、Purview スキャナーによって次のようにファイルがサンプリングされます。

- 構造化されたファイルの種類の場合、各列の 128 行または 1 MB のいずれか小さい方がサンプリングされます。
- ドキュメント ファイル形式の場合、各ファイルにつき 20 MB がサンプリングされます。
    - ドキュメント ファイルが 20 MB を超える場合は、詳細スキャンの対象にはなりません (分類の対象になります)。 この場合、Purview によって、ファイル名や完全修飾名などの基本的なメタ データのみがキャプチャされます。

## <a name="resource-set-file-sampling"></a>リソース セット ファイルのサンプリング

フォルダーまたはパーティション ファイルのグループは、システムのリソース セット ポリシーまたは顧客定義のリソース セット ポリシーと一致する場合、"*リソース セット*" として Purview で検出されます。 リソース セットが検出されると、それに含まれている各フォルダーが Purview によってサンプリングされます。 リソース セットの詳細については、[こちら](concept-resource-sets.md)を参照してください。

ファイルの種類別のリソース セットのファイル サンプリングは次のとおりです。

- **区切られたファイル (CSV、PSV、SSV、TSV)** - 'リソース セット' と見なされるフォルダーまたはパーティション ファイルのグループ内で、100 個中 1 個のファイルがサンプリングされます (L3 スキャン)。
- **Data Lake ファイルの種類 (Parquet、Avro、Orc)** - "*リソース セット*" と見なされるフォルダーまたはパーティション ファイルのグループ内で、18446744073709551615 (long の最大値) 個中 1 個のファイルがサンプリングされます (L3 スキャン)。
- **その他の構造化されたファイルの種類 (JSON、XML、TXT)** - 'リソース セット' と見なされるフォルダーまたはパーティション ファイルのグループ内で、100 個中 1 個のファイルがサンプリングされます (L3 スキャン)。
- **SQL オブジェクトと CosmosDB エンティティ** - 各ファイルが L3 スキャンされます。
- **ドキュメント ファイルの種類** - 各ファイルが L3 スキャンされます。 リソース セットのパターンは、これらのファイルの種類には適用されません。

## <a name="classification"></a>分類

105 個すべてのシステム分類ルールが、構造化されたファイル形式に適用されます。 MCE 分類ルールのみがドキュメント ファイルの種類に適用されます (データ スキャン ネイティブの正規表現パターンではなく、ブルーム フィルターベースの検出)。 サポートされている分類の詳細については、「[Azure Purview でサポートされている分類](supported-classifications.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [チュートリアル: スタート キットの実行とデータのスキャン](tutorial-scan-data.md)
- [Azure Purview でデータ ソースを管理する (プレビュー)](manage-data-sources.md)