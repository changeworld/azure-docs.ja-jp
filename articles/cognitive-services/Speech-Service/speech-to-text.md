---
title: 音声テキスト変換の概要 - 音声サービス
titleSuffix: Azure Cognitive Services
description: 音声テキスト変換ソフトウェアを使用すると、オーディオ ストリームをテキストにリアルタイムで文字起こしできます。 アプリケーション、ツール、またはデバイスで、このテキスト入力を使用、表示することや、このテキスト入力に基づきアクションを実行することができます。 この記事は、音声テキスト変換サービスの利点と機能の概要になっています。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: trbye
ms.custom: cog-serv-seo-aug-2020
keywords: 音声テキスト変換、音声テキスト変換ソフトウェア
ms.openlocfilehash: 3450d39729096bfc3077f51e2069f8f102e571a5
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449394"
---
# <a name="what-is-speech-to-text"></a>音声変換の概要

この概要では、音声テキスト変換サービスの利点と機能について説明します。
音声テキスト変換 (音声認識とも呼ばれる) を使用すると、オーディオ ストリームをテキストにリアルタイムで文字起こしできます。 アプリケーション、ツール、またはデバイスで、このテキストを使用、表示することや、このテキストをコマンド入力としてアクションを実行することができます。 このサービスでは、Microsoft が Cortana や Office 製品に対して使用するのと同じ認識テクノロジが採用されています。 これは、<a href="./speech-translation.md" target="_blank">翻訳 </a> および<a href="./text-to-speech.md" target="_blank">テキスト読み上げ </a> サービス オファリングとシームレスに連携します。 使用可能な音声変換の言語の詳細については、[サポートされる言語](language-support.md#speech-to-text)に関するページを参照してください。

既定では、音声変換サービスでは、汎用言語モデルが使用されます。 このモデルは、Microsoft が所有するデータでトレーニングされて、クラウドにデプロイされています。 このモデルは、会話や口述のシナリオに最適です。 独自の環境で認識と文字起こしのために音声変換を使用している場合は、カスタムの音響、言語、および発音モデルを作成してトレーニングできます。 カスタマイズは、アンビエント ノイズや業界固有の語彙に対処する場合に役立ちます。

このドキュメントには、次の種類の記事が含まれています。

* **クイックスタート** は、サービスへの要求の実行方法を説明する概要手順です。
* **攻略ガイド** には、より具体的またはカスタマイズした方法でサービスを使用するための手順が記載されています。
* **概念** では、サービスの機能と特徴について詳しく説明します。
* **チュートリアル** はより長文のガイドであり、より広範なビジネス ソリューションの 1 コンポーネントとしてこのサービスを使用する方法を示すものです。

> [!NOTE]
> Bing Speech は、2019 年 10 月 15 日に使用が停止されました。 アプリケーション、ツール、または製品で Bing Speech API を使用している場合、Speech サービスへの移行に役立つガイドが作成されています。
> - [Bing Speech から Speech Service に移行する](how-to-migrate-from-bing-speech.md)

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="get-started"></a>作業開始

音声テキスト変換の使用を開始するには、[クイックスタート](get-started-speech-to-text.md)を参照してください。 サービスは、[Speech SDK](speech-sdk.md)、[REST API](rest-speech-to-text.md#pronunciation-assessment-parameters)、および [Speech CLI](spx-overview.md) を介して利用できます。

## <a name="sample-code"></a>サンプル コード

Speech SDK のサンプル コードは、GitHub 上で入手できます。 これらのサンプルでは、ファイルやストリームからの音声の読み取り、連続的な認識と単発の認識、カスタム モデルの使用など、一般的なシナリオについて説明されています。

- [音声変換のサンプル (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [バッチ文字起こしのサンプル (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [発音評価サンプル (REST)](rest-speech-to-text.md#pronunciation-assessment-parameters)

## <a name="customization"></a>カスタマイズ

標準の Speech Service モデルに加えて、カスタム モデルも作成できます。 カスタマイズにより、話し方、ボキャブラリ、背景ノイズなどの音声認識の障壁を克服できます。「[カスタム音声](./custom-speech-overview.md)」を参照してください。 カスタマイズのオプションは、言語やロケールによって異なります。[サポートされる言語](./language-support.md)に関するページを参照して、サポートについて確認してください。

## <a name="batch-transcription"></a>バッチ文字起こし

バッチ文字起こしは、ストレージ内の大量のオーディオを文字起こしできる一連の REST API 操作です。 Shared Access Signatures (SAS) URI でオーディオ ファイルを示して、非同期に文字起こしの結果を受け取ることができます。 Batch Transcription API の使用方法の詳細については、[使用方法](batch-transcription.md)に関するページを参照してください。

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>次のステップ

- [Speech サービスのサブスクリプション キーを無料で取得する](overview.md#try-the-speech-service-for-free)
- [Speech SDK を取得する](speech-sdk.md)