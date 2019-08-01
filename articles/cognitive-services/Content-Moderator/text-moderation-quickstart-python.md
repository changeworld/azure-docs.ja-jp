---
title: クイック スタート:Python でテキストの内容を分析する - Content Moderator
titleSuffix: Azure Cognitive Services
description: Content Moderator SDK for Python を使用してさまざまな好ましくない要素を検出するためにテキスト コンテンツを分析する方法
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: ceaaa9bdb5f6e5c977e0c1a71af080f22d52a5e7
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561212"
---
# <a name="quickstart-analyze-text-content-for-objectionable-material-in-python"></a>クイック スタート: Python で好ましくない要素を検出するためにテキストの内容を分析する

この記事では、Content Moderator SDK for Python の使用を開始するために役立つ情報とコード サンプルを提供します。 潜在的に好ましくない素材をモデレートすることを目的として、テキスト コンテンツの用語ベースのフィルター処理と分類を実行する方法を学習します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 

## <a name="prerequisites"></a>前提条件
- Content Moderator のサブスクリプション キー。 [Cognitive Services アカウントの作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)に関するページの手順に従って、Content Moderator をサブスクライブし、キーを取得します。
- [Python 2.7 以降または 3.5 以降](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/) ツール

## <a name="get-the-content-moderator-sdk"></a>Content Moderator SDK を取得する

Content Moderator Python SDK をインストールするには、コマンド プロンプトを開いて次のコマンドを実行します。

```bash
pip install azure-cognitiveservices-vision-contentmoderator
```

## <a name="import-modules"></a>モジュールをインポートする

_ContentModeratorQS.py_ という名前の新しい Python スクリプトを作成して、次のコードを追加し、SDK の必要な部分をインポートします。 この整形印刷モジュールは、JSON 応答を読みやすくするために用意されています。

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=imports)]


## <a name="initialize-variables"></a>変数の初期化

次に、お使いの Content Moderator サブスクリプション キーとエンドポイント URL のための変数を追加します。 環境変数に名前 `CONTENT_MODERATOR_SUBSCRIPTION_KEY` を追加し、その値としてサブスクリプション キーを使用します。 ベース エンドポイント URL 用に、`CONTENT_MODERATOR_ENDPOINT` を環境変数に追加し、その値としてリージョンに固有の URL を使用します (例: `https://westus.api.cognitive.microsoft.com`)。 無料試用版のサブスクリプション キーは、**westus** リージョンで生成されます。

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=authentication)]

ファイルからの複数行テキストの文字列は、調整されます。 [content_moderator_text_moderation.txt](https://github.com/Azure-Samples/cognitive-services-content-moderator-samples/blob/master/documentation-samples/python/content_moderator_text_moderation.txt) ファイルをローカル ルート フォルダーに含め、そのファイル名を変数に追加します。

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=textModerationFile)]

## <a name="query-the-moderator-service"></a>Moderator サービスの照会

お使いのサブスクリプション キーとエンドポイント URL を使用して、**ContentModeratorClient** インスタンスを作成します。 

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=client)]

次に、クライアントをそのメンバー **TextModerationOperations** インスタンスと共に使用して、関数 `screen_text` を使用してモデレート API を呼び出します。 呼び出し方法の詳細については、 **[screen_text](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)** リファレンスのドキュメントを参照してください。

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=textModeration)]

## <a name="check-the-printed-response"></a>印刷された応答の確認

サンプルを実行し、応答を確認します。 正常に完了すると、**Screen** インスタンスが返されます。 成功した場合の結果を次に示します。

```console
{'auto_corrected_text': '" Is this a garbage email abide@ abed. com, phone: '
                        '6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, '
                        'Redmond, WA 98052. Crap is the profanity here. Is '
                        'this information PII? phone 3144444444\\ n"',
 'classification': {'category1': {'score': 0.00025233393535017967},
                    'category2': {'score': 0.18468093872070312},
                    'category3': {'score': 0.9879999756813049},
                    'review_recommended': True},
 'language': 'eng',
 'normalized_text': '" Is this a garbage email abide@ abed. com, phone: '
                    '6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, '
                    'Redmond, WA 98052. Crap is the profanity here. Is this '
                    'information PII? phone 3144444444\\ n"',
 'original_text': '"Is this a grabage email abcdef@abcd.com, phone: '
                  '6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, '
                  'WA 98052. Crap is the profanity here. Is this information '
                  'PII? phone 3144444444\\n"',
 'pii': {'address': [{'index': 82,
                      'text': '1 Microsoft Way, Redmond, WA 98052'}],
         'email': [{'detected': 'abcdef@abcd.com',
                    'index': 25,
                    'sub_type': 'Regular',
                    'text': 'abcdef@abcd.com'}],
         'ipa': [{'index': 65, 'sub_type': 'IPV4', 'text': '255.255.255.255'}],
         'phone': [{'country_code': 'US', 'index': 49, 'text': '6657789887'},
                   {'country_code': 'US', 'index': 177, 'text': '3144444444'}]},
 'status': {'code': 3000, 'description': 'OK'},
 'terms': [{'index': 118, 'list_id': 0, 'original_index': 118, 'term': 'crap'}],
 'tracking_id': 'b253515c-e713-4316-a016-8397662a3f1a'}
```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Content Moderator サービスを使用して特定のサンプル テキストに関する関連情報を返す単純な Python スクリプトを開発しました。 次は、必要なデータを判断し、アプリを使ってそれをどのように処理するかを判断できるように、さまざまなフラグと分類の意味について学習します。

> [!div class="nextstepaction"]
> [テキスト モデレーション ガイド](text-moderation-api.md)
