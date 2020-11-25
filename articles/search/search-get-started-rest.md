---
title: 'クイックスタート: REST API を使用して検索インデックスを作成する'
titleSuffix: Azure Cognitive Search
description: この REST API クイックスタートでは、Postman または Visual Studio Code を使用して Azure Cognitive Search REST API を呼び出す方法について説明します。
zone_pivot_groups: URL-test-interface-rest-apis
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 11/17/2020
ms.openlocfilehash: 971ee806d8b5f9a336b40d96d500ce47d2f5166e
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94711401"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-using-rest-apis"></a>クイックスタート: REST API を使用して Azure Cognitive Search インデックスを作成する

この記事では、[Azure Cognitive Search REST API シリーズ](/rest/api/searchservice)と API クライアントを使用してREST API 要求を対話的に作成し、要求を送受信する方法について説明します。 API クライアントと以下の手順を利用すると、コードを記述することなく要求を送信して応答を確認できます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

::: zone pivot="url-test-tool-rest-postman"

[!INCLUDE [Send requests using Postman](includes/search-get-started-rest-postman.md)]

::: zone-end

::: zone pivot="url-test-tool-rest-vscode-ext"

[!INCLUDE [Send requests using Visual Studio Code](includes/search-get-started-rest-vscode-ext.md)]

::: zone-end

## <a name="clean-up-resources"></a>リソースをクリーンアップする

独自のサブスクリプションを使用している場合は、プロジェクトの最後に、作成したリソースがまだ必要かどうかを確認してください。 リソースを実行したままにすると、お金がかかる場合があります。 リソースは個別に削除することも、リソース グループを削除してリソースのセット全体を削除することもできます。

ポータルの左側のナビゲーション ウィンドウにある **[すべてのリソース]** または **[リソース グループ]** リンクを使って、リソースを検索および管理できます。

無料サービスを使っている場合は、3 つのインデックス、インデクサー、およびデータソースに制限されることに注意してください。 ポータルで個別の項目を削除して、制限を超えないようにすることができます。 

## <a name="next-steps"></a>次のステップ

主なタスクの実行方法は以上です。その他の REST API 呼び出しに進んで、インデックス作成処理にコンテンツ変換を追加する[エンリッチメント パイプラインの設定](cognitive-search-tutorial-blob.md)やインデクサーなど、より高度な機能について見てみましょう。 次のステップについては、以下のリンクをお勧めします。

> [!div class="nextstepaction"]
> [チュートリアル: REST と AI を使用して Azure Blob から検索可能なコンテンツを生成する](cognitive-search-tutorial-blob.md)