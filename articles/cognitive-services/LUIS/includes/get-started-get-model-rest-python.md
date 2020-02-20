---
title: C# で REST 呼び出しを使用してモデルを取得する
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/14/2020
ms.author: diberry
ms.openlocfilehash: 4d8da7d2bc51c4fc4ebc8d71f230f24f20b3aa24
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368407"
---
## <a name="prerequisites"></a>前提条件

* Azure Language Understanding (作成リソースの 32 文字のキーおよび作成エンドポイントの URL)。 [Azure portal](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) または [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli) で作成します。
* cognitive-services-language-understanding GitHub リポジトリから [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) アプリをインポートします。
* インポートした TravelAgent アプリ用の LUIS アプリケーション ID。 アプリケーション ID は、アプリケーション ダッシュボードに表示されます。
* 発話を受け取るアプリケーション内のバージョン ID。 既定の ID は "0.1" です。
* [Python 3.6](https://www.python.org/downloads/) 以降。
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>発話の例の JSON ファイル

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>プログラムを使用してモデルを変更する

1. `model.py` という名前で新しいファイルを作成します。 次のコードを追加します。

    [!code-python[Add example utterances to Language Understanding in python](~/samples-luis/documentation-samples/quickstarts/change-model/python/3.x/add-utterances-3-6.py)]

1. `YOUR-` で始まる値を実際の値に置き換えます。

    |Information|目的|
    |--|--|
    |`YOUR-KEY`|32 文字の実際の作成キー。|
    |`YOUR-ENDPOINT`| 作成 URL エンドポイント。 たとえば、「 `replace-with-your-resource-name.api.cognitive.microsoft.com` 」のように入力します。 リソース名は、リソースの作成時に設定します。|
    |`YOUR-APP-ID`| LUIS アプリ ID。 |

    割り当てられたキーとリソースは、LUIS ポータルの [Manage]\(管理\) セクションの **[Azure リソース]** ページで確認できます。 アプリ ID は、同じ [Manage]\(管理\) セクションの **[アプリケーションの設定]** ページで入手できます。

1. このファイルを作成したときと同じディレクトリからコマンド プロンプトで次のコマンドを入力し、ファイルを実行します。

    ```console
    python model.py
    ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートを使用して完了したときに、ファイル システムからファイルを削除します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [アプリのベスト プラクティス](../luis-concept-best-practices.md)
