---
title: '方法: Data Catalog を検索する'
description: この記事では、データ カタログの検索方法の概要を説明します。
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 7799266bf9cece1ed789d6fab64ec970a09fbfcb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104588440"
---
# <a name="search-the-azure-purview-data-catalog"></a>Azure Purview Data Catalog を検索する

データ検出は、データ コンシューマーのデータ検出またはデータ ガバナンス ワークロードの最初の手順です。 必要なデータの場所がわからない場合、データ検出に時間がかかることがあります。 データを検出した後でも、そのデータを信頼できるかどうかや、そのデータの依存関係について疑問を感じる場合があります。

Azure Purview での検索の目的は、重要なデータをすばやく見つけられるように、データ検出プロセスを迅速化することです。 この記事では、探しているデータをすばやく見つけるための Azure Purview データ カタログの検索方法について説明します。

## <a name="search-the-catalog-for-assets"></a>カタログで資産を検索する

Azure Purview では、検索バーは Purview Studio UX の上部にあります。

:::image type="content" source="./media/how-to-search-catalog/purview-search-bar.png" alt-text="Azure Purview の検索バーの場所を示すスクリーンショット" border="true":::

検索バーをクリックすると、最近の検索履歴と最近アクセスした資産が表示されます。 最近表示したすべての資産を表示するには、[すべて表示] を選択します。

:::image type="content" source="./media/how-to-search-catalog/search-no-keywords.png" alt-text="キーワードが入力される前の検索バーを示すスクリーンショット" border="true":::

資産を識別するのに役立つキーワードを入力します (その名前、データ型、分類、用語集の用語など)。 目的の資産に関連するキーワードを入力すると、Azure Purview には、検索対象の候補と、一致する可能性がある資産の候補が表示されます。 検索を完了するには、[検索結果の表示] をクリックするか、"Enter" キーを押します。

:::image type="content" source="./media/how-to-search-catalog/search-keywords.png" alt-text="ユーザーがキーワードを入力した検索バーのスクリーンショット" border="true":::

検索結果ページには、入力されたキーワードに一致する資産の一覧が関連性の順に表示されます。 資産の関連性スコアに影響を与える可能性があるさまざまな要因があります。 検索対象の資産に適用される特定のデータ ストア、分類、連絡先、ラベル、用語集の用語を選択することで、一覧をさらに絞り込むことができます。

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="検索結果を示すスクリーンショット" border="true":::

 目的の資産をクリックすると、資産の詳細ページが表示されます。ここでは、スキーマ、系列、資産の所有者などのプロパティを確認できます。

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="資産の詳細ページを示すスクリーンショット" border="true":::

## <a name="search-query-syntax"></a>検索クエリ構文

すべての検索クエリは、キーワードと演算子で構成されています。 キーワードは、資産のプロパティの一部となるものです。 キーワードには、分類、用語集の用語、資産の説明、または資産名が考えられます。 キーワードは、一致を検索するプロパティの一部だけでも構いません。 キーワードと、下に一覧表示されている演算子を使用して、探している資産が Azure Purview によって確実に返されるようにします。 

検索クエリの作成に使用できる演算子を下に示します。 演算子は、1 つのクエリで必要に応じて何度でも組み合わせることができます。

| 演算子 | 定義 | 例 |
| -------- | ---------- | ------- |
| OR | 資産に、2 つのキーワードのうち少なくとも 1 つが含まれている必要があることを指定します。 すべて大文字にする必要があります。 空白も OR 演算子となります。  | クエリ `hive OR database` では、"hive" または "database"、あるいはその両方を含む資産が返されます。 |
| AND | 資産に、両方のキーワードが含まれている必要があることを指定します。 すべて大文字にする必要があります。 | クエリ `hive AND database` では、"hive" と "database" の両方を含む資産が返されます。 |
| NOT | 資産に、NOT 句の右側のキーワードが含まれていてはならないことを指定します。 | クエリ `hive NOT database` では、"hive" を含み、"database" を含まない資産が返されます。 |
| () | 一連のキーワードと演算子をまとめてグループ化します。 複数の演算子を組み合わせる場合は、かっこで演算の順序を指定します。 | クエリ `hive AND (database OR warehouse)` では、"hive" と、"database" または "warehouse"、あるいはその両方を含む資産が返されます。 |
| "" | クエリが一致する必要がある語句の正確な内容を指定します。 | クエリ `"hive database"` では、そのプロパティに "hive database" という語句を含む資産が返されます。 |
| * | 1 文字から多数の文字に一致するワイルドカード。 キーワードの最初の文字にすることはできません。 | クエリ `hiv\`* では、 "hive" や "hive-table" などの "hiv" で始まるプロパティを持つ資産が返されます。 |
| ? | 1 つの文字に一致するワイルドカード。 キーワードの最初の文字にすることはできません。 | クエリ `hiv?` では、"hiv" で始まるプロパティを持ち、"hive" や "hiva" などの 4 文字である資産が返されます。 |

> [!Note]
> ブール演算子 (**AND**、**OR**、**NOT**) は、常にすべて大文字で指定します。 それ以外については、大文字と小文字の区別はなく、余分なスペースも問題にはなりません。

## <a name="next-steps"></a>次のステップ

- [用語集の用語を作成、インポート、エクスポートする方法](how-to-create-import-export-glossary.md)
- [ビジネス用語集の用語テンプレートを管理する方法](how-to-manage-term-templates.md)
