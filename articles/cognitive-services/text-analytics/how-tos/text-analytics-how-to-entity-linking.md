---
title: Text Analytics REST API でエンティティ リンク設定を使用する方法 (Azure 上の Microsoft Cognitive Services) | Microsoft Docs
description: このチュートリアルでは、Azure 上の Microsoft Cognitive Services の Text Analytics REST API を使用して、エンティティの識別と解決を行う方法を説明します。
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: article
ms.date: 5/02/2018
ms.author: ashmaka
ms.openlocfilehash: 55bec1a0223b70749a97a30e2da92ef15128038c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374512"
---
# <a name="how-to-identify-linked-entities-in-text-analytics-preview"></a>Text Analytics でリンクされているエンティティを識別する方法 (プレビュー)

[Entity Linking API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) は、構造化されていないテキストを取り、JSON ドキュメントごとに、あいまいさを解消したエンティティの一覧を返します。一覧には、Web 上にある、より多くの情報 (Wikipedia と Bing) へのリンクが含まれています。 

## <a name="entity-linking-vs-named-entity-recognition"></a>エンティティ リンク設定と名前付きエンティティの認識

自然言語処理において、エンティティ リンク設定の概念と、名前付きエンティティの認識 (NER) の概念が混同されることは容易に起こりえます。 プレビュー バージョンの Text Analytics の `entities` エンドポイントでは、エンティティ リンク設定のみがサポートされています。

エンティティ リンク設定は、テキスト内で見つかったエンティティの個性を識別してあいまいさを解消する機能です (例: "Mars" が惑星として使用されているか、古代ローマの戦争の神様として使用されているかを判定する)。 このプロセスのためには、認識されたエンティティがリンクされているナレッジ ベースが存在している必要があります。`entities` エンドポイントの Text Analytics には、Wikipedia がナレッジ ベースとして使用されます。

### <a name="language-support"></a>言語のサポート

さまざまな言語でエンティティ リンク設定を使用するには、各言語で、対応するナレッジ ベースを使用する必要があります。 Text Analytics でのエンティティ リンク設定の場合、これは、`entities` エンドポイントによってサポートされている各言語は、その言語の対応する Wikipedia コーパスにリンクすることを意味します。 コーパスのサイズは言語によって異なるため、エンティティ リンク設定の機能の再現度もさまざまであると考えられています。


## <a name="preparation"></a>準備

id、text、language という形式の JSON ドキュメントが必要です

現在サポートされている言語については、[この一覧](../text-analytics-supported-languages.md)を参照してください。

ドキュメントのサイズは、ドキュメントごとに 5,000 文字未満である必要があり、コレクションごとに最大 1,000 の項目 (ID) を持つことができます。 コレクションは、要求の本文で送信されます。 次の例では、エンティティ リンク設定の末尾に付け加えるコンテンツを示しています。

```
{"documents": [{"id": "1",
                "language": "en",
                "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."
                },
               {"id": "2",
                "language": "en",
                "text": "The Seattle Seahawks won the Super Bowl in 2014."
                }
               ]
}
```    
    
## <a name="step-1-structure-the-request"></a>手順 1: 要求を構造化する

要求定義の詳細については、[Text Analytics API を呼び出す方法](text-analytics-how-to-call-api.md)に関するページを参照してください。 確認に便利なように、以下に再度、要点を示します。

+ **POST** 要求を作成します。 この要求については次の API ドキュメントを確認してください: [Entity Linking API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634)

+ キー フレーズ抽出用の HTTP エンドポイントを設定します。 そこには、`/entities` リソースが含まれている必要があります: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/entities`

+ Text Analytics 操作用のアクセス キーが含まれるように要求ヘッダーを設定します。 詳細については、[エンドポイントを見つけてアクセス キーにアクセスする方法](text-analytics-how-to-access-key.md)についてのページを参照してください。

+ 要求本文で、この分析のために準備した JSON ドキュメントのコレクションを提供します

> [!Tip]
> [Postman](text-analytics-how-to-call-api.md) を使用するか、[ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634)に記載されている **API テスト コンソール**を開き、要求を構造化して POST でサービスに投稿します。

## <a name="step-2-post-the-request"></a>手順 2: 要求を投稿する

要求が受信されると分析が実行されます。 サービスは、1 分あたり最大 100 個の要求を受け付けます。 各要求の最大サイズは 1 MB です。

サービスはステートレスであることを思い出してください。 ユーザーのアカウントに保存されるデータはありません。 結果はすぐに、応答で返されます。

## <a name="step-3-view-results"></a>手順 3: 結果を表示する

すべての POST 要求で、ID と検出されたプロパティを含む JSON 形式の応答が返されます。

出力はすぐに返されます。 結果は、JSON を受け付けるアプリケーションにストリームするか、ローカル システム上のファイルに出力を保存してから、そのファイルを、データの並べ替え、検索、および操作が可能なアプリケーションにインポートすることができます。

次に、エンティティ リンク設定のための出力の例を示します。

```
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "name": "Xbox One",
                    "matches": [
                        {
                            "text": "XBox One",
                            "offset": 23,
                            "length": 8
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Xbox One",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Xbox_One",
                    "bingId": "446bb4df-4999-4243-84c0-74e0f6c60e75"
                },
                {
                    "name": "Ultra-high-definition television",
                    "matches": [
                        {
                            "text": "4K",
                            "offset": 63,
                            "length": 2
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Ultra-high-definition television",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Ultra-high-definition_television",
                    "bingId": "7ee02026-b6ec-878b-f4de-f0bc7b0ab8c4"
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "name": "2013 Seattle Seahawks season",
                    "matches": [
                        {
                            "text": "Seattle Seahawks",
                            "offset": 4,
                            "length": 16
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "2013 Seattle Seahawks season",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/2013_Seattle_Seahawks_season",
                    "bingId": "eb637865-4722-4eca-be9e-0ac0c376d361"
                }
            ]
        }
    ],
    "errors": []
}
```

見つかる場合は、応答に、検出されたエンティティごとの Wikipedia ID、Wikipedia URL、および Bing ID が含まれます。 これらは、リンクされているエンティティに関する情報によってアプリケーションをさらに強化するために使用できます。


## <a name="summary"></a>まとめ

この記事では、Cognitive Services の Text Analytics を使用するエンティティ リンク設定の概念とワークフローについて説明しました。 要約すると:

+ 選択した言語に対して、[Entity Linking API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) を使用できます。
+ 要求本文内の JSON ドキュメントには、id、text、および language のコードが含まれます。
+ POST 要求は、ユーザーのサブスクリプションで有効な、個人用に設定された[アクセス キーとエンドポイント](text-analytics-how-to-access-key.md)を使用して `/entities` エンドポイントに対して行われます。
+ リンクされているエンティティ (ドキュメント ID ごとに信頼度スコア、オフセット、Web リンクが含まれている) で構成される応答の出力は、どのアプリケーションでも使用できます

## <a name="see-also"></a>関連項目 

 [Text Analytics の概要](../overview.md)  
 [よく寄せられる質問 (FAQ)](../text-analytics-resource-faq.md)</br>
 [Text Analytics 製品ページ](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Text Analytics API](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)
