---
title: 'JavaScript チュートリアル: Web アプリに検索を追加する'
titleSuffix: Azure Cognitive Search
description: npm SDK (@azure/search-documents) を使用して、JavaScript でインデックスを作成し、検索インデックスに CSV データをインポートします。
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: 0fd28262f4a4b852386fa354037e69c5097109c5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104723537"
---
# <a name="2---create-and-load-search-index-with-javascript"></a>2 - JavaScript を使用して検索インデックスを作成して読み込む

以下を行って、検索が有効な Web サイトの構築を続行します。
* VS Code 拡張機能を使用して Search リソースを作成する
* サンプル スクリプトと Azure SDK ([@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents)) を使用して、JavaScript で新しいインデックスを作成し、データをインポートする

## <a name="create-an-azure-search-resource"></a>Azure Search リソースの作成 

Visual Studio Code 用の [Azure Cognitive Search](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch) 拡張機能を使用して、新しい Search リソースを作成します。

1. Visual Studio Code で[アクティビティ バー](https://code.visualstudio.com/docs/getstarted/userinterface)を開き、Azure アイコンを選択します。 

1. サイド バーで、`Azure: Cognitive Search` 領域の下にある **自分の Azure サブスクリプションを右クリック** し、 **[Create new search service]\(新しい検索サービスの作成\)** を選択します。

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-create-search-resource.png" alt-text="サイド バーで、**[Azure] の [Cognitive Search]** 領域の下にある Azure サブスクリプションを右クリックし、**[Create new search service]\(新しい検索サービスの作成\)** を選択します。":::

1. プロンプトに従って、次の情報を指定します。

    |Prompt|Enter|
    |--|--|
    |新しい Search サービスの、グローバルに一意な名前の入力|**この名前を覚えておいてください**。 このリソース名は、リソース エンドポイントの一部になります。|
    |Select a resource group for new resources (新しいリソースのリソース グループを選択する)|このチュートリアル用に作成したリソース グループを使用します。|
    |Search サービスの SKU の選択|このチュートリアルでは、 **[Free]\(無料\)** を選択します。 サービスを作成した後は、SKU 価格レベルを変更できません。|
    |新しいリソースの場所の選択。|近くのリージョンを選択します。|

1. プロンプトが完了すると、新しい Search リソースが作成されます。 

## <a name="get-your-search-resource-admin-key"></a>Search リソースの管理者キーを取得する

Visual Studio Code 拡張機能を使用して、自分の Search リソースの管理者キーを取得します。 

1. Visual Studio Code のサイド バーで、自分の Search リソースを右クリックし、 **[Copy Admin Key]\(管理者キーのコピー\)** を選択します。

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-copy-admin-key.png" alt-text="サイドバーで、自分の Search リソースを右クリックし、**[Copy Admin Key]\(管理者キーのコピー\)** を選択します。":::

1. この管理者キーは、[以降のセクション](#prepare-the-bulk-import-script-for-search)で使用する必要があるため保存してください。 

## <a name="prepare-the-bulk-import-script-for-search"></a>Search 用の一括インポート スクリプトを準備する

このスクリプトでは、Cognitive Search 用 Azure SDK を使用します。

* [npm パッケージ @azure/search-documents](https://www.npmjs.com/package/@azure/search-documents)
* [リファレンス ドキュメント](/javascript/api/overview/azure/search-documents-readme)

1. Visual Studio Code で、サブディレクトリ `search-web/bulk-insert` 内の `bulk_insert_books.js` ファイルを開き、次の変数を実際の値に置き換えて、Azure Search SDK による認証を行います。

    * YOUR-SEARCH-RESOURCE-NAME
    * YOUR-SEARCH-ADMIN-KEY

    :::code language="javascript" source="~/azure-search-javascript-samples/search-website/bulk-insert/bulk_insert_books.js" highlight="16,17" :::

1. プロジェクト ディレクトリのサブディレクトリ `search-web/bulk-insert` のために Visual Studio で統合ターミナルを開き、次のコマンドを実行して依存関係をインストールします。 

    ```bash
    npm install 
    ```

## <a name="run-the-bulk-import-script-for-search"></a>Search 用の一括インポート スクリプトを実行する

1. プロジェクト ディレクトリのサブディレクトリ `search-web/bulk-insert` に Visual Studio の統合ターミナルを引き続き使用し、次の bash コマンドを実行して `bulk_insert_books.js` スクリプトを実行します。

    ```javascript
    npm start
    ```

1. コードを実行すると、コンソールに進行状況が表示されます。 
1. アップロードが完了すると、コンソールに出力される最後のステートメントは "done" になります。

## <a name="review-the-new-search-index"></a>新しい検索インデックスを確認する

アップロードが完了すると、検索インデックスを使用できるようになります。 新しいインデックスを確認しましょう。

1. Visual Studio Code で、Azure Cognitive Search 拡張機能を開き、自分の Search リソースを選択します。  

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-resource.png" alt-text="Visual Studio Code で、Azure Cognitive Search 拡張機能を開き、自分の Search リソースを開きます。":::

1. [インデックス]、[ドキュメント]、`good-books` の順に展開してから、ドキュメントを選択して、ドキュメント固有のデータをすべて表示します。
 
    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-docs.png" lightbox="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-docs.png" alt-text="[インデックス]、&quot;good-books&quot; の順に展開してから、ドキュメントを選択します。":::

## <a name="copy-your-search-resource-name"></a>Search リソース名をコピーする

**Search リソース名** をメモします。 これは、Azure 関数アプリを自分の Search リソースに接続するために必要になります。 

> [!CAUTION]
> Azure 関数内で自分の Search 管理者キーを使用したくなるかもしれませんが、それでは最小特権の原則に沿っていません。 Azure 関数では、クエリ キーを使用して最小特権に準拠します。 

## <a name="next-steps"></a>次のステップ

[静的 Web アプリをデプロイする](tutorial-javascript-deploy-static-web-app.md)