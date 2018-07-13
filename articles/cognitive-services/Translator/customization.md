---
title: Microsoft Translator Text API の翻訳のカスタマイズ | Microsoft Docs
description: Microsoft Translator Hub を利用し、適切な用語とスタイルを使用して、独自の機械翻訳システムを構築します。
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 05/10/2018
ms.author: v-jansko
ms.openlocfilehash: 1db22a414c41f338c4e7fd6ce9dc7ac739fa9237
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377517"
---
# <a name="customize-your-text-translations"></a>テキスト変換をカスタマイズする

Microsoft Translator サービスの機能である Microsoft Custom Translator (プレビュー) を使用すると、ユーザーは Microsoft Translator Text API (バージョン 3 のみ) を使用してテキストを翻訳するときに、Microsoft Translator の高度なニューラル機械翻訳をカスタマイズすることができます。 

この機能を [Cognitive Services Speech (プレビュー)](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/) と使用すると、音声の翻訳をカスタマイズすることもできます。

## <a name="custom-translator"></a>Custom Translator
Custom Translator を使用すると、独自のビジネスや業界で使用される用語を理解するニューラル翻訳システムを構築できます。 カスタマイズされた翻訳システムは、既存のアプリケーション、ワークフロー、Web サイトと統合します。 

### <a name="how-does-it-work"></a>それはどのように機能しますか?
以前に翻訳したドキュメント (リーフレット、Web ページ、ドキュメントなど) を使用して、ドメイン固有の用語とスタイルが反映された、汎用翻訳システムより優れた、翻訳システムを構築します。 ユーザーは、TMX、XLIFF、TXT、DOCX、XLSX ドキュメントをアップロードできます。  

システムは、ドキュメント レベルでは並列化されているが、センテンス レベルではまだ揃っていないデータも受け付けます。 ユーザーが同じコンテンツの複数言語による個別ドキュメントのバージョンにアクセスできる場合、Custom Translator はドキュメント間でセンテンスを自動的に一致させることができます。  また、システムは、一方または両方の言語で単一言語データを使用し、並列トレーニング データを補完して翻訳を向上させることもできます。 

カスタマイズされたシステムは、カテゴリ パラメーターを使用する Microsoft Translator Text API の通常の呼び出しで利用できます。

適切な種類と量のトレーニング データがあれば、Custom Translator を使用することで、5 から 10 の間あるいはそれ以上の BLEU ポイントの翻訳品質を期待できることも珍しくありません。

使用可能なデータに基づくカスタマイズのさまざまなレベルについて詳しくは、「[Custom Translator User Guide](http://aka.ms/CustomTranslatorDocs)」(Custom Translator ユーザー ガイド) をご覧ください。


## <a name="microsoft-translator-hub"></a>Microsoft Translator Hub

従来の Microsoft Translator Hub を使用して、統計的な機械翻訳を翻訳できます。 [詳細情報](https://www.microsoft.com/en-us/translator/hub.aspx) 

## <a name="custom-translator-versus-hub"></a>Custom Translator と Hub

|   | **ハブ** | **Custom Translator**|
|:-----|:----:|:----:|
|カスタマイズ機能の状態   | 一般公開  | プレビュー |
| Text API バージョン  | V2 のみ   | V3 のみ |
| SMT のカスタマイズ | [はい]   | いいえ  | 
| NMT のカスタマイズ | いいえ     | [はい] |
| 新しい統合 Speech Services のカスタマイズ | いいえ     | [はい] | 
| [トレースなし](http://www.aka.ms/notrace) | [はい]   | [はい] | 

## <a name="collaborative-translations-framework"></a>コラボレーション翻訳フレームワーク

> [!NOTE]
> 2018 年 2 月 1 日の時点で、AddTranslation() と AddTranslationArray() は Translator Text API V2.0 では使用できなくなっています。 これらのメソッドは失敗し、何も書き込まれません。 Translator Text API V3.0 はこれらのメソッドをサポートしません。

>Translator Hub API では類似機能を使用できます。 [https://hub.microsofttranslator.com/swagger](https://hub.microsofttranslator.com/swagger) をご覧ください。 

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [Custom Translator を使用してカスタマイズされた言語システムを設定します](http://aka.ms/CustomTranslatorDocs)
