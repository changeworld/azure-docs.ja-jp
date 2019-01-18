---
title: 音声翻訳について - Speech Services
titlesuffix: Azure Cognitive Services
description: Speech Service API を使用すると、音声のエンド ツー エンドでリアルタイムの多言語翻訳機能を、アプリケーション、ツール、デバイスに追加することができます。 同じ API を、音声間の翻訳と、音声テキスト変換の両方に使用できます。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 9d94105377597ca8e79cc43ed0903371e185d510
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53086710"
---
# <a name="about-the-speech-translation-api"></a>Speech Translation API について

Speech Service API を使用すると、音声のエンド ツー エンドでリアルタイムの多言語翻訳機能を、アプリケーション、ツール、デバイスに追加することができます。 同じ API を、音声間の翻訳と、音声テキスト変換の両方に使用できます。

Translator Speech API では、クライアント アプリケーションは、サービスに音声オーディオをストリーミングし、結果のストリームを受け取ります。 これらの結果には、ソース言語での認識されたテキストと、ターゲット言語での翻訳が含まれます。 発話が完了するまでは中間翻訳を提供でき、完了した時点で最終的な翻訳を提供できます。

必要に応じて、最終的な翻訳の合成オーディオ バージョンを準備して、真の音声間翻訳を実現できます。

Speech Translation API では、WebSocket プロトコルを使用して、クライアントとサーバーの間に全二重通信チャンネルを提供します。 ただし、ユーザーは WebSocket を処理する必要はありません。Speech SDK が代わって処理します。

Speech Translation API では、Microsoft のさまざまな製品やサービスで利用されているのと同じテクノロジが採用されています。 このサービスは、既に世界中の何千もの企業でアプリケーションやワークフローに使用されています。

## <a name="about-the-technology"></a>テクノロジについて

基になっている Microsoft の翻訳エンジンは、統計的機械翻訳 (SMT) とニューラル機械翻訳 (NMT) の 2 種類です。 後者は、ニューラル ネットワークを採用した人工知能アプローチであり、機械翻訳に対する最新のアプローチです。 NMT は、正確さだけでなく、流ちょうさや自然さについても、よりよい翻訳を提供します。 こうした流ちょうさが得られるのは、NMT では 1 つの文章のコンテキスト全体を使用して言葉を変換することが主な理由です。

今日では、Microsoft はよく使われるほとんどの言語で NMT に移行しており、SMT はあまり使われない言語に対してのみ採用されています。 [音声間翻訳に使用できるすべての言語](language-support.md#speech-translation)で、NMT が導入されています。 音声テキスト変換では、言語ペアに応じて SMT または NMT を使用できます。 ターゲット言語が NMT によってサポートされている場合、翻訳全体が NMT によって処理されます。 ターゲット言語が NMT によってサポートされていない場合は、2 つの言語間の "ピボット" として英語を使用する NMT と SMT のハイブリッド翻訳となります。

モデル間の違いは、翻訳エンジンの内部で吸収されます。 エンド ユーザーが気付くのは、翻訳品質の向上だけです (特に、中国語、日本語、アラビア語)。

> [!NOTE]
> Microsoft の翻訳エンジンの背後にあるテクノロジについての学習に興味がありますか。 「[機械翻訳](https://www.microsoft.com/en-us/translator/mt.aspx)」をご覧ください。

## <a name="next-steps"></a>次の手順

* [Speech 試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
* [C# で音声を翻訳する方法を確認します](how-to-translate-speech-csharp.md)
* [C++ で音声を翻訳する方法を確認する](how-to-translate-speech-cpp.md)
* [Java で音声を翻訳する方法を確認する](how-to-translate-speech-java.md)
