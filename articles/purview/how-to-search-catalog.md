---
title: '方法: Data Catalog を検索する'
description: この記事では、Azure Purview データ カタログを検索する方法の概要について説明します。
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: 329213fc37edae93d3871c1a52b6d5b73f8f76ed
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129659566"
---
# <a name="search-the-azure-purview-data-catalog"></a>Azure Purview Data Catalog を検索する

データがスキャンされ、Azure Purview データ マップに取り込まれた後、データ コンシューマーは、分析またはガバナンスのワークロードに必要なデータを簡単に見つける必要があります。 必要なデータの場所がわからない場合、データ検出に時間がかかることがあります。 データを検出した後でも、そのデータを信頼できるかどうかや、そのデータの依存関係について疑問を感じる場合があります。

Azure Purview での検索の目的は、重要なデータをすばやく見つけられるように、データ検出プロセスを迅速化することです。 この記事では、探しているデータをすばやく見つけるための Azure Purview データ カタログの検索方法について説明します。

## <a name="search-the-catalog-for-assets"></a>カタログで資産を検索する

検索バーには、Purview Studio UX の上部バーからすぐにアクセスできます。 データ カタログのホーム ページでは、画面の中央に検索バーがあります。

:::image type="content" source="./media/how-to-search-catalog/purview-search-bar.png" alt-text="Azure Purview の検索バーの場所を示すスクリーンショット" border="true":::

検索バーをクリックすると、検索履歴と、データ カタログで最近アクセスされた資産が表示されます。 これにより、既に行われた以前のデータ探索からすばやく選択できます。

:::image type="content" source="./media/how-to-search-catalog/search-no-keywords.png" alt-text="キーワードが入力される前の検索バーを示すスクリーンショット" border="true":::

資産を識別するのに役立つキーワードを入力します (その名前、データ型、分類、用語集の用語など)。 検索キーワードを入力すると、ニーズに合った資産と検索が、Purview によって動的に提案されます。 検索を完了するには、[検索結果の表示] をクリックするか、"Enter" キーを押します。

:::image type="content" source="./media/how-to-search-catalog/search-keywords.png" alt-text="ユーザーがキーワードを入力した検索バーのスクリーンショット" border="true":::

検索を入力すると、入力したキーワードと一致し、ユーザーがデータ閲覧者になっているデータ資産の一覧が、Purview から返されます。

Purview の関連性エンジンにより、すべての一致が並べ替えられ、ユーザーにとって役に立つと思われるものに基づいてランク付けされます。 たとえば、データ スチュワードが用語集の用語を割り当て、説明を指定してある、複数のキーワードについて一致するテーブルは、注釈のないフォルダーより、データ コンシューマーに対して興味深いものである可能性があります。 資産の関連性スコアは大きな要因セットによって決定され、Purview の検索チームは、ユーザーにとって価値のある検索結果が上位に表示されるように、関連性エンジンを常にチューニングしています。

探している資産が上位の結果に含まれない場合は、左側のファセットを使用して、用語集の用語、分類、含まれているコレクションなどのビジネス メタデータでフィルター処理できます。 Azure Data Lake Storage Gen2 や Azure SQL Database などの特定のデータ ソースの種類に関心がある場合は、ソースの種類のピル フィルターを使用して検索を絞り込むことができます。

> [!NOTE]
> 検索からは、ユーザーがデータ閲覧者またはキュレーターになっているコレクション内の資産だけが返されます。 詳細については、[コレクションの作成と管理](how-to-create-and-manage-collections.md)に関するページを参照してください。

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="検索結果を示すスクリーンショット" border="true":::

特定の注釈については、省略記号をクリックして AND 条件または OR 条件を選択できます。 

:::image type="content" source="./media/how-to-search-catalog/search-and-or-choice.png" alt-text="AND 条件または OR 条件を選択する方法を示すスクリーンショット" border="true":::

