---
title: Speech Studio の概要 - 音声サービス
titleSuffix: Azure Cognitive Services
description: Speech Studio は、アプリケーションで Azure 音声サービスの機能を構築および統合するための UI ベースのツールのセットです。
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/07/2021
ms.author: eur
ms.openlocfilehash: dacc8a9938537ca85b624dec044ef11567d8b456
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131509367"
---
# <a name="what-is-speech-studio"></a>Speech Studio とは

[Speech Studio](https://speech.microsoft.com) は、アプリケーションで Azure 音声サービスの機能を構築および統合するための UI ベースのツールのセットです。 コーディングなしのアプローチを使用して Speech Studio でプロジェクトを作成し、[Speech SDK](speech-sdk.md)、[Speech CLI](spx-overview.md)、またはさまざまな REST API を使用して、アプリケーション内に作成したアセットを参照します。

## <a name="set-up-your-azure-account"></a>Azure アカウントの設定

[Speech Studio](https://speech.microsoft.com) を使用する前に、Azure アカウントを用意して、音声サービス リソースを追加する必要があります。 アカウントとリソースがない場合は、[Speech Service を無料でお試しください](overview.md#try-the-speech-service-for-free)。

Azure アカウントと音声サービス リソースを作成したら、次を実行します。

1. Azure アカウントで [Speech Studio](https://speech.microsoft.com) にサインインします。
1. 開始するために必要な音声サービス リソースを選択します。 (リソースは、上部メニューの [設定] でいつでも変更できます。)

## <a name="speech-studio-features"></a>Speech Studio の機能

次の音声サービスの機能は、Speech Studio でプロジェクト タイプとして使用できます。

* **リアルタイム音声テキスト変換**: コードを使用せずにオーディオ ファイルをドラッグ アンド ドロップすることで、音声テキスト変換をすばやくテストします。 これはオーディオ サンプルでの音声テキスト変換のしくみを確認するためのデモ ツールですが、使用可能なすべての機能を調べるには、音声テキスト変換の[概要](speech-to-text.md)を参照してください。
* **Custom Speech**: Custom Speech を使用すると、特定のボキャブラリ セットと話し方に合わせて調整された音声認識モデルを作成できます。 基本音声認識モデルを使用する場合と異なり、Custom Speech モデルは、パブリックにアクセスできないので、独自の競争上の強みの一部になります。 サンプル オーディオのアップロードを開始して、Custom Speech モデルを作成するには、[クイックスタート](how-to-custom-speech-test-and-train.md)を参照してください。
* **発音評価**: 発音評価によりスピーチの発音が評価され、話された音声の正確性と流暢性に関するフィードバックが話者に提供されます。 Speech Studio には、コーディングなしでこの機能をすばやくテストするサンドボックスが用意されていますが、アプリケーションの Speech SDK でこの機能を使用する方法については[ハウツー](how-to-pronunciation-assessment.md)記事を参照してください。
* **音声ギャラリー**: 自然に話すアプリとサービスを構築します。 70 の言語とバリエーションで 170 を超える音声から選択できます。 豊富な表現力と人間のようなニューラル音声でシナリオを実現します。
* **Custom Voice**: Custom Voice を使用すると、テキスト読み上げ用の独自のカスタム音声を作成できます。 Speech Studio でオーディオ ファイルを指定し、対応する文字起こしを作成してから、アプリケーションでカスタム音声を使用します。 カスタム音声を作成してエンドポイント経由で使用する方法に関する[ハウツー](how-to-custom-voice-create-voice.md)記事を参照してください。 
* **Audio Content Creation**: [Audio Content Creation](how-to-audio-content-creation.md) は、オーディオブック、ニュース放送、ビデオ ナレーション、チャットボットなどのさまざまなシナリオ向けの非常に自然なオーディオ コンテンツを作成できる、使いやすいツールです。 Speech Studio を使用すると、作成したオーディオ ファイルをエクスポートして、アプリケーションで使用できます。
* **Custom Keyword**: Custom Keyword は、製品を音声でアクティブにできる単語または短い語句です。 Speech Studio で Custom Keyword を作成し、アプリケーションに [Speech SDK で使用](custom-keyword-basics.md)するバイナリ ファイルを生成します。
* **Custom Commands**: Custom Commands を使用すると、音声優先の対話エクスペリエンス向けに最適化された、機能が豊富な音声コマンド処理アプリを簡単に構築できます。 これにより、Speech Studio でのコーディング不要な作成エクスペリエンスと、自動ホスティング モデルが提供され、複雑度が比較的低くなるので、音声コマンド処理シナリオに最適なソリューションの構築に専念できるようになります。 Custom Commands アプリケーションの構築に関する[ハウツー](how-to-develop-custom-commands-application.md) ガイドを参照してください。また、[Custom Commands アプリケーションと Speech SDK の統合](how-to-custom-commands-setup-speech-sdk.md)に関するガイドも参照してください。

## <a name="next-steps"></a>次のステップ

[Speech Studio を調査](https://speech.microsoft.com)し、プロジェクトを作成します。




