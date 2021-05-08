---
title: 'クイックスタート: Language Understanding (LUIS) SDK クライアント ライブラリと REST API'
description: LUIS SDK クライアント ライブラリと REST API を使用して LUIS アプリを作成し、クエリを実行します。
ms.topic: quickstart
ms.date: 03/29/2021
ms.service: cognitive-services
ms.author: aahi
manager: nitinme
ms.subservice: language-understanding
author: aahill
keywords: Azure, 人工知能, AI, 自然言語処理, NLP, LUIS, Azure LUIS, 自然言語理解, AI チャットボット, チャットボット メーカー, 自然言語の理解
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-luis
ms.openlocfilehash: ca45266ce4b8ca784c3d54aafb80a66efaf2a1da
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278917"
---
# <a name="quickstart-language-understanding-luis-client-libraries-and-rest-api"></a>クイックスタート: Language Understanding (LUIS) クライアント ライブラリと REST API

C#、Python、または JavaScript を使用したこのクイックスタートでは、LUIS SDK クライアント ライブラリを使用して Azure LUIS 人工知能 (AI) アプリを作成し、クエリを実行します。 また、cURL を使用して、REST API で要求を送信することもできます。

Language Understanding (LUIS) を使うと、自然言語処理 (NLP) をユーザーの自然言語での会話に適用して、全体の意味を予測し、関連性のある詳細な情報を引き出すことができます。

* **作成** クライアント ライブラリと REST API を使用すると、LUIS アプリを作成、編集、トレーニング、および公開できます。
* **予測ランタイム** クライアント ライブラリと REST API を使用すると、公開されているアプリに対してクエリを実行できます。

::: zone pivot="programming-language-csharp"
[!INCLUDE [LUIS development with C# SDK](./includes/sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [LUIS development with Node.js SDK](./includes/sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [LUIS development with Python SDK](./includes/sdk-python.md)]
::: zone-end

::: zone pivot="rest-api"
[!INCLUDE [LUIS development with REST API](./includes/rest-api.md)]
::: zone-end

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[LUIS ポータル](https://www.luis.ai)からアプリを削除し、[Azure portal](https://portal.azure.com/) から Azure リソースを削除することができます。

REST API を使用している場合は、このクイック スタートが完了したら、ファイル システムから `ExampleUtterances.JSON` ファイルを削除してください。

## <a name="troubleshooting"></a>トラブルシューティング

* クライアント ライブラリへの認証 - 認証エラーは通常、間違ったキーとエンドポイントが使用されたことを示します。 このクイックスタートでは、便宜上、予測ランタイムの作成キーとエンドポイントを使用しますが、これは月間クォータをまだ使っていない場合にのみ機能します。 作成キーとエンドポイントを使用できない場合は、予測ランタイム SDK クライアント ライブラリにアクセスするときに予測ランタイム キーとエンドポイントを使用する必要があります。
* エンティティの作成 - このチュートリアルで使用されている入れ子になった機械学習エンティティの作成でエラーが発生した場合は、コードをコピーした後で別のエンティティを作成するようにコードを変更していないことを確認してください。
* 発話の例の作成 - このチュートリアルで使用されているラベル付きの発話の例の作成でエラーが発生した場合は、コードをコピーした後で別のラベル付きの例を作成するようにコードを変更していないことを確認してください。
* トレーニング - トレーニング エラーが発生した場合、これは通常、(発話の例が含まれる意図がない) 空のアプリ、または無効な形式の意図またはエンティティが含まれるアプリを示します。
* その他のエラー - コードではテキストおよび JSON オブジェクトを使用してクライアント ライブラリを呼び出すため、コードを変更していないことを確認してください。

これ以外のエラー - 上記の一覧に記載されていないエラーが発生した場合は、このページの下部でフィードバックを送信してお知らせください。 インストールしたクライアント ライブラリのプログラミング言語とバージョンを含めます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [LUIS の反復的アプリ開発](./luis-concept-app-iteration.md)