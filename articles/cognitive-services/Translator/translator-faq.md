---
title: よく寄せられる質問 - Translator
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services の Translator API についてよく寄せられる質問とその回答を紹介します。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/18/2021
ms.author: lajanuar
ms.openlocfilehash: db1d981305ee62267f72f3050475c88dbcd5cc54
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110116581"
---
# <a name="frequently-asked-questionstranslator-api"></a>よく寄せられる質問 - Translator API

## <a name="how-does-translator-count-characters"></a>Translator では文字をどのようにカウントしますか。

Translator では、Unicode で定義されているすべてのコード ポイントを文字としてカウントします。 各翻訳は、複数の言語に翻訳する 1 つの API 呼び出しで要求されたものであっても、個別の翻訳としてカウントされます。 応答の長さは関係なく、要求、単語、バイト、または文の数は文字数に関係ありません。

Translator では、次の入力をカウントします。

* 要求の本文内の Translator に渡されるテキスト。
  * [Translate](reference/v3-0-translate.md)、[Transliterate](reference/v3-0-transliterate.md)、[Dictionary Lookup](reference/v3-0-dictionary-lookup.md) の各メソッドを使用する場合`Text`、
  * `Text`および`Translation`、[Dictionary Examples](reference/v3-0-dictionary-examples.md) メソッドを使用する場合。

* すべてのマークアップ: 要求本文のテキスト フィールド内の HTML タグ、XML タグなど。 要求のビルドに使用される JSON 表記 (キー "Text:" など) はカウント **されません**。
* 個々の文字。
* 句読点。
* スペース、タブ、マークアップ、または任意の空白文字。
* 以前に同じテキストを翻訳した場合でも、繰り返しの翻訳。 translate 関数に送信されたすべての文字は、コンテンツが変更されていない場合や、ソースとターゲットの言語が同じ場合でもカウントされます。

中国語や日本語の漢字などの表意文字に基づくスクリプトでも、Translator では Unicode コード ポイントの数が引き続きカウントされます。 表意文字につき 1 文字。 例外:Unicode サロゲート ペアは 2 文字としてカウントされます。

**Detect** と **BreakSentence** のメソッドの呼び出しは、文字の使用量にカウントされません。 ただし、Detect と BreakSentence のメソッドの呼び出しは、カウントされる他の関数の使用に対して妥当な割合となっていることが求められます。 Detect または BreakSentence の呼び出しの数が、他のカウントされるメソッドの 100 倍を超えた場合に、Microsoft は Detect と BreakSentence のメソッドの使用を制限する権利を留保します。

## <a name="where-can-i-see-my-monthly-usage"></a>毎月の使用量はどこで確認できますか。

コストの見積もりには、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)をご利用ください。 また、Azure portal の自分のアカウントで、Azure サービスの Azure アラートを監視、表示、追加することもできます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. Translator リソース の [概要] ページに移動します。
1. Translator リソースの **サブスクリプション キー** を選択します。

    :::image type="content" source="media/azure-portal-overview.png" alt-text="Azure portal の [概要] ページのサブスクリプション リンクを示すスクリーンショット。":::

2. 左側のレールで、 **[Cost Management]** の下を選択します。

    :::image type="content" source="media/azure-portal-cost-management.png" alt-text="Azure portal のコスト管理リソースのリンクを示すスクリーンショット。":::

## <a name="is-attribution-required-when-using-translator"></a>Translator を使用する場合、属性は必要ですか。

テキスト翻訳と音声翻訳に Translator を使用する場合、属性は必要ありません。 表示しているコンテンツが機械翻訳済みである場合は、ユーザーに通知することをお勧めします。

属性が存在する場合は、[Translator の属性のガイドライン](https://www.microsoft.com/translator/business/attribution/)に準拠している必要があります。

## <a name="is-translator-a-replacement-for-human-translator"></a>Translator は人間の翻訳者に代わるものですか。

いいえ。どちらもコミュニケーションに不可欠なツールとしての役割があります。 コンテンツの量、作成速度、予算の制約がある場合は機械翻訳を使用して、人間の翻訳を使用できないようにします。

機械翻訳は、人間の翻訳を使用する前に、いくつかの [言語サービスプロバイダー (LSP)](https://www.microsoft.com/translator/business/partners/) パートナーによって最初のパスとして使用されており、生産性を最大で 50% 向上させることができます。 LSP パートナーの一覧については、Translator パートナーのページを参照してください。

---
> [!TIP]
> この FAQ で質問に対する回答が見つからない場合は、[StackOverflow](https://stackoverflow.com/search?q=%5Bmicrosoft-cognitive%5D+or+%5Bmicrosoft-cognitive%5D+translator&s=34bf0ce2-b6b3-4355-86a6-d45a1121fe27) の Translator API コミュニティに質問してください。