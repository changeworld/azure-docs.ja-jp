---
title: オーディオ処理 - Speech Service
titleSuffix: Azure Cognitive Services
description: Microsoft Audio Stack のオーディオ処理と機能の概要。
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/17/2021
ms.author: hasshah
ms.custom: devx-track-csharp, ignite-fall-2021
ms.openlocfilehash: f0bc80a9c248b9171a89bead1971cb7d5f4ce0fe
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091860"
---
# <a name="audio-processing"></a>オーディオ処理

オーディオ処理とは、オーディオの品質を向上させる目的でオーディオ ストリームに適用される機能強化を指します。 組み合わされた一連の機能強化は、多くの場合、オーディオ処理スタックと呼ばれます。 オーディオ品質を向上させる目標は、音声処理や電気通信などのさまざまなシナリオにさらにセグメント化できるようにすることです。 一般的な機能強化の例としては、自動ゲイン制御 (AGC)、ノイズ抑制、音響エコー キャンセル (AEC) があります。

さまざまなシナリオまたは使用例に、オーディオ処理スタックの動作に影響を与える各種の最適化が必要です。 たとえば、電話などの電気通信のシナリオでは、処理が適用された後にオーディオ信号にわずかな歪みが生じる可能性があります。 これは、人間は、高い正確性でスピーチを継続して理解できるためです。 しかし、聞く人にとって、エコーで自分の声の反響を聞くのは受け入れられず、気が散るものです。 これは、音声処理のシナリオとは対照的です。音声処理において、歪んだオーディオは、機械学習音声認識モデルの精度に悪影響を与える可能性がありますが、エコー残差のレベルが小さい場合は許容されます。

## <a name="microsoft-audio-stack"></a>Microsoft Audio Stack

Microsoft Audio Stack は、音声処理シナリオ用に最適化された一連の拡張機能です。 これには、キーワード認識や音声認識のような例が含まれます。 これは、入力オーディオ信号を操作するさまざまな機能強化機能またはコンポーネントで構成されています。

* **ノイズ抑制** - バックグラウンド ノイズのレベルを減らします。
* **ビームフォーミング** - サウンドの原点をローカライズし、複数のマイクを使用してオーディオ信号を最適化します。
* **残響除去** - 環境内の面からの音の反響を減らします。
* **音響エコー キャンセル** - マイク入力がアクティブな間はデバイスからオーディオが再生されるのを抑制します。
* **自動ゲイン制御** - ソフト スピーカー、遠距離、または調整されていないマイクを考慮して、人の音声レベルを動的に調整します。

[![Microsoft Audio Stack の機能強化のブロック図。](media/audio-processing/mas-block-diagram.png)](media/audio-processing/mas-block-diagram.png#lightbox)

Microsoft Audio Stack は、Microsoft 製品の幅広い機能を提供します。
* **Windows** - Microsoft Audio Stack は、Speech オーディオ カテゴリを使用する場合の既定の音声処理パイプラインです。 
* **Microsoft Teams ディスプレイおよび Microsoft Teams Room デバイス** - Microsoft Teams ディスプレイと Teams Room デバイスでは、Microsoft Audio Stack を使用して、Cortana で高品質のハンズフリーの音声ベースのエクスペリエンスを実現します。

### <a name="pricing"></a>価格

Speech SDK で Microsoft Audio Stack を使用する場合、コストはかかりません。

### <a name="minimum-requirements-to-use-microsoft-audio-stack"></a>Microsoft Audio Stack を使用する最小要件

Microsoft Audio Stack は、次の要件を満たした任意の製品またはアプリケーションで使用できます。
* **未加工のオーディオ** - Microsoft Audio Stack では、最良の結果を得る入力として生の (未処理の) オーディオが必要です。 処理済みのオーディオを提供すると、オーディオ スタックが高品質で拡張機能を実行する能力が制限されます。
* **マイク ジオメトリ** - Microsoft Audio Stack によって提供されるすべての拡張機能を正しく実行するには、デバイス上の各マイクに関するジオメトリ情報が必要です。 情報には、マイクの数、その物理的な配置、座標が含まれます。 最大 16 個の入力マイク チャネルがサポートされています。 
* **ループバックまたは参照オーディオ** - 音響エコー キャンセルを実行するには、デバイスから再生されるオーディオを表すオーディオ チャネルが必要です。 
* **入力形式** - Microsoft Audio Stack では、16 kHz の整数倍のサンプル レートのダウン サンプリングがサポートされています。 16 kHz の最小サンプリング レートが必要です。 さらに、32 ビット IEEE リトル エンディアン float 型、32 ビット リトル エンディアン符号付き int 型、24 ビット リトル エンディアン符号付き int 型、16 ビット リトル エンディアン符号付き int、8 ビット符号付き int 型の形式がサポートされています。

## <a name="next-steps"></a>次のステップ

* [Microsoft Audio Stack の Speech SDK 統合の詳細を確認します。](audio-processing-speech-sdk.md)
