---
title: 'JavaScript チュートリアル: Search 統合の概要'
titleSuffix: Azure Cognitive Search
description: Web サイトへの検索の追加と Azure 静的 Web アプリへのデプロイについての技術的概要とセットアップ。
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: 03192b8a84b78682b53bf3d47e7de7b65eb8bceb
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104723506"
---
# <a name="1---overview-of-adding-search-to-a-website"></a>1 - Web サイトへの検索の追加の概要

このチュートリアルでは、書籍のカタログを検索する Web サイトを構築し、その Web サイトを Azure 静的 Web アプリにデプロイします。 

アプリケーションは利用可能です。 
* [サンプル](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website)
* [デモ Web サイト - aka.ms/azs-good-books](https://aka.ms/azs-good-books)

## <a name="what-does-the-sample-do"></a>サンプルの動作 

このサンプル Web サイトでは、10,000 冊の書籍のカタログにアクセスできます。 ユーザーは、検索バーにテキストを入力してカタログを検索できます。 ユーザーがテキストを入力しているとき、Web サイトによって検索インデックスの提案機能が使用され、テキストが補完されます。 クエリが完了すると、書籍の一覧が詳細の一部と共に表示されます。 ユーザーは、書籍を選択して、書籍の検索インデックスに格納されているすべての詳細を表示できます。 

:::image type="content" source="./media/tutorial-javascript-overview/cognitive-search-enabled-book-website.png" alt-text="このサンプル Web サイトでは、10,000 冊の書籍のカタログにアクセスできます。ユーザーは、検索バーにテキストを入力してカタログを検索できます。ユーザーがテキストを入力しているとき、Web サイトによって検索インデックスの提案機能が使用され、テキストが補完されます。検索が完了すると、書籍の一覧が詳細の一部と共に表示されます。ユーザーは、書籍を選択して、書籍の検索インデックスに格納されているすべての詳細を表示できます。":::

検索エクスペリエンスには次が含まれます。 

* 検索 - アプリケーション用の検索機能を提供します。
* 提案 - ユーザーが検索バーに入力している間、提案を示します。
* ドキュメント検索 - ID でドキュメントを検索し、詳細ページ用としてコンテンツをすべて取得します。

## <a name="how-is-the-sample-organized"></a>サンプルの構成

[サンプル](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website)には、次が含まれています。

|アプリ|目的|GitHub<br>リポジトリ<br>場所|
|--|--|--|
|Client|検索で書籍を表示するための React アプリ (プレゼンテーション層)。 Azure 関数アプリを呼び出します。 |[/search-website/src](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/src)|
|サーバー|Azure 関数アプリ (ビジネス層)。JavaScript SDK を使用して Azure Cognitive Search API を呼び出します。 |[/search-website/api](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/src)|
|一括挿入|インデックスを作成し、それにドキュメントを追加するための JavaScript ファイル。|[/search-website/bulk-insert](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/bulk-insert)|

## <a name="set-up-your-development-environment"></a>開発環境を設定する

ローカル開発環境に次をインストールします。 

- [Node.js 12 または 14](https://nodejs.org/en/download)
    - 自分のローカル コンピューターに別のバージョンの Node.js がインストールされている場合は、[Node Version Manager](https://github.com/nvm-sh/nvm) (nvm) または Docker コンテナーの使用を検討してください。  
- [Git](https://git-scm.com/downloads)
- [Visual Studio Code](https://code.visualstudio.com/) と次の拡張機能
    - [[Azure リソース]](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureresourcegroups)
    - [Azure Cognitive Search 0.2.0+](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch)
    - [Azure Static Web Apps](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps) 
- 省略可能:
    - このチュートリアルでは Azure 関数 API をローカルで実行しませんが、ローカルで実行する場合は、次の bash コマンドを使用して [azure-functions-core-tools](/azure/azure-functions/functions-run-local?tabs=linux%2Ccsharp%2Cbash) をグローバルにインストールする必要があります。 
    
    ```bash
    npm install -g azure-functions-core-tools
    ```

## <a name="fork-and-clone-the-search-sample-with-git"></a>Git を使用して検索サンプルをフォークし、クローンする

サンプル リポジトリのフォークは、静的 Web アプリをデプロイできるようにするために重要です。 Web アプリでは、独自の GitHub フォークの場所に基づいて、ビルド アクションとデプロイ コンテンツを決定します。 静的 Web アプリでのコードの実行はリモートであり、フォークされたサンプルのコードから、Azure Static Web Apps によって読み取りが行われます。

1. GitHub で、[サンプル リポジトリ](https://github.com/Azure-Samples/azure-search-javascript-samples)をフォークします。 

    自分の GitHub アカウントを使用して、Web ブラウザーでフォークのプロセスを完了します。 このチュートリアルでは、Azure 静的 Web アプリへのデプロイの一部としてお客様のフォークを使用します。 

1. bash ターミナルで、サンプル アプリケーションをローカル コンピューターにダウンロードします。 

    `YOUR-GITHUB-ALIAS` を自分の GitHub の別名に置き換えます。 

    ```bash
    git clone https://github.com/YOUR-GITHUB-ALIAS/azure-search-javascript-samples
    ```

1. Visual Studio Code で、クローンされたリポジトリのローカル フォルダーを開きます。 残りのタスクは、指定されていない限り、Visual Studio Code から実行されます。

## <a name="create-a-resource-group-for-your-azure-resources"></a>Azure リソース用のリソース グループを作成する

1. Visual Studio Code で[アクティビティ バー](https://code.visualstudio.com/docs/getstarted/userinterface)を開き、Azure アイコンを選択します。 
1. サイド バーで、`Resource Groups` 領域の下にある **自分の Azure サブスクリプションを右クリック** し、 **[リソース グループの作成]** を選択します。

    :::image type="content" source="./media/tutorial-javascript-overview/visual-studio-code-create-resource-group.png" alt-text="サイド バーで、[リソース グループ] 領域の下にある**自分の Azure サブスクリプションを右クリック**し、**[リソース グループの作成]** を選択します。":::
1. リソース グループ名 (`cognitive-search-website-tutorial` など) を入力します。 
1. 近くの場所を選択します。
1. Cognitive Search と静的 Web アプリのリソースを作成する場合は、後ほどこのチュートリアルの中で、このリソース グループを使用します。 

    リソース グループを作成すると、リソースを管理 (使用完了時の削除など) するための論理ユニットが提供されます。

## <a name="next-steps"></a>次のステップ

* [検索インデックスを作成してドキュメントと共に読み込む](tutorial-javascript-create-load-index.md)
* [静的 Web アプリをデプロイする](tutorial-javascript-deploy-static-web-app.md)
