---
title: カスタム エンティティで Cognitive Search のインデックスをエンリッチする
titleSuffix: Azure Cognitive Services
description: カスタム固有表現認識 (NER) を使って Cognitive Search のインデックスを向上させます
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: tutorial
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 661716e57ee3a4f73082db593f3dfd96a6f7cedc
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132704982"
---
# <a name="tutorial-enrich-a-cognitive-search-index-with-custom-entities-from-your-data"></a>チュートリアル: データからのカスタム エンティティで Cognitive Search のインデックスをエンリッチする

企業に電子ドキュメントが大量にあると、その検索は時間とコストのかかるタスクになる可能性があります。 [Azure Cognitive Search](../../../../search/search-create-service-portal.md) は、インデックスに基づくファイルの検索に役立ちます。 カスタム NER は、ファイルから関連エンティティを抽出し、これらのファイルのインデックス作成プロセスをエンリッチするのに役立ちます。

このチュートリアルでは、以下の内容を学習します。

* カスタム固有表現認識プロジェクトを作成します。
* Azure 関数を発行します。
* Azure Cognitive Search にインデックスを追加します。

## <a name="prerequisites"></a>前提条件

* [Azure Blob Storage アカウントに接続された Azure 言語リソース](../how-to/create-project.md)。
    * 容易にセットアップするため、Azure portal を使ったリソース作成手順に従うことをお勧めします。 
* 現在のサブスクリプション内の [Azure Cognitive Search サービス](../../../../search/search-create-service-portal.md)
    * このサービスには、任意のレベルと任意のリージョンを使うことができます。
