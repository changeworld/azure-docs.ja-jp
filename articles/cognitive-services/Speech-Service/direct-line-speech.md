---
title: Direct Line Speech - 音声サービス
titleSuffix: Azure Cognitive Services
description: Direct Line Speech と Speech SDK (ソフトウェア開発キット) を使用した音声アシスタントの特徴、機能、制限の概要。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: cec3131d791d591375fd87d1c080294c9034a815
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806220"
---
# <a name="about-direct-line-speech"></a>Direct Line Speech について

[音声アシスタント](voice-assistants.md)はユーザーの音声を聞き取り、応答としてアクションを実行します (多くの場合は音声で応答します)。 これは、[音声テキスト変換](speech-to-text.md)を使用してユーザーの音声を文字に変換し、そのテキストの自然言語理解に対してアクションを実行します。 このアクションには、多くの場合、[テキスト読み上げ](text-to-speech.md)で生成されたアシスタントからの音声出力が含まれます。 デバイスは、Speech SDK の `DialogServiceConnector` オブジェクトを使用してアシスタントに接続します。

**Direct Line Speech** は、柔軟で拡張可能な音声アシスタントを作成するための、堅牢なエンドツーエンドのソリューションです。このようなアシスタントは、Bot Framework とその Direct Line Speech チャネルによって実現され、音声によるボットとの対話のために最適化されます。

Direct Line Speech は、音声アシスタントのための最高レベルのカスタマイズと精緻化を提供します。 タスクの完了または指示と制御が目的の、自由形式な、自然な、またはその両方の性質を持った会話のシナリオに適しています。 このような高度の柔軟性はさらなる複雑化を伴うため、自然言語入力を使用して十分に定義されたタスクを扱うシナリオでは、ソリューションのエクスペリエンスを簡素化するために、[カスタム コマンド (プレビュー)](custom-commands.md) を検討することができます。

## <a name="getting-started-with-direct-line-speech"></a>Direct Line Speech の使用を開始する

Direct Line Speech を使用して音声アシスタントを作成する最初の手順では、[Speech のサブスクリプション キーを取得](get-started.md)し、そのサブスクリプションに関連付けられた新しいボットを作成し、そのボットを Direct Line Speech チャネルに接続します。

   ![Direct Line Speech のオーケストレーション サービス フローの概念図](media/voice-assistants/overview-directlinespeech.png "Speech チャネルのフロー")

Direct Line Speech を使用して簡単な音声アシスタントを作成するための完全なステップ形式ガイドについては、[Speech SDK と Direct Line Speech チャネルを使用してボットを音声対応にするチュートリアル](tutorial-voice-enable-your-bot-speech-sdk.md)を参照してください。

10 分もかからずにコードを実行できるように設計されたクイック スタートも用意されています。 以下の表は、音声アシスタントのクイックスタートの一覧を言語別に整理して示しています。

| クイック スタート | プラットフォーム | API リファレンス |
|------------|----------|---------------|
| C#、UWP | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| Java | Windows、macOS、Linux | [Browse](https://aka.ms/csspeech/javaref) |
| Java | Android | [Browse](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>サンプル コード

音声アシスタントを作成するためのサンプル コードは GitHub から入手できます。 これらのサンプルは、いくつかの一般的なプログラミング言語でアシスタントに接続するためのクライアント アプリケーションに対応しています。

* [音声アシスタントのサンプル (SDK)](https://aka.ms/csspeech/samples)
* [チュートリアル:Speech SDK を使用してアシスタントを音声対応にする (C#)](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>カスタマイズ

音声サービスを使用して構築された音声アシスタントでは、[音声変換](speech-to-text.md)、[テキスト読み上げ](text-to-speech.md)、および[カスタム キーワードの選択](speech-devices-sdk-create-kws.md)に利用できるさまざまなカスタム オプションを使用できます。

> [!NOTE]
> カスタマイズのオプションは、言語やロケールによって異なります ([サポートされる言語](supported-languages.md)に関するページを参照してください)。

Direct Line Speech と音声アシスタントのための関連する機能は、[仮想アシスタント ソリューションとエンタープライズ テンプレート](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)に最適な補足機能です。 Direct Line Speech は任意の互換性のあるボットで機能しますが、これらのリソースは、高品質の会話エクスペリエンスだけでなく、すぐに開始できる一般的なサポート スキルおよびモデルに再利用できるベースラインとして役立ちます。

## <a name="reference-docs"></a>リファレンス ドキュメント

* [Speech SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>次の手順

* [Speech サービスのサブスクリプション キーを無料で取得する](get-started.md)
* [Speech SDK を取得する](speech-sdk.md)
* [基本ボットの作成とデプロイ](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [仮想アシスタント ソリューションとエンタープライズ テンプレートの入手](https://github.com/Microsoft/AI)
