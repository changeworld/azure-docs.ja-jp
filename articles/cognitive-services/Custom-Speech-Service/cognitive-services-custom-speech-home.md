---
title: Azure 上の Custom Speech Service の概要 | Microsoft Docs
description: Custom Speech Service は、ユーザーが音声からテキストへの文字起こし用の音声モデルをカスタマイズできる、クラウドベース サービスです。
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/07/2017
ms.author: panosper
ms.openlocfilehash: a6139283a555f8f97371c02f9d1f3d53dc6f15d3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374765"
---
# <a name="what-is-custom-speech-service"></a>Custom Speech Service とは

Custom Speech Service は、音声からテキストへの文字起こし用の音声モデルをカスタマイズするための機能をユーザーに提供するクラウドベース サービスです。
Custom Speech Service を使用するには、[Custom Speech Service ポータル](https://cris.ai)を参照します。

Custom Speech Service を使用すると、アプリケーションやユーザー向けに調整されているカスタマイズされた言語モデルと音響モデルを作成できます。 特定の音声とテキスト データのいずれかまたは両方を Custom Speech Service にアップロードすることで、Microsoft の既存の最先端の音声モデルと組み合わせて使用できる、カスタム モデルを作成できます。

たとえば、携帯電話、タブレット、PC のアプリに音声対話を追加している場合は、Microsoft の音響モデルと組み合わせ可能なカスタム言語モデルを作成して、アプリ専用に設計した音声テキスト変換のエンドポイントを作成できます。 特定の環境や、特定のユーザー層による使用向けにアプリケーションが設計されている場合、このサービスを利用してカスタムの音響モデルを作成およびデプロイすることもできます。


## <a name="how-do-speech-recognition-systems-work"></a>音声認識システムのしくみ
音声認識システムは、連動する複数のコンポーネントから構成されています。 最も重要な 2 つのコンポーネントは、音響モデルと言語モデルです。

音響モデルとは、音声の短い断片を特定の言語のいくつかの音素、つまり音声単位に分類する分類器です。 たとえば、“speech” という単語は “s p iy ch” という 4 つの音素で構成されています。 こうした分類は、1 秒あたり 100 回程度行われます。

言語モデルは、一連の単語における確率分布を表すものです。 言語モデルにより、一連の単語の中から、単語系列自体の確率に基づいて音が似ている単語を選択できます。 たとえば、“recognize speech” と “wreck a nice beach” の音は似ていますが、前者の仮定の発生確率の方がはるかに大きいため、言語モデルが付けるスコアは大きくなります。

音響モデルと言語モデルは両方とも、トレーニング データから学習した統計モデルです。 結果として、アプリケーションでの使用時に遭遇する音声が、トレーニング中に観察されたデータと類似している場合に、最適に動作します。 Microsoft 音声テキスト変換エンジンの音響モデルと言語モデルは、音声およびテキストの巨大なコレクションでトレーニングされており、スマート フォン、タブレット、または PC 上の Cortana との連携、音声による Web 検索、友人へのテキスト メッセージのディクテーションなど、最も一般的な使用のシナリオに対して最先端のパフォーマンスを提供します。

## <a name="why-use-the-custom-speech-service"></a>Custom Speech Service を使用する理由
Microsoft 音声テキスト変換エンジンは国際的レベルのものですが、上記のシナリオを対象としています。 しかし、アプリケーションに対する音声クエリに、通常の音声ではめったに使われない製品名や専門用語などの特定の語彙が含まれると予想される場合は、言語モデルをカスタマイズすることでパフォーマンスの改善を実現できる可能性があります。

たとえば、音声により MSDN を検索するアプリを作成する場合、通常の音声アプリケーションよりも “オブジェクト指向” や “名前空間”、“Dot Net” などの用語が使われる可能性は高いと考えられます。 言語モデルのカスタマイズにより、システムにこうした状況を学習させることができます。

## <a name="next-steps"></a>次の手順

Custom Speech Service の使用方法について詳しくは、Custom Speech Service ポータルhttps://cris.ai)を参照してください。

* [作業の開始](cognitive-services-custom-speech-get-started.md)
* [FAQ](cognitive-services-custom-speech-faq.md)
* [用語集](cognitive-services-custom-speech-glossary.md)