* [Azure 関数アプリ](../../../../azure-functions/functions-create-function-app-portal.md)
* この[サンプル データ](https://go.microsoft.com/fwlink/?linkid=2175226)をダウンロードします。

## <a name="create-a-custom-ner-project-through-language-studio"></a>Language Studio でカスタム NER プロジェクトを作る

1. [Language Studio ポータル](https://aka.ms/LanguageStudio)からログインして、 **[Custom entity extraction]\(カスタム エンティティ抽出\)** を選びます。

2. 言語リソースを選びます。 リソースとストレージ アカウントのロールと [ID 管理が有効になっている](../how-to/create-project.md#enable-identity-management-for-your-resource)ことを確かめます。

3. プロジェクト画面の上部にある **[新しいプロジェクトの作成]** を選びます。 求められた場合は、表示されるメニューからストレージ アカウントを選びます。

    :::image type="content" source="../media/create-project.png" alt-text="プロジェクト作成ページのスクリーンショット。" lightbox="../media/create-project.png":::

4. プロジェクトの情報を入力します。

    | Key | 説明 |
    |--|--|
    | 名前 | プロジェクトの名前。 作成した後でプロジェクトの名前を変えることはできません。 |
    | 説明 | プロジェクトの説明 |
    | Language | プロジェクト内のファイルの言語。|

    > [!NOTE]
    > ドキュメントで複数の言語が使われている場合は、プロジェクトの作成で **[複数言語]** オプションを選び、 **[言語]** オプションをドキュメントの主要な言語に設定します。

5. このチュートリアルでは、**既存のタグ ファイル** を使い、サンプル データからダウンロードしたタグ ファイルを選びます。

## <a name="train-your-model"></a>モデルをトレーニングする

1. 左側のメニューから **[トレーニング]** を選びます。

2. 新しいモデルをトレーニングするには、 **[Train a new model]\(新しいモデルをトレーニングする\)** を選び、下のテキスト ボックスにモデル名を入力します。

    :::image type="content" source="../media/train-model.png" alt-text="新しいモデルを作成します" lightbox="../media/train-model.png":::

3. ページの下部にある **[トレーニング]** ボタンを選びます。

4. トレーニングが完了したら、[モデルの評価の詳細を確認](../how-to/view-model-evaluation.md)して、[モデルを改善する](../how-to/improve-model.md)ことができます

## <a name="deploy-your-model"></a>モデルをデプロイする

1. 左側のメニューから **[Deploy model]\(モデルのデプロイ\)** を選びます。

2. デプロイするモデルを選び、上部のメニューで **[Deploy model]\(モデルのデプロイ\)** をクリックします。 トレーニングが正常に完了したモデルのみを表示できます。

## <a name="prepare-your-secrets-for-the-azure-function"></a>Azure 関数用にシークレットを準備する

次に、Azure 関数用にシークレットを準備する必要があります。 プロジェクトのシークレットは次のとおりです。 
* エンドポイント
* リソース キー
* モデル ID

### <a name="get-your-custom-ner-project-secrets"></a>カスタム NER プロジェクトのシークレットを取得する

* **プロジェクト名** が必要です。プロジェクト名は大文字と小文字の区別があります。

* デプロイ スロットも必要です。 
   * Language Studio でモデルをデプロイした場合、デプロイ スロットは既定で `prod` になります。 
   * API を使ってプログラムでモデルをデプロイした場合、これは要求で割り当てたデプロイ名です。

### <a name="get-your-resource-keys-endpoint"></a>リソース キー エンドポイントを取得する

1. [Azure portal](https://ms.portal.azure.com/#home) でリソースに移動します。

2. 左側のメニューで **[キーとエンドポイント]** を選びます。 API 要求にはエンドポイントとキーの 1 つが必要です。

    :::image type="content" source="../../media/azure-portal-resource-credentials.png" alt-text="Azure portal のキーとエンドポイントの画面を示すスクリーンショット" lightbox="../../media/azure-portal-resource-credentials.png":::
   
## <a name="edit-and-deploy-your-azure-function"></a>Azure 関数を編集してデプロイする

1. [提供されているサンプル関数](https://aka.ms/ct-cognitive-search-integration-tool)をダウンロードして使います。

2. サンプル関数をダウンロードした後、*program.cs* ファイルを開き、アプリのシークレットを入力します。

3. [関数を Azure に発行します](../../../../azure-functions/functions-develop-vs.md?tabs=in-process#publish-to-azure)。

## <a name="use-the-integration-tool"></a>統合ツールを使用する

次のセクションでは、[Cognitive Search 統合ツール](https://aka.ms/ct-cognitive-search-integration-tool)を使って、プロジェクトと Azure Cognitive Search を統合します。 このリポジトリをダウンロードします。 

### <a name="prepare-configuration-file"></a>構成ファイルを準備する

1. ダウンロードしたフォルダーで、[サンプル構成ファイル](https://github.com/microsoft/CognitiveServicesLanguageUtilities/blob/dev/CustomTextAnalytics.CognitiveSearch/Samples/configs.json)を見つけます。 それをテキスト エディターで開きます。 

2. 次のようにして、ストレージ アカウントの接続文字列を取得します。
    1. [Azure portal](https://ms.portal.azure.com/#home) でストレージ アカウントの概要ページに移動します。
    2. 画面の上部セクションにあるコンテナー名を、構成ファイルの `blobStorage` の下にある `containerName` フィールドにコピーします。  
    3. 画面左側のメニューにある **[アクセス キー]** セクションの **[接続文字列]** を、構成ファイルの `blobStorage` の下にある `connectionString` フィールドにコピーします。

1. 次のようにして、Cognitive Search のエンドポイントとキーを取得します。
    1. [Azure portal](https://ms.portal.azure.com/#home) でリソースの概要ページに移動します。
    2. ページの右上のセクションにある **[Url]** を、`cognitiveSearch` 内の `endpointUrl` フィールドにコピーします。
    3. 画面の左側にあるメニューの **[キー]** セクションに移動します。 **[プライマリ管理者キー]** を `cognitiveSearch` 内の `apiKey` フィールドにコピーします。

3. Azure 関数のエンドポイントとキーを取得する
    
    1. Azure 関数のエンドポイントとキーを取得するには、[Azure portal](https://ms.portal.azure.com/#home) で関数の概要ページに移動します。
    2. 画面の左側にある **[関数]** メニューに移動し、作成した関数をクリックします。
    3. 上部のメニューで、 **[関数の URL の取得]** をクリックします。 URL は、`YOUR-ENDPOINT-URL?code=YOUR-API-KEY` のように書式設定されています。 
    4. `YOUR-ENDPOINT-URL` を構成ファイルの `azureFunction` の下の `endpointUrl` フィールドにコピーします。 
    5. `YOUR-API-KEY` を構成ファイルの `azureFunction` の下の `apiKey` フィールドにコピーします。 

### <a name="prepare-schema-file"></a>スキーマ ファイルを準備する

前にダウンロードしたフォルダーで、[サンプル スキーマ ファイル](https://github.com/microsoft/CognitiveServicesLanguageUtilities/blob/dev/CustomTextAnalytics.CognitiveSearch/Samples/app-schema.json)を見つけます。 それをテキスト エディターで開きます。 

`entityNames` 配列内のエントリは、プロジェクトの作成時に割り当てたエンティティ名になります。 それらをプロジェクトからコピーして、[Language Studio](https://aka.ms/custom-extraction) に貼り付けます。または、 

### <a name="run-the-index-command"></a>`Index` コマンドを実行します

構成ファイルとスキーマ ファイルが完成したら、プロジェクトのインデックスを作成できます。 構成ファイルを CLI ツールと同じパスに置き、次のコマンドを実行します。

```cli
    indexer index --schema <path/to/your/schema> --index-name <name-your-index-here>
```

`name-your-index-here` を、お使いの Cognitive Search インスタンスに表示されるインデックス名に置き換えます。

## <a name="next-steps"></a>次のステップ

* [Cognitive Search SDK を使用してアプリを検索する](../../../../search/search-howto-dotnet-sdk.md#run-queries)