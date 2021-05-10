---
title: 'JavaScript チュートリアル: 検索が有効な Web サイトをデプロイする'
titleSuffix: Azure Cognitive Search
description: 検索が有効な Web サイトを Azure 静的 Web アプリにデプロイします。
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: a49ede283899cec42898672f5a376221265dea10
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104723518"
---
# <a name="3---deploy-the-search-enabled-website"></a>3 - 検索が有効な Web サイトをデプロイする

検索が有効な Web サイトを Azure 静的 Web アプリとしてデプロイします。 このデプロイには、React アプリと関数アプリの両方が含まれています。  

静的 Web アプリでは、サンプル リポジトリのフォークを使用して、GitHub からデプロイのための情報とファイルをプルします。  

## <a name="create-a-static-web-app-in-visual-studio-code"></a>Visual Studio Code で静的 Web アプリを作成する

1. アクティビティ バーから **[Azure]** を選択し、サイド バーから **[Static Web Apps]** を選択します。 
1. サブスクリプション名を右クリックし、 **[Create Static Web App (Advanced)]\(静的 Web アプリの作成 (詳細設定)\)** を選択します。    

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-create-static-web-app-resource-advanced.png" alt-text="サブスクリプション名を右クリックし、**[Create Static Web App (Advanced)]\(静的 Web アプリの作成 (詳細設定)\)** を選択します。":::

1. プロンプトに従って、次の情報を指定します。

    |Prompt|Enter|
    |--|--|
    |静的 Web アプリを作成する方法|既存の GitHub リポジトリを使用します|
    |組織の選択|"_独自_" の GitHub の別名を組織として選択します。|
    |リポジトリの選択|一覧から **azure-search-javascript-samples** を選択します。 |
    |リポジトリのブランチの選択|一覧から **master** を選択します。 |
    |新しい静的 Web アプリの名前の入力。|自分のリソースに一意の名前を作成します。 たとえば `joansmith-azure-search-javascript-samples` のように、リポジトリ名の先頭に自分の名前を追加できます。 |
    |新しいリソース用のリソース グループの選択。|このチュートリアル用に作成したリソース グループを使用します。|
    |既定のプロジェクト構造を構成するためのビルド プリセットの選択。|[**カスタム**] を選択します。|
    |アプリケーション コードの場所の選択|`search-website`|
    |Azure 関数コードの場所の選択|`search-website/api`|
    |ビルド出力のパスの入力|build|
    |新しいリソースの場所の選択。|近くのリージョンを選択します。|

1. リソースが作成されたら、通知から **[Open Actions in GitHub]\(GitHub でアクションを開く\)** を選択します。 これにより、自分がフォークしたリポジトリを参照するブラウザー ウィンドウが開きます。 

    アクションの一覧は、自分の Web アプリ (クライアントと関数の両方) が Azure 静的 Web アプリに正常にプッシュされたことを示しています。 

    ビルドとデプロイが完了するまで待ってから続行します。 これが完了するまでには 1 から 2 分ほどかかることがあります。

## <a name="get-cognitive-search-query-key-in-visual-studio-code"></a>Visual Studio Code で Cognitive Search クエリ キーを取得する

1. Visual Studio Code で[アクティビティ バー](https://code.visualstudio.com/docs/getstarted/userinterface)を開き、Azure アイコンを選択します。 

1. サイド バーで、 **[Azure] の [Cognitive Search]** 領域にある自分の Azure サブスクリプションを選択し、Search リソースを右クリックして **[Copy Query Key]\(クエリ キーのコピー\)** を選択します。 

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-copy-query-key.png" alt-text="サイド バーで、**[Azure] の [Cognitive Search]** 領域にある自分の Azure サブスクリプションを選択し、Search リソースを右クリックして **[Copy Query Key]\(クエリ キーのコピー\)** を選択します。":::

1. このクエリ キーは保存してください。これは次のセクションで使用する必要があります。 クエリ キーを使用してインデックスに対してクエリを実行できます。 

## <a name="add-configuration-settings-in-visual-studio-code"></a>Visual Studio Code で構成設定を追加する

Azure 関数アプリは、Search シークレットが設定されるまで検索データを返しません。 

1. アクティビティ バーから **[Azure]** を選択し、サイド バーから **[Static Web Apps]** を選択します。 
1. **アプリケーション設定** が表示されるまで、新しい静的 Web アプリを展開します。
1. **[アプリケーションの設定]** を右クリックしてから、 **[新しい設定の追加]** を選択します。

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-static-web-app-configure-settings.png" alt-text="**[アプリケーションの設定]** を右クリックしてから、**[新しい設定の追加]** を選択します。":::

1. 以下の設定を追加します。

    |設定|Search リソースの値|
    |--|--|
    |SearchApiKey|実際の Search クエリ キー|
    |SearchServiceName|実際の Search リソース名|
    |SearchIndexName|`good-books`|
    |SearchFacets|`authors*,language_code`|

## <a name="use-search-in-your-static-web-app"></a>静的 Web アプリで検索を使用する

1. Visual Studio Code で[アクティビティ バー](https://code.visualstudio.com/docs/getstarted/userinterface)を開き、Azure アイコンを選択します。
1. サイド バーで、`Static web apps` 領域の下にある **自分の Azure サブスクリプションを右クリック** し、このチュートリアル用に作成した静的 Web アプリを見つけます。
1. 静的 Web アプリの名前を右クリックし、 **[Browse site]\(サイトの参照\)** を選択します。
    
    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-browse-static-web-app.png" alt-text="静的 Web アプリの名前を右クリックし、**[Browse site]\(サイトの参照\)** を選択します。":::    

1. ポップアップ ダイアログで **[開く]** を選択します。
1. Web サイトの検索バーに、`code` のような検索クエリを、提案機能によって書籍のタイトルが提案されるように "_ゆっくりと_" 入力します。 提案されたものを選択するか、引き続き自分のクエリを入力します。 検索クエリを入力し終えたら、Enter キーを押します。 
1. 結果を確認してから、いずれかの書籍を選択して詳細を表示します。 

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで作成したリソースをクリーンアップするには、リソース グループを削除します。

1. Visual Studio Code で[アクティビティ バー](https://code.visualstudio.com/docs/getstarted/userinterface)を開き、Azure アイコンを選択します。 

1. サイド バーで、`Resource Groups` 領域の下にある **自分の Azure サブスクリプションを右クリック** し、このチュートリアル用に作成したリソース グループを見つけます。
1. リソース グループの名前を右クリックしてから、 **[削除]** を選択します。
    これにより、Search リソースと静的 Web アプリ リソースの両方が削除されます。
1. サンプルの GitHub フォークがもう必要ない場合は、忘れずに GitHub 上でそれを削除してください。 自分のフォークの **[設定]** に異動して、フォークを削除します。 


## <a name="next-steps"></a>次のステップ

* [検索が有効な Web サイトの Search 統合について理解する](tutorial-javascript-search-query-integration.md)
