---
title: '.NET チュートリアル: 検索統合の概要'
titleSuffix: Azure Cognitive Search
description: .NET による Web サイトへの検索の追加と Azure 静的 Web アプリへのデプロイについての技術的概要とセットアップ。
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 04/23/2021
ms.custom: devx-track-csharp
ms.devlang: dotnet
ms.openlocfilehash: 49919a5a0efbdae46f2ef82a7129fee8aa686e8e
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111964039"
---
# <a name="1---overview-of-adding-search-to-a-website-with-net"></a>1 - .NET による Web サイトへの検索の追加の概要

このチュートリアルでは、書籍のカタログを検索する Web サイトを構築し、その Web サイトを Azure 静的 Web アプリにデプロイします。 

アプリケーションは利用可能です。 
* [サンプル](https://github.com/azure-samples/azure-search-dotnet-samples/tree/master/search-website)
* [デモ Web サイト - aka.ms/azs-good-books](https://aka.ms/azs-good-books)

## <a name="what-does-the-sample-do"></a>サンプルの動作 

このサンプル Web サイトでは、10,000 冊の書籍のカタログにアクセスできます。 ユーザーは、検索バーにテキストを入力してカタログを検索できます。 ユーザーがテキストを入力しているとき、Web サイトによって検索インデックスの提案機能が使用され、テキストが補完されます。 クエリが完了すると、書籍の一覧が詳細の一部と共に表示されます。 ユーザーは、書籍を選択して、書籍の検索インデックスに格納されているすべての詳細を表示できます。 

:::image type="content" source="./media/tutorial-javascript-overview/cognitive-search-enabled-book-website.png" alt-text="このサンプル Web サイトでは、10,000 冊の書籍のカタログにアクセスできます。ユーザーは、検索バーにテキストを入力してカタログを検索できます。ユーザーがテキストを入力しているとき、Web サイトによって検索インデックスの提案機能が使用され、テキストが補完されます。検索が完了すると、書籍の一覧が詳細の一部と共に表示されます。ユーザーは、書籍を選択して、書籍の検索インデックスに格納されているすべての詳細を表示できます。":::

検索エクスペリエンスには次が含まれます。 

* 検索 - アプリケーション用の検索機能を提供します。
* 提案 - ユーザーが検索バーに入力している間、提案を示します。
* ドキュメント検索 - ID でドキュメントを検索し、詳細ページ用としてコンテンツをすべて取得します。

## <a name="how-is-the-sample-organized"></a>サンプルの構成

[サンプル](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/search-website)には、次が含まれています。

|アプリ|目的|GitHub<br>リポジトリ<br>場所|
|--|--|--|
|Client|検索で書籍を表示するための React アプリ (プレゼンテーション層)。 Azure 関数アプリを呼び出します。 |[/search-website/src](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/search-website/src)|
|サーバー|Azure .NET 関数アプリ (ビジネス層)。.NET SDK を使用して Azure Cognitive Search API を呼び出します |[/search-website/api](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/search-website/api)|
|一括挿入|インデックスを作成し、それにドキュメントを追加するための .NET ファイル。|[/search-website/bulk-insert](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/search-website/bulk-insert)|

## <a name="set-up-your-development-environment"></a>開発環境を設定する

ローカル開発環境に次をインストールします。 

- [.NET 3](https://dotnet.microsoft.com/download/dotnet/5.0)  
- [Git](https://git-scm.com/downloads)
- [Visual Studio Code](https://code.visualstudio.com/) と次の拡張機能
    - [[Azure リソース]](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureresourcegroups)
    - [Azure Cognitive Search 0.2.0+](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch)
    - [Azure Static Web Apps](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps) 
- 省略可能:
    - このチュートリアルでは Azure 関数 API をローカルで実行しませんが、ローカルで実行する場合は、[azure-functions-core-tools](../azure-functions/functions-run-local.md?tabs=linux%2ccsharp%2cbash#install-the-azure-functions-core-tools) をインストールする必要があります。

## <a name="fork-and-clone-the-search-sample-with-git"></a>Git を使用して検索サンプルをフォークし、クローンする

サンプル リポジトリのフォークは、静的 Web アプリをデプロイできるようにするために重要です。 Web アプリでは、独自の GitHub フォークの場所に基づいて、ビルド アクションとデプロイ コンテンツを決定します。 静的 Web アプリでのコードの実行はリモートであり、フォークされたサンプルのコードから、Azure Static Web Apps によって読み取りが行われます。

1. GitHub で、[サンプル リポジトリ](https://github.com/Azure-Samples/azure-search-dotnet-samples)をフォークします。 

    自分の GitHub アカウントを使用して、Web ブラウザーでフォークのプロセスを完了します。 このチュートリアルでは、Azure 静的 Web アプリへのデプロイの一部としてお客様のフォークを使用します。 

1. bash ターミナルで、サンプル アプリケーションをローカル コンピューターにダウンロードします。 

    `YOUR-GITHUB-ALIAS` を自分の GitHub の別名に置き換えます。 

    ```bash
    git clone https://github.com/YOUR-GITHUB-ALIAS/azure-search-dotnet-samples
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

* [検索インデックスを作成してドキュメントと共に読み込む](tutorial-csharp-create-load-index.md)
* [静的 Web アプリをデプロイする](tutorial-csharp-deploy-static-web-app.md)