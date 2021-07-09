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
ms.openlocfilehash: 021f7aeda86f5ff9f11eb2991a74c6ad37a203e6
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110469785"
---
# <a name="3---deploy-the-search-enabled-website"></a>3 - 検索が有効な Web サイトをデプロイする

検索が有効な Web サイトを Azure 静的 Web アプリとしてデプロイします。 このデプロイには、React アプリと関数アプリの両方が含まれています。  

静的 Web アプリでは、サンプル リポジトリのフォークを使用して、GitHub からデプロイのための情報とファイルをプルします。  

## <a name="create-a-static-web-app-in-visual-studio-code"></a>Visual Studio Code で静的 Web アプリを作成する

1. アクティビティ バーから **[Azure]** を選択し、サイド バーから **[Static Web Apps]** を選択します。 

1. どのブランチからデプロイするかを確認するポップアップ ウィンドウが VS Code で表示された場合は、既定のブランチ （通常は **master** または **main**） を選択します。 

    この設定により、そのブランチにコミットした変更だけが静的 Web アプリにデプロイされます。 

1. ポップアップ ウィンドウが表示されて変更をコミットするよう求められた場合は、これをしないでください。 一括インポート手順で作成されたシークレットは、リポジトリにコミットしないでください。 

    変更をロールバックするには、VS Code のアクティビティ バーで [ソース管理] アイコンを選択してから、変更された各ファイルを [変更] の一覧で選択して、 **[変更の破棄]** アイコンを選択します。

1. サブスクリプション名を右クリックし、 **[Create Static Web App (Advanced)]\(静的 Web アプリの作成 (詳細設定)\)** を選択します。    

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-create-static-web-app-resource-advanced.png" alt-text="サブスクリプション名を右クリックし、**[Create Static Web App (Advanced)]\(静的 Web アプリの作成 (詳細設定)\)** を選択します。":::

1. プロンプトに従って、次の情報を指定します。

    |Prompt|Enter|
    |--|--|
    |新しい静的 Web アプリの名前の入力。|自分のリソースに一意の名前を作成します。 たとえば `joansmith-azure-search-javascript-samples` のように、リポジトリ名の先頭に自分の名前を追加できます。 |
    |新しいリソース用のリソース グループの選択。|このチュートリアル用に作成したリソース グループを使用します。|
    |SKU の選択| このチュートリアル用に無料の SKU を選択します。|
    |既定のプロジェクト構造を構成するためのビルド プリセットの選択。|[**カスタム**] を選択します。|
    |アプリケーション コードの場所の選択|`search-website`<br><br>これは、リポジトリのルートから Azure 静的 Web アプリまでのパスです。 |
    |Azure 関数コードの場所の選択|`search-website/api`<br><br>これは、リポジトリのルートから Azure 関数アプリまでのパスです。 |
    |ビルド出力のパスの入力|`build`<br><br>これは、Azure 静的 Web アプリから、生成されたファイルまでのパスです。|
    |新しいリソースの場所の選択。|近くのリージョンを選択します。|

1. リソースが作成されたら、通知から **[Open Actions in GitHub]\(GitHub でアクションを開く\)** を選択します。 これにより、自分がフォークしたリポジトリを参照するブラウザー ウィンドウが開きます。 

    アクションの一覧は、自分の Web アプリ (クライアントと関数の両方) が Azure 静的 Web アプリに正常にプッシュされたことを示しています。 

    ビルドとデプロイが完了するまで待ってから続行します。 これが完了するまでには 1 から 2 分ほどかかることがあります。

## <a name="get-cognitive-search-query-key-in-visual-studio-code"></a>Visual Studio Code で Cognitive Search クエリ キーを取得する

1. Visual Studio Code で[アクティビティ バー](https://code.visualstudio.com/docs/getstarted/userinterface)を開き、Azure アイコンを選択します。 

1. サイド バーで、 **[Azure] の [Cognitive Search]** 領域にある自分の Azure サブスクリプションを選択し、Search リソースを右クリックして **[Copy Query Key]\(クエリ キーのコピー\)** を選択します。 

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-copy-query-key.png" alt-text="サイド バーで、**[Azure] の [Cognitive Search]** 領域にある自分の Azure サブスクリプションを選択し、Search リソースを右クリックして **[Copy Query Key]\(クエリ キーのコピー\)** を選択します。":::

1. このクエリ キーは保存してください。これは次のセクションで使用する必要があります。 クエリ キーを使用してインデックスに対してクエリを実行できます。 

## <a name="add-configuration-settings-in-azure-portal"></a>Azure portal で構成設定を追加する

Azure 関数アプリは、Search シークレットが設定されるまで検索データを返しません。 

1. アクティビティ バーで **[Azure]** を選択します。 
1. 静的 Web アプリ リソースを右クリックし、 **[ポータルで開く]** を選択します。

    :::image type="content" source="media/tutorial-javascript-static-web-app/open-static-web-app-in-azure-portal.png" alt-text="JavaScript の静的 Web アプリ リソースを右クリックし、[ポータルで開く] を選択する。":::

1. **[構成]** 、 **[+ 追加]** の順に選択します。

    :::image type="content" source="media/tutorial-javascript-static-web-app/add-new-application-setting-to-static-web-app-in-portal.png" alt-text="[構成] を選択し、JavaScript アプリの [追加] を選択する。":::

1. 次の各設定を追加します。

    |設定|Search リソースの値|
    |--|--|
    |SearchApiKey|実際の Search クエリ キー|
    |SearchServiceName|実際の Search リソース名|
    |SearchIndexName|`good-books`|
    |SearchFacets|`authors*,language_code`|

    Azure Cognitive Search では、コレクションのフィルター処理に、文字列の場合とは異なる構文が必要です。 フィールドが `Collection(Edm.String)` 型であることを示すために、フィールド名の後に `*` を追加します。 これにより、Azure 関数はクエリにフィルターを正しく追加できます。

1. **[保存]** を選択して設定を保存します。 

    :::image type="content" source="media/tutorial-javascript-static-web-app/save-new-application-setting-to-static-web-app-in-portal.png" alt-text="[保存] を選択して設定を保存する。":::

1. VS Code に戻ります。 
1. 静的 Web アプリを最新の状態に更新して、静的 Web アプリのアプリケーション設定を確認します。 

    :::image type="content" source="media/tutorial-javascript-static-web-app/visual-studio-code-extension-fresh-resource.png" alt-text="静的 Web アプリを最新の状態に更新して、静的 Web アプリのアプリケーション設定を確認する。":::

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
