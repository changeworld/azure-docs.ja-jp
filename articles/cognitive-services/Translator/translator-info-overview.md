---
title: Microsoft Translator サービス
titlesuffix: Azure Cognitive Services
description: Translator をアプリケーション、Web サイト、ツール、またはその他のソリューションに統合して、多言語ユーザー エクスペリエンスを提供できます。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.subservice: translator-text
ms.date: 02/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: Translator, テキスト翻訳, 機械翻訳, 翻訳サービス
ms.openlocfilehash: 72df700d38cad3ee31ef438d7df1c890acde9e6b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727976"
---
# <a name="what-is-the-translator-service"></a>Translator サービスとは

Translator はクラウドベースの機械翻訳サービスであり、インテリジェント アプリを構築するたに使用されるコグニティブ API の [Azure Cognitive Services](../../index.yml?panel=ai&pivot=products) ファミリに含まれています。 Translator は、皆さんのアプリケーションや Web サイト、ツール、ソリューションに簡単に統合することができます。 これにより、[90 の言語と方言](./language-support.md)で多言語ユーザー エクスペリエンスを追加できます。 また、これは、任意のオペレーティング システムを備えた任意のハードウェア プラットフォームでテキスト翻訳に使用できます。

## <a name="about-microsoft-translator"></a>Microsoft Translator について

Translator は、Microsoft の多くの製品やサービスに活かされているほか、世界中の膨大な数の企業がそのアプリケーションとワークフローでこれを使用しています。

Translator によって支えられた音声翻訳は、[Azure Speech サービス](../speech-service/index.yml)を通じて利用することもできます。 Translator Speech API と Custom Speech Service の機能が融合され、一元化された完全にカスタマイズ可能なサービスとなっています。 

## <a name="language-support"></a>言語のサポート

Translator は、テキスト翻訳、表記変換、言語検出、辞書の多言語化を実現します。 サポートされる全言語のリストについては、[言語のサポート](language-support.md)に関するページを参照してください。このリストには、プログラムから [REST API](./reference/v3-0-languages.md) でアクセスすることもできます。  

## <a name="microsoft-translator-neural-machine-translation"></a>Microsoft Translator のニューラル機械翻訳

ニューラル機械翻訳 (NMT) は、高品質な AI による機械翻訳の新しい標準です。 2010 年半ばに品質の向上が停滞するようになった従来の統計的機械翻訳 (SMT) は、このテクノロジで置き換えられます。

NMT では、生の翻訳品質のスコアリングの観点からだけでなく、音声がより流ちょうで人間らしいため、SMT より優れた翻訳が提供されます。 こうした流ちょうさが得られるのは、NMT では 1 つの文章のコンテキスト全体を使用して言葉を変換することが主な理由です。 SMT で考慮されるのは、各単語の前後にある数語の直接的コンテキストのみです。

NMT モデルは、API のコアにあって、エンドユーザーからは見えません。 顕著な違いは翻訳品質の向上のみです。特に中国語、日本語、アラビア語などの言語で向上しています。

[NMT の動作方法](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)の詳細についてご確認ください。

## <a name="improve-translations-with-custom-translator"></a>Custom Translator による翻訳の改善

 Translator サービスの拡張機能である Custom Translator を Translator と組み合わせて使用することで、ニューラル翻訳システムをカスタマイズでき、特定の用語やスタイルの翻訳を改善できます。

Custom Translator を使用すると、独自のビジネスや業界で使用される用語を処理する翻訳システムを構築できます。 カスタマイズされた翻訳システムは、通常の Translator でカテゴリ パラメーターを使用して、既存のアプリケーション、ワークフロー、Web サイト、デバイスと簡単に統合できます。

[Custom Translator](customization.md) の詳細についてご確認ください。

## <a name="next-steps"></a>次のステップ

- [Translator サービスを作成](./translator-how-to-signup.md)して、アクセス キーとエンドポイントを取得します。
- [クイックスタート](quickstart-translator.md)を試して Translator サービスをすばやく呼び出します。
- [API リファレンス](./reference/v3-0-reference.md)には、API に関する技術文書があります。
- [価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
