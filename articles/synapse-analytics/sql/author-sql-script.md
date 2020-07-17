---
title: Azure Synapse Studio (プレビュー) 内の SQL スクリプト
description: Azure Synapse Studio (プレビュー) の SQL スクリプトの概要
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 940c6d6d96c5c1aa062397d21ea96dace2c09bae
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427399"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>Azure Synapse Studio (プレビュー) での SQL スクリプトの使用

Azure Synapse Studio (プレビュー) は、SQL クエリを作成するための SQL スクリプト Web インターフェイスを提供します。 SQL プール (プレビュー) または SQL オンデマンド (プレビュー) に接続できます。 

## <a name="begin-authoring-in-sql-script"></a>SQL スクリプトの作成を開始する 

SQL スクリプトで作成のエクスペリエンスを開始するには、いくつかの方法があります。 新しい SQL スクリプトを作成するには、次の方法のいずれかを使用します。

1. "+" アイコンを選択し、[SQL スクリプト] を選択します。

    > [!div class="mx-imgBorder"] 
    >![newsqlscript](./media/author-sql-script/newsqlscript.png)

2. [開発]、[SQL スクリプト] の [アクション] メニューから、[New SQL script]\(新しい SQL スクリプト\) を選択します。 

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript2actions.png)

or 

3. SQL スクリプトの開発の [アクション] メニューから、[インポート] を選択し、ローカル ストレージから既存の SQL スクリプトを選択します。

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>SQL スクリプトを作成する

1. [プロパティ] ボタンを選択し、作成する SQL スクリプトの名前を選択して、SQL スクリプトに割り当てられた既定の名前を置き換えます。

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscriptrename.png)

1. [接続先] ドロップダウン メニューから、特定の SQL プールまたは SQL オンデマンドを選択します。 または、必要に応じて、"データベースの使用" からデータベースを選択します。

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlchoosepool.png)

1. IntelliSense 機能を使用して、SQL スクリプトの作成を開始します。

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>SQL スクリプトを実行する

SQL スクリプトを実行するには、[実行] ボタンを選択します。 既定では、結果はテーブルに表示されます。

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>結果をエクスポートする

結果は、さまざまな形式 (CSV、Excel、JSON、XML など) でローカル ストレージにエクスポートできます。そのためには、[結果のエクスポート] を選択して、拡張子を選択します。

[グラフ] ボタンを選択して、SQL スクリプトの結果をグラフで視覚化することもできます。 [グラフの種類] と [Category column]\(カテゴリ列\) を選択します。 [イメージとして保存] を選択して、グラフを画像にエクスポートします。 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>Parquet ファイルからデータを探索する

SQL スクリプトを使用してストレージ アカウント内の Parquet ファイルを探索して、ファイルの内容をプレビューすることができます。 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>SQL テーブル、外部テーブル、ビュー

データの [アクション] メニューを選択すると、[New SQL script]\(新しい SQL スクリプト\)、[Select TOP 1000 rows]\(上位 1000 行の選択\)、[作成]、[DROP and CREATE]\(DROP および CREATE\) などのアクションを選択できます。 SQL プールおよび SQL オンデマンドのノードを右クリックして、使用可能なジェスチャを調べます。

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>次のステップ

SQL スクリプトの作成の詳細については、「[Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)」を参照してください。