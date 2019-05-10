---
title: 会話の文字起こしサービス - Speech Services
titleSuffix: Azure Cognitive Services
description: 会話の文字起こしサービスは、リアルタイムの音声認識、話者識別、およびダイアライゼーションを組み合わせた、Speech Services の高度な機能です。 会話の文字起こしサービスは、対面会議の文字起こしに最適です。話者を区別することができるため、だれがいつ何を言ったのかを知ることができ、参加者は会議に集中して次のステップにすばやく進むことができます。 この機能により、アクセシビリティも向上します。 文字起こしがあると、聴覚に障碍がある人が積極的に参加できます。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 2c3269a9e57c0392e152fedfb640b9fae91a4989
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025699"
---
# <a name="what-is-the-conversation-transcription-service"></a>会話の文字起こしサービスとは

会話の文字起こしサービスは、リアルタイムの音声認識、話者識別、およびダイアライゼーションを組み合わせた、Speech Services の高度な機能です。 会話の文字起こしサービスは、対面会議の文字起こしに最適です。話者を区別することができるため、だれがいつ何を言ったのかを知ることができ、参加者は会議に集中して次のステップにすばやく進むことができます。 この機能により、アクセシビリティも向上します。 文字起こしがあると、聴覚に障碍がある人が積極的に参加できます。   

会話の文字起こしサービスでは、カスタマイズ可能な音声モデルによって正確な認識を実現し、業界および企業固有の語彙を理解するように調整できます。 さらに、会話の文字起こしサービスと Speech Devices SDK を組み合わせることで、マルチマイク デバイスのエクスペリエンスを最適化できます。

>[!NOTE]
> 現時点では、会話の文字起こしサービスは小規模な会議での使用をお勧めします。 大規模な会議向けに会話の文字起こしサービスを拡張したい場合は、お問い合わせください。

次の図は、会話の文字起こしサービスと連携するハードウェア、ソフトウェア、およびサービスを示しています。

![会話の文字起こしサービスの入力の図](media/scenarios/conversation-transcription-service.png)

>[!IMPORTANT]
> 特定のジオメトリ構成で、7 つのマイクの循環配列が必要です。 仕様とデザインの詳細については、[Microsoft Speech Device SDK マイク](https://aka.ms/cts/microphone)に関するページを参照してください。 開発キットの詳細および購入については、[Microsoft Speech Device SDK の入手](https://aka.ms/cts/getsdk)に関するページを参照してください。

## <a name="get-started-with-conversation-transcription"></a>会話の文字起こしを使ってみる

会話の文字起こしサービスを使用するには、3 つの手順を実行する必要があります。

1. ユーザーから音声サンプルを収集します。
2. ユーザーの音声サンプルを使用して、ユーザー プロファイルを生成します。
3. Speech SDK を使用して、ユーザー (話者) を特定し、音声を文字起こしします。

## <a name="collect-user-voice-samples"></a>ユーザーの音声サンプルを収集する

最初の手順では、各ユーザーからオーディオ録音を収集します。 バックグラウンド ノイズのない静かな環境で、ユーザーの音声を録音する必要があります。 各オーディオ サンプルの推奨される長さは、30 秒から 2 分です。 オーディオ サンプルが長いほど、話者の識別精度が向上します。 オーディオは、16 KHz のサンプル レートのモノラル チャンネルである必要があります。

前述のガイダンス以外に、オーディオの録音方法と保存方法にも配慮してください。安全なデータベースをお勧めします。 次のセクションでは、Speech SDK で話者を認識するために使用されるユーザー プロファイルを生成するために、このオーディオがどのように使用されるかを説明します。

## <a name="generate-user-profiles"></a>ユーザー プロファイルを生成する

次に、収集したオーディオ録音を署名生成サービスに送信して、音声を検証し、ユーザー プロファイルを生成する必要があります。 [署名生成サービス](https://aka.ms/cts/signaturegenservice)は、ユーザー プロファイルの生成と取得を可能にする、REST API のセットです。

ユーザー プロファイルを作成するには、`GenerateVoiceSignature` API を使用する必要があります。 仕様の詳細とサンプル コードについては、以下を参照してください。

> [!NOTE]
> 現在、会話の文字起こしサービスは、`centralus` および `eastasia` リージョンで使用できます。

* [REST の仕様](https://aka.ms/cts/signaturegenservice)
* [会話の文字起こしサービスの使用方法](https://aka.ms/cts/howto)

## <a name="transcribe-and-identify-speakers"></a>文字起こしと話者の識別を行う

会話の文字起こしサービスでは、文字起こしの生成と話者の識別のための入力として、マルチチャンネル オーディオ ストリームとユーザー プロファイルを想定しています。 オーディオおよびユーザー プロファイル データは、Speech Devices SDK を使用して、会話の文字起こしサービスに送信されます。 前に説明したように、会話の文字起こしサービスを使用するには、7 つのマイクの循環配列と Speech Devices SDK が必要です。

>[!NOTE]
> 仕様とデザインの詳細については、[Microsoft Speech Device SDK マイク](https://aka.ms/cts/microphone)に関するページを参照してください。 開発キットの詳細および購入については、[Microsoft Speech Device SDK の入手](https://aka.ms/cts/getsdk)に関するページを参照してください。

会話の文字起こしサービスと Speech Devices SDK を使用する方法については、[会話の文字起こしサービスの使用方法](https://aka.ms/cts/howto)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Speech Devices SDK についてさらに詳しく学習する](speech-devices-sdk.md)
