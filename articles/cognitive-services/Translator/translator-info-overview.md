---
title: Translator Text API とは
titlesuffix: Azure Cognitive Services
description: Translator Text API をアプリケーション、Web サイト、ツール、またはその他のソリューションに統合して、多言語ユーザー エクスペリエンスを提供できます。
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: overview
ms.date: 05/10/2018
ms.author: nolachar
ms.openlocfilehash: c0004960b7a234d374ec0ae1bdc2f6576b197705
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2018
ms.locfileid: "49385027"
---
# <a name="what-is-translator-text-api"></a>Translator Text API とは

Translator Text API は、アプリケーション、Web サイト、ツール、またはその他のソリューションにシームレスに統合して、[60 言語を超える](languages.md)多言語ユーザー エクスペリエンスを提供できます。 使用しているハードウェア プラットフォーム上やオペレーティング システムにかかわらず、テキストからテキストへの言語翻訳を実行できます。

Translator Text API は、機械学習と AI のアルゴリズムをクラウドで提供する Azure の [Cognitive Services API ](https://docs.microsoft.com/azure/#pivot=products&panel=ai) コレクションの一部です。この API は、開発プロジェクトですぐに利用できます。

## <a name="about-microsoft-translator"></a>Microsoft Translator について

Microsoft Translator は、クラウドベースの機械翻訳サービスです。 このサービスの中核にあるのは、さまざまな Microsoft 製品やサービスを動かしていて、コンテンツを世界中のユーザーに届けられるように、世界中に広がる数千の企業のアプリケーションとワークフローで使用されている Translator Text API と [Translator Speech API](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-translation) です。

音声翻訳は [Cognitive Services Speech プレビュー](https://docs.microsoft.com/azure/cognitive-services/speech-service/)でも使用できます。これは、既存の Translator Speech API、Bing Speech API、および Custom Speech Service (プレビュー) を統合し、完全にカスタマイズ可能なサービスです。  

Microsoft Translator サービスの詳細については、[こちら](https://www.microsoft.com/en-us/translator/home.aspx)を参照してください

## <a name="language-customization"></a>言語のカスタマイズ

コア Microsoft Translator サービスの拡張機能である Custom Translator は、Translator Text API と組み合わせて使用することで、ニューラル翻訳システムをカスタマイズし、特定の用語やスタイルの翻訳を改善することができます。

Custom Translator を使用すると、独自のビジネスや業界で使用される用語を処理するニューラル翻訳システムを構築できます。 カスタマイズされた翻訳システムは、通常の Microsoft Translator Text API でカテゴリ パラメーターを使用して、複数の種類のデバイス全体で既存のアプリケーション、ワークフロー、および Web サイトに簡単に統合できます。 

[言語カスタマイズ](customization.md)の詳細

## <a name="microsoft-translator-neural-machine-translation"></a>Microsoft Translator のニューラル機械翻訳

ニューラル機械翻訳 (NMT) は、高品質な AI による機械翻訳の新しい標準です。 2010 年半ばに品質の向上が停滞するようになった従来の統計的機械翻訳 (SMT) は、このテクノロジで置き換えられます。

NMT では、生の翻訳品質のスコアリングの観点からだけでなく、音声がより流ちょうで人間らしいため、SMT より優れた翻訳が提供されます。 こうした流ちょうさが得られるのは、NMT では 1 つの文章のコンテキスト全体を使用して言葉を変換することが主な理由です。 SMT で考慮されるのは、各単語の前後にある数語の直接的コンテキストのみです。

NMT モデルは、API のコアにあって、エンドユーザーからは見えません。 顕著な違いは翻訳品質の向上のみです。特に中国語、日本語、アラビア語などの言語で向上しています。 

NMT の動作方法の詳細については、[こちら](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)を参照してください

## <a name="next-steps"></a>次の手順

- [価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)を確認します。

- [サインアップ](translator-text-how-to-signup.md)してアクセス キーを入手します。

- [こちらのクイック スタート](quickstarts/csharp.md)は C# で記述された REST API 呼び出しのチュートリアルです。 ある言語から別の言語にテキストを翻訳する方法について説明します。

- [API リファレンス ドキュメント](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)には、API に関する技術文書があります。

## <a name="see-also"></a>関連項目

- [Cognitive Services のドキュメント ページ](https://docs.microsoft.com/azure/#pivot=products&panel=ai)
- [Cognitive Services の製品ページ](https://azure.microsoft.com/services/cognitive-services/)
- [ソリューションと価格情報](https://www.microsoft.com/en-us/translator/default.aspx)
