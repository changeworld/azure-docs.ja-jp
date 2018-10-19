---
title: V3 への移行 - Translator Text API
titlesuffix: Azure Cognitive Services
description: Translator Text API を V2 から V3 に移行する方法について説明します。
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: v-jansko
ms.openlocfilehash: eaf65bef28110d73378c213ae4781a409b86e1bd
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "46128181"
---
# <a name="translator-text-api-v2-to-v3-migration"></a>Translator Text API を V2 から V3 に移行する

Microsoft Translator チームは Translator Text API のバージョン 3 (V3) をリリースしました。 このリリースには、新しい機能、非推奨になったメソッド、Microsoft Translator Service との間でデータを送受信するための新しい形式が含まれています。 このドキュメントでは、V3 を使用するためにアプリケーションを変更する方法に関する情報を提供します。 V2 は 2018 年 4 月 30 日をもって非推奨となり、2019 年 4 月 30 日をもってサービス停止となります。

このドキュメントの終わりには、役に立つ詳細ページのリンクが記載されています。

## <a name="summary-of-features"></a>機能のまとめ

* トレースなし - V3 では、Azure Portal の全価格レベルにトレースが適用されません。 つまり、この機能では、V3 API に送信されたテキストを Microsoft が保存することはありません。
* JSON - XML が JSON に取って代わられます。 サービスに届くデータとサービスから届くデータはすべて JSON 形式になります。
* 1 回の要求で複数のターゲット言語 - 翻訳メソッドは、1 回の要求で複数の ‘翻訳先’ 言語に応じます。 たとえば、1 回の要求で ‘翻訳元’ を英語とし、‘翻訳先’ にドイツ語、スペイン語、日本語、その他の言語グループを指定できます。
* 2 か国語辞書 - 2 か国語辞書メソッドが API に追加されました。 このメソッドには ‘lookup’ と ‘examples’ が含まれます。
* 音訳 - 音訳メソッドが API に追加されました。 このメソッドでは、ある文字体系 (アラビア語 など) で書かれた言葉や文章が別の文字体系 (ラテン語 など) に変換されます。
* 言語 - 新しい ‘languages’ メソッドは、‘translate’、‘dictionary’、‘transliterate’ メソッドで使用するために、言語情報を JSON 形式で届けます。
* 翻訳の新機能 - 別のメソッドとして V2 API に存在した一部の機能に対応する目的で、新しい機能が ‘translate’ メソッドに追加されました。 それには、たとえば、TranslateArray があります。
* 読み上げメソッド - テキスト読み上げ機能は、Microsoft Translator API でサポートから外れました。 テキスト読み上げ機能は、Azure Cognitive サービスの Bing Speech API でご利用いただけます。

V2 メソッドと V3 メソッドについてまとめた次のリストで、V3 メソッドと、V2 に備わっていた機能を提供する API を確認できます。

| V2 API メソッド   | V3 API 互換性 |
|:----------- |:-------------|
| Translate     | 翻訳          |
| TranslateArray      | 翻訳          |
| GetLanguageNames      | Languages          |
| GetLanguagesForTranslate     | Languages        |
| GetLanguagesForSpeak      | Cognitive Services Speech API         |
| Speak     | Cognitive Services Speech API          |
| Detect     | Detect         |
| DetectArray     | Detect         |
| AddTranslation     | Microsoft Translator HUB API         |
| AddTranslationArray    | Microsoft Translator HUB API          |
| BreakSentences      | BreakSentence         |
| GetTranslations      | この機能は現在サポートされていません         |
| GetTranslationsArray      | Feature は現在サポートされていません         |

## <a name="move-to-json-format"></a>JSON 形式に移行

Microsoft Translator Text Translation V2 では、XML 形式でデータを受け取り、返しました。 V3 では、API を利用して送受信されるすべてのデータが JSON 形式になります。 XML は V3 では受け取られず、返されません。 

この変更は、V2 Text Translation API のために記述されたアプリケーションのいくつかの面に影響を与えます。 例: Languages API は、テキスト翻訳メソッド、音訳メソッド、2 か国語辞書メソッドの言語情報を返します。 1 回の呼び出しですべてのメソッドのすべての言語情報を要求するか、それらを個別に要求できます。

言語メソッドは認証を要求しません。次のリンクをクリックすると、V3 の全言語情報を JSON で参照できます。

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>認証キー

V2 で使用している認証キーは、V3 で受け取られます。 新しいサブスクリプションを取得する必要はありません。 移行期間が 1 年間続くとき、アプリで V2 と V3 を混在させることができます。V2-XML から V3-JSON に移行しながら、新しいバージョンを公開することが簡単にできます。

## <a name="pricing-model"></a>価格モデル

Microsoft Translator V3 の価格設定は V2 のときと同じであり、スペースを含む文字あたりとなります。 V3 に新機能が追加されたことで、課金対象となる文字の数え方にいくつかの変更があります。

| V3 メソッド   | 課金対象となる文字数 |
|:----------- |:-------------|
| Languages     | いかなる文字も送信されず、何も数えられず、何も課金されません。          |
| 翻訳     | 翻訳のために送信された文字の数と文字の翻訳先となる言語の数に基づいて数えられます。 50 文字が送信され、5 か国語が要求された場合、50x5 で計算されます。           |
| Transliterate     | 音訳のために送信された文字数が数えられます。         |
| Dictionary の lookup と example     | Dictionary の lookup と examples のために送信された文字数が数えられます。         |
| BreakSentence     | 課金はありません。       |
| Detect     | 課金はありません。      |

## <a name="v3-end-points"></a>V3 エンド ポイント

グローバル

* api.cognitive.microsofttranslator.com


## <a name="v3-api-text-translations-methods"></a>V3 API テキスト翻訳メソッド

[言語](reference/v3-0-languages.md)

[Translate](reference/v3-0-translate.md)

[Transliterate](reference/v3-0-transliterate.md)

[BreakSentence](reference/v3-0-break-sentence.md)

[Detect](reference/v3-0-detect.md)

[Dictionary/lookup](reference/v3-0-dictionary-lookup.md)

[Dictionary/example](reference/v3-0-dictionary-examples.md)

## <a name="customization"></a>カスタマイズ

Microsoft Translator V3 では、ニューラル機械翻訳が既定で使用されます。 そのため、Microsoft Translator Hub では使用できません。 Translator Hub は、従来の統計的な機械翻訳のみをサポートしています。 ニューラル翻訳のカスタマイゼーションは、Custom Translator を使用してご利用いただけるようになりました。 [ニューラル機械翻訳のカスタマイゼーションに関する詳細](customization.md)

V3 テキスト API によるニューラル翻訳では、標準カテゴリ (SMT、speech、text、generalnn) を使用できません。


## <a name="links"></a>リンク

* [Microsoft プライバシーに関する声明](https://privacy.microsoft.com/privacystatement)
* [Microsoft Azure の法的情報](https://azure.microsoft.com/support/legal)
* [オンライン サービス規約](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [V3.0 ドキュメントを見る](reference/v3-0-reference.md)
