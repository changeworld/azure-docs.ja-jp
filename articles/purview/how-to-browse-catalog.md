---
title: '方法: Data Catalog を参照する'
description: この記事では、資産の種類により Azure Purview データ カタログを参照する方法の概要を示します
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: ab5cb2856343d4fdcfcbd6c3c88d45889c191d2d
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129456837"
---
# <a name="browse-the-azure-purview-data-catalog"></a>Azure Purview データ カタログを参照する

データ カタログの検索は、データ コンシューマーが探しているものをわかっている場合は、データの検出に優れたツールとなりますが、多くの場合、ユーザーはデータ資産がどのように構成されているかを正確に把握していません。 Azure Purview データ カタログには、コレクションから使用できるデータ、またはカタログ内の各データ ソースの階層をたどることで、使用可能なデータを探索できる参照エクスペリエンスが用意されています。

参照エクスペリエンスにアクセスするには、データ カタログのホームページから [アセットを参照する] を選択します。

:::image type="content" source="media/how-to-browse-catalog/studio-home-page.png" alt-text="Purview のホーム ページ" border="true":::

## <a name="browse-by-collection"></a>コレクションで参照する

[Browse by collection]\(コレクション別に参照\) を使用すると、自分がデータ閲覧者またはキュレーターであるさまざまなコレクションを探索できます。

> [!NOTE]
> 自分がアクセスできるコレクションのみが表示されます。 詳細については、[コレクションの作成と管理す](how-to-create-and-manage-collections.md)に関するページを参照してください。

:::image type="content" source="media/how-to-browse-catalog/browse-by-collection.png" alt-text="[Browse by collection]\(コレクション別に参照\) ページを示すスクリーンショット" border="true":::

コレクションを選択すると、そのコレクションに含まれる資産の一覧が、検索で使用できるファセットやフィルターと共に表示されます。 コレクションには何千もの資産が含まれる可能性があるため、参照には Purview の検索関連性エンジンが使用され、重要度が高い資産が上位に表示されます。

:::image type="content" source="media/how-to-browse-catalog/browse-collection-results.png" alt-text="[Browse by collection]\(コレクション別に参照\) の結果を示すスクリーンショット" border="true":::

特定の注釈については、省略記号をクリックして AND 条件または OR 条件を選択できます。 

:::image type="content" source="./media/how-to-search-catalog/search-and-or-choice.png" alt-text="AND 条件または OR 条件を選択する方法を示すスクリーンショット" border="true":::

選択したコレクションに探しているデータが含まれていない場合は、関連するコレクションに簡単に移動したり、コレクション ツリー全体に戻って表示したりすることができます。

:::image type="content" source="media/how-to-browse-catalog/browse-collection-navigation.png" alt-text="コレクション間を移動する方法を示すスクリーンショット" border="true":::

探している資産が見つかったら、それを選択して、スキーマ、データ系列、詳細な分類リストなどのその他の詳細情報を表示できます。 資産の詳細ページについては、[カタログ資産の管理](catalog-asset-details.md)に関するページを参照してください。

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="資産の詳細ページを示すスクリーンショット" border="true":::

## <a name="browse-by-source-type"></a>ソースの種類別に参照

データ コンシューマーは [Browse by source type]\(ソースの種類別に参照\) でエクスプローラー ビューを使用してデータ ソースの階層を探索できます。 ソースの種類を選択すると、スキャンされたソースの一覧が表示されます。

たとえば、*DateDimension* と言う名前のデータセットは、Azure Data Lake Storage Gen 2 の *[ディメンション]* フォルダーの下で簡単に見つけることができます。 [Browse by source type]\(ソースの種類別に参照\) エクスペリエンスを使用して ADLS Gen 2 ストレージ アカウントに移動し、[サービス] > [コンテナー] > [フォルダー] を選択して特定の *[ディメンション]* フォルダーに移動し、*DateDimension* テーブルを参照できます。

階層型名前空間を使用したネイティブな参照エクスペリエンスは、対応するデータ ソースごとに提供されます。

> [!NOTE]
> 範囲を指定したスキャンが正常に終了した後、新たにスキャンされた資産がブラウズ エクスペリエンスで表示されるまでに、遅延が発生する場合があります。


1. **[Browse by source type]\(ソースの種類別に参照\)** ページでは、データ ソース別にタイルが分類されます。 各データ ソースの資産をさらに調べるには、対応するタイルを選択します。

    :::image type="content" source="media/how-to-browse-catalog/browse-asset-types.png" alt-text="[Browse asset types]\(資産の種類を参照\) ページ" border="true":::

   > [!TIP]
   > 特定のタイルは、データソースのコレクションをグループ化したものです。 たとえば、[Azure Storage アカウント] タイルには、すべての Azure Blob Storage と Azure Data Lake Storage Gen2 アカウントが含まれています。 [Azure SQL Server] タイルには、カタログに取り込まれた Azure SQL Database と Azure 専用 SQL プール インスタンスを含む Azure SQL Server の資産が表示されます。 

1. 次のページには、選択したデータの種類の最上位レベルの資産が一覧表示されます。 資産の 1 つを選択して、その内容を詳しく調べます。 たとえば、[Azure SQL Database] を選択すると、データ カタログに資産があるデータベースの一覧が表示されます。

    :::image type="content" source="media/how-to-browse-catalog/asset-type-specific-browse.png" alt-text="Azure SQL Database の参照ページ" border="true":::

1. エクスプローラー ビューが開きます。 左側のパネルで資産を選択して、参照を開始します。 ページの右側のパネルには、子資産が一覧表示されます。

    :::image type="content" source="media/how-to-browse-catalog/explorer-view.png" alt-text="エクスプローラー ビュー" border="true":::

1. 資産の詳細を表示するには、右端にある名前または省略記号ボタンを選択します。

    :::image type="content" source="media/how-to-browse-catalog/view-asset-detail-click-ellipses.png" alt-text="省略記号ボタンを選択して、資産の詳細ページを表示" border="true":::

## <a name="next-steps"></a>次のステップ

- [用語集の用語を作成、インポート、エクスポートする方法](how-to-create-import-export-glossary.md)
- [ビジネス用語集の用語テンプレートを管理する方法](how-to-manage-term-templates.md)
- [Purview データ カタログを検索する方法](how-to-search-catalog.md)