探している資産が見つかったら、それを選択して、スキーマ、データ系列、詳細な分類リストなどの詳細情報を表示できます。 資産の詳細ページについては、[カタログ資産の管理](catalog-asset-details.md)に関する記事を参照してください。

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="資産の詳細ページを示すスクリーンショット" border="true":::

## <a name="bulk-edit-search-results"></a>検索結果を一括編集する

検索によって返される複数の資産を変更する場合は、Azure Purview では用語集の用語、分類、連絡先を一括で変更できます。 詳細については、[資産の一括編集](how-to-bulk-edit-assets.md)に関するガイドを参照してください。

## <a name="browse-the-data-catalog"></a>データ カタログを参照する

検索は、探しているものがわかっている場合は便利ですが、データ コンシューマーは利用可能なデータを探索したい場合があります。 Azure Purview データ カタログには、コレクションから使用できるデータ、またはカタログ内の各データ ソースの階層をたどることで、使用可能なデータを探索できる参照エクスペリエンスが用意されています。 詳細については、[データ カタログの参照](how-to-browse-catalog.md)に関する記事を参照してください。

## <a name="search-query-syntax"></a>検索クエリ構文

すべての検索クエリは、キーワードと演算子で構成されています。 キーワードは、資産のプロパティの一部となるものです。 キーワードには、分類、用語集の用語、資産の説明、または資産名が考えられます。 キーワードは、一致を検索するプロパティの一部だけでも構いません。 キーワードと、下に一覧表示されている演算子を使用して、探している資産が Azure Purview によって確実に返されるようにします。

スペース、ダッシュ、コンマなどの特定の文字が区切り記号として解釈されます。 `hive-database` のような文字列の検索は、2 つのキーワード `hive database` の検索と同じです。 

検索クエリの作成に使用できる演算子を下に示します。 演算子は、1 つのクエリで必要に応じて何度でも組み合わせることができます。

| 演算子 | 定義 | 例 |
| -------- | ---------- | ------- |
| OR | 資産に、2 つのキーワードのうち少なくとも 1 つが含まれている必要があることを指定します。 すべて大文字にする必要があります。 空白も OR 演算子となります。  | クエリ `hive OR database` では、"hive" または "database"、あるいはその両方を含む資産が返されます。 |
| AND | 資産に、両方のキーワードが含まれている必要があることを指定します。 すべて大文字にする必要があります。 | クエリ `hive AND database` では、"hive" と "database" の両方を含む資産が返されます。 |
| NOT | 資産に、NOT 句の右側のキーワードが含まれていてはならないことを指定します。 | クエリ `hive NOT database` では、"hive" を含み、"database" を含まない資産が返されます。 |
| () | 一連のキーワードと演算子をまとめてグループ化します。 複数の演算子を組み合わせる場合は、かっこで演算の順序を指定します。 | クエリ `hive AND (database OR warehouse)` では、"hive" と、"database" または "warehouse"、あるいはその両方を含む資産が返されます。 |
| "" | クエリが一致する必要がある語句の正確な内容を指定します。 | クエリ `"hive database"` では、そのプロパティに "hive database" という語句を含む資産が返されます。 |
| * | 1 文字から多数の文字に一致するワイルドカード。 キーワードの最初の文字にすることはできません。 | クエリ `dat*` では、"data" や "database" など、"dat" で始まるプロパティを持つ資産が返されます。 |
| ? | 1 つの文字に一致するワイルドカード。 キーワードの最初の文字にすることはできません。 | クエリ `dat?` では、"date" や "data" など、"dat" で始まる 4 文字のプロパティを持つ資産が返されます。 |

> [!Note]
> ブール演算子 (**AND**、**OR**、**NOT**) は、常にすべて大文字で指定します。 それ以外については、大文字と小文字の区別はなく、余分なスペースも問題にはなりません。

## <a name="next-steps"></a>次のステップ

- [用語集の用語を作成、インポート、エクスポートする方法](how-to-create-import-export-glossary.md)
- [ビジネス用語集の用語テンプレートを管理する方法](how-to-manage-term-templates.md)
