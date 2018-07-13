---
title: Microsoft Translator Text API による不適切な表現のフィルター処理 | Microsoft Docs
description: Microsoft Translator Text API で不適切な表現のフィルター処理を使用します。
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: a7172e1e8aa336c011fb06e93fc5c4b54d26a3cd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374349"
---
# <a name="how-to-add-profanity-filtering-with-the-microsoft-translator-text-api"></a>Microsoft Translator Text API による不適切な表現のフィルター処理を追加する方法

通常、Translator サービスでは、ソースに存在する不適切な表現は翻訳でも保たれます。 不適切な表現の程度と、単語を不適切にしているコンテキストは、文化により異なります。 その結果、対象言語における不適切な表現の程度は、増幅されたり低減されることがあります。

翻訳で不適切な表現が表示されるのを防ぐには (ソース テキスト内での不適切な表現の有無に関係なく)、Translate() メソッドで不適切な表現のフィルター処理オプションを使用できます。 このオプションを使用すれば、不適切な表現を削除したり適切なタグでマークされていることを確認するか、アクションを行わないかを選択できます。

Translate() メソッドは "options" パラメーターを取り、ここには "ProfanityAction" の新しい要素が含まれます。 ProfanityAction の値は、"NoAction"、"Marked"、および "Deleted" です。

## <a name="accepted-values-of-profanityaction-and-examples"></a>ProfanityAction に指定できる値と例
|ProfanityAction 値 | アクションを表示します。 | 例: ソース - 日本語 | 例: ターゲット - 英語|
| :---|:---|:---|:---|
| NoAction | [既定]。 オプションを設定しない場合と同じです。 不適切な表現はソースからターゲットに渡されます。 | 彼は変態です。 | He is a jerk. |
| Marked | 不適切な表現は XML タグ \<profanity> … \</profanity> で囲まれます。 | 彼は変態です。 | He is a \<profanity>jerk\</profanity>. |
| Deleted | 不適切な単語は、置換されずに出力から削除されます。 | 彼は。 | He is a. |

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [Translator API 呼び出しで不適切な表現のフィルター処理を適用する](reference/v3-0-translate.md)

