---
title: 同意語で応答の品質を向上させる
description: このチュートリアルでは、同意語と代替語を使用して応答を向上させる方法について説明します。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/29/2021
ms.openlocfilehash: 9830eab526ad1cad4f7eb45d010958545ccec81b
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2021
ms.locfileid: "113235330"
---
# <a name="improve-quality-of-response-with-synonyms"></a>同意語で応答の品質を向上させる

このチュートリアルでは、同意語を使用することで応答の品質を向上させる方法について説明します。 ユーザーが単語の代替形式、同意語、または頭字語を使用すると、問い合わせに対して正確な応答を取られないものとします。 そこで、[Alterations API](/rest/api/cognitiveservices-qnamaker/QnAMaker4.0/Alterations) を使用してキーワードの同意語を追加することで、応答の品質を向上させることにします。

## <a name="add-synonyms-using-alterations-api"></a>Alterations API を使用して同意語を追加する

次の単語とその変化を追加して、結果を改善します。

|Word | 変化|
|--------------|--------------------------------|
| fix problems (問題を解決する) | `Troubleshoot`, `trouble-shoot`|
| whiteboard (ホワイトボード)   | `white-board`, `white board`   |
| Bluetooth    | `blue-tooth`, `blue tooth`     |

```json
{
    "wordAlterations": [
        {
            "alterations": [
                "fix problems",
                "trouble shoot",
                "trouble-shoot",
                ]
        },
        {
            "alterations": [
                "whiteboard",
                "white-board",
                "white board"
            ]
        },
        {
            "alterations": [
                "bluetooth",
                "blue-tooth",
                "blue tooth"
            ]
        }
    ]
}

```

## <a name="response-after-adding-synonyms"></a>同意語を追加した後の応答

次に示す "Fix problems with Surface Pen" (Surface ペンの問題を解決する) について、その同意語 "トラブルシューティング" を使用して行われた問い合わせの応答を比較します。

> [!div class="mx-imgBorder"]
> [ !["Fix problems with Surface Pen" (Surface ペンの問題を解決する) が赤で強調されているスクリーンショット]( ../media/adding-synonyms/fix-problems.png) ]( ../media/adding-synonyms/fix-problems.png#lightbox)

## <a name="response-before-addition-of-synonym"></a>同意語を追加する前の応答

> [!div class="mx-imgBorder"]
> [ ![信頼度スコア 71.82 が赤で強調されているスクリーンショット]( ../media/adding-synonyms/confidence-score.png) ]( ../media/adding-synonyms/confidence-score.png#lightbox)

## <a name="response-after-addition-of-synonym"></a>同意語を追加した後の応答 

> [!div class="mx-imgBorder"]
> [ ![信頼度スコア 97 が赤で強調されているスクリーンショット]( ../media/adding-synonyms/increase-score.png) ]( ../media/adding-synonyms/increase-score.png#lightbox)

このように、同意語として `trouble shoot` が追加されていないときは、"How to troubleshoot your surface pen" (Surface ペンのトラブルシューティング方法) という問い合わせに対して信頼度の低い応答が返されました。 一方、"fix problems" (問題を解決する) の同意語として `trouble shoot` を追加した後では、問い合わせに対して高い信頼度スコアの正しい応答を受け取りました。 これらの単語の変化を追加すると、結果の関連性が向上し、それによってユーザー エクスペリエンスが向上しました。 

> [!NOTE]
> 同意語は大文字と小文字を区別されません。 また、同意語としてストップ ワードを追加した場合も、同意語が意図したとおりに機能しないことがあります。 ストップ ワードの一覧は、[ストップ ワードの一覧](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/STOPWORDS.md)に関するページで確認できます。

たとえば、Information Technology (情報技術) の省略形 **IT** を追加した場合、**IT** はストップ ワードであり、問い合わせが処理されるときにフィルター処理されるため、システムは Information Technology を認識できない可能性があります。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [複数の言語のナレッジ ベースを作成する](multiple-languages.md)