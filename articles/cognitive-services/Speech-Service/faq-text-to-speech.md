---
title: Azure の Speech to Text サービスに関してよく寄せられる質問
description: Speech to Text に関してよく寄せられる質問に対する回答を紹介します。
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 8d70c4a359c713d6c5f46423193e9c9e7e1f3baf
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282859"
---
# <a name="text-to-speech-frequently-asked-questions"></a>テキスト読み上げについてよく寄せられる質問

疑問点への回答がこの FAQ で見つからない場合は、[こちら](support.md)で他のサポート オプションを確認してください。

## <a name="general"></a>全般

**質問**: 標準音声モデルとカスタム音声モデルの違いは何ですか。

**回答**: 標準音声モデル (音声フォント とも呼ばれます) は Microsoft が所有するデータでトレーニングされており、既にクラウド内にデプロイされています。 カスタム音声モデルを使用すると、ユーザーは平均的なモデルを適応し、話者の音声スタイルに従って音色や表現方法を転送するか、またはユーザーによって準備されたトレーニング データに基づいて完全な新しいモデルをトレーニングできます。 今日、ますます多くの顧客が、ボットに独自のブランド化された音声を備えたいと考えています。 カスタム音声構築プラットフォームは、そのための適切な選択肢です。

**質問**: 標準音声モデルを使用しようとする場合は、どこから開始すればよいですか。

**回答**: 45 以上の言語の 80 を超える標準音声モデルが HTTP 要求経由で使用できます。 まず、[サブスクリプション キー](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started)を取得する必要があります。 事前にデプロイされた音声モデルへの REST 呼び出しを実行するには、[ここで詳細を](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech)確認してください。

**質問**: カスタマイズされた音声モデルを使用しようとする場合、API は標準音声と同じですか。

**回答**: カスタム音声モデルが作成され、デプロイされている場合は、使用しているモデルのための固有のエンドポイントが取得されます。 音声を使用してアプリで話すには、HTTP 要求でそのエンドポイントを指定する必要があります。 カスタム エンドポイントでは、Text-to-Speech サービスの REST API 経由で使用可能なのと同じ機能も使用できます。 [カスタム エンドポイントを作成して使用する](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-endpoint)方法に関するページを参照してください。

**質問**: カスタム音声モデルを作成するために、自分でトレーニング データを準備する必要がありますか。

**回答**: 自分でトレーニング データを準備する必要があります。 カスタマイズされた音声モデルを作成するには、音声データのコレクションが必要です。 このコレクションは、音声録音の一連のオーディオ ファイルと、各オーディオ ファイルの文字起こしのテキスト ファイルで構成されます。 デジタル音声の結果は、トレーニング データの品質に大きく依存します。 良質の TTS 音声を生成するには、録音が、静かな部屋で高品質のスタンド マイクを使用して行われることが重要です。 一定の音量、話す速さ、ピッチ、さらには話の表現方法における一貫性までもが、優れたデジタル音声の構築には不可欠です。 音声は録音スタジオで録音するようにすることを強くお勧めします。
現時点では、オンライン録音のサポートは提供しておらず、録音スタジオに関する推奨事項もありません。 形式に関する要件については、[録音および文字起こしを準備する方法](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/how-to-customize-voice-font#prepare-recordings-and-transcripts)を参照してください。
 
**質問**: カスタム音声トレーニングのために音声データを録音するには、どのようなスクリプトを使用すればよいですか。 

**回答**: 音声録音のためのスクリプトに制限はありません。 独自のスクリプトを使用して音声を録音できます。 音声データに十分な音声カバレッジがあることだけは確認してください。 カスタム音声をトレーニングするには、50 個の異なる文 (約 3 ～ 5 分の音声) 程度の少量の音声データで始めることができます。 提供するデータが多いほど、音声はより自然になります。 2000 を超える文 (約 3 ～ 4 時間の音声) の録音を提供すると、完全な音声フォントをトレーニングし始めることができます。 高品質の完全な音声を得るには、6000 を超える文 (約 8 ～ 10 時間の音声) の録音を準備する必要があります。  
録音用のスクリプトの準備に役立つ追加のサービスを提供しています。 詳細については、[Custom Voice カスタマー サポート](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation)に問い合わせてください。

**質問**: 既定値またはポータルで提供されているものより高い同時実行性が必要な場合はどうすればよいですか。

**回答**: モデルは同時要求を 20 ずつ処理するようスケールアップできます。 より高いスケーリングの詳細については、[Custom Voice カスタマー サポート](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation)に問い合わせてください。

**質問**: モデルをダウンロードしてローカルで実行できますか。

**回答**: モデルをダウンロードしてローカルで実行することはできません。

## <a name="next-steps"></a>次の手順

* [トラブルシューティング](troubleshooting.md)
* [リリース ノート](releasenotes.md)
