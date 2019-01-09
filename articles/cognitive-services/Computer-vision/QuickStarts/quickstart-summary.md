---
title: Computer Vision クイック スタートの概要
titleSuffix: Azure Cognitive Services
description: これらのクイック スタートでは、Computer Vision API を使って、画像の分析やサムネイルの作成、印刷されたテキストの抽出、手書きテキストの抽出を行います。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 645c57c9bfeaefcfd2fc50b2dd431713fffaa42b
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53578786"
---
# <a name="quickstart-summary"></a>クイック スタート:まとめ

これらのクイック スタートでは、Computer Vision サービスをすぐにご利用いただけるよう役立つ情報とコード サンプルを紹介しています。

一連のサンプルでは、HTTP 呼び出しを Computer Vision API にリダイレクトします。 Computer Vision クライアント ライブラリには、HTTP 呼び出しをラップする便利なメソッドが用意されています。これらのライブラリを使ったサンプルについては、"*SDK のクイック スタート*" に関するセクションをご覧ください。

Computer Vision API を簡単に試す場合は、[Open API テスト コンソール](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)をお試しください。

Computer Vision サービスを使うと、次のタスクを実行することができます。

* リモート画像の分析
* ローカル画像の分析
* 著名人やランドマークの検出 (ドメイン モデル)
* サムネイルをインテリジェントに生成する
* 画像に含まれる印刷されたテキスト (OCR) の検出と抽出
* 画像に含まれる手書きテキストの検出と抽出

いずれのサンプルも、使われているコードは似ています。 しかし、Computer Vision サービスとの間でデータをやり取りするさまざまな手法と共に、その各種機能を大きく取り上げた内容となっています。

* "_サムネイルの生成_" では、応答の本文で画像がバイト配列として返されます。
* "_ローカル画像の分析_" では、画像をバイト配列として要求に含める必要があります。
* "_手書きテキストの抽出_" では、テキストを取得するために 2 回の呼び出しが必要となります。

## <a name="summary"></a>まとめ

| クイック スタート               | 要求パラメーター                          | Response          |
| ------------------------ | ------------------------------------------- | ----------------  |
| リモート画像の分析   | visualFeatures=Categories,Description,Color | JSON 文字列       |
| ローカル画像の分析    | data=image_data (バイト配列)                | JSON 文字列       |
| 著名人の検出       | model=celebrities                           | JSON 文字列       |
| サムネイルの生成     | width=200&height=150&smartCropping=true     | byte array        |
| 印刷されたテキストの抽出     | language=unk&detectOrientation=true         | JSON 文字列       |
| 手書きのテキストの抽出 | handwriting=true                            | URL、JSON 文字列  |

## <a name="next-steps"></a>次の手順

画像の分析、著名人やランドマークの検出、サムネイルの作成、印刷されたテキストや手書きテキストの抽出に使用される Computer Vision API の詳細を確認します。

> [!div class="nextstepaction"]
> [Computer Vision API の詳細](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
