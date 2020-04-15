---
title: 音声合成の基本 - 音声サービス
titleSuffix: Azure Cognitive Services
description: Speech SDK を使用してテキストを音声に変換する方法について説明します。 この記事では、オブジェクトの構築、サポートされているオーディオ出力形式、音声合成でのカスタム構成オプションについて説明します。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/06/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: b95b55588c51c4e202d7a02c6c158b26cdcd7ac7
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985964"
---
# <a name="learn-the-basics-of-speech-synthesis"></a>音声合成の基本を学ぶ

この記事では、Speech SDK を使用してテキスト読み上げ合成を行うための一般的な設計パターンについて説明します。 まずは基本的な構成と合成を行った後、次のようなより高度なカスタム アプリケーション開発の例に進みます。

* インメモリ ストリームとして応答を取得する
* 出力のサンプル レートとビット レートをカスタマイズする
* SSML (音声合成マークアップ言語) を使用して合成要求を送信する
* ニューラル音声を使用する

> [!TIP]
> クイックスタートのいずれかを完了する機会がなかった場合は、ご自分で動作方法の確認をして音声認識を試してみることをお勧めします。
> * [マイクから音声を認識する](quickstarts/text-to-speech.md)

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [C++ Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cpp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-python.md)]
::: zone-end

::: zone pivot="programming-language-more"
[!INCLUDE [More languages include](./includes/how-to/speech-to-text-basics/more.md)]
::: zone-end

## <a name="next-steps"></a>次のステップ

* [カスタム音声の概要](how-to-custom-voice.md)
* [SSML を使用して合成を改善する](speech-synthesis-markup.md)