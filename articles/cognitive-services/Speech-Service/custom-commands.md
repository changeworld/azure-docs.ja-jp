---
title: カスタム コマンド (プレビュー) - 音声サービス
titleSuffix: Azure Cognitive Services
description: 音声アプリケーションを作成するためのソリューションであるカスタム コマンド (プレビュー) の特徴、機能、および制限の概要。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 9349969f1f9b037a271cb727233517daba999acd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446979"
---
# <a name="custom-commands-preview"></a>カスタム コマンド (プレビュー)

[音声アシスタント](voice-assistants.md)などの音声アプリケーションでは、ユーザーの音声を聞き取り、応答としてアクションを実行します (多くの場合は、音声で応答します)。 これは、[音声テキスト変換](speech-to-text.md)を使用してユーザーの音声を文字に変換し、そのテキストの自然言語理解に対してアクションを実行します。 このアクションには、多くの場合、[テキスト読み上げ](text-to-speech.md)で生成されたアシスタントからの音声出力が含まれます。 デバイスは、Speech SDK の `DialogServiceConnector` オブジェクトを使用してアシスタントに接続します。

**カスタム コマンド (プレビュー)** は、音声アプリケーションを作成するための簡素化されたソリューションです。 これにより、統一された作成エクスペリエンスと自動ホスティング モデルが提供され、[Direct Line Speech](direct-line-speech.md) など他のオプションと比べて複雑さが低くなっています。 ただし、この簡素化と引き替えに柔軟性が低下します。 そのため、カスタム コマンド (プレビュー) は、タスクの完了やコマンドと制御のシナリオに最適です。 特に、モノのインターネット (IoT) やヘッドレス デバイスに適しています。

複雑な対話操作や、[仮想アシスタント ソリューションおよびエンタープライズ テンプレート](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)などの他のソリューションとの統合については、Direct Line Speech を使用することをお勧めします。

カスタム コマンド (プレビュー) の適切な候補には、適切に定義された変数セットを持つ固定の語彙があります。 たとえば、サーモスタットの制御などのホーム オートメーションのタスクが理想的です。

   ![タスク完了シナリオの例](media/voice-assistants/task-completion-examples.png "タスク完了の例")

## <a name="getting-started-with-custom-commands-preview"></a>カスタム コマンド (プレビュー) の開始

カスタム コマンド (プレビュー) を使用して音声アプリケーションを作成する最初の手順として、[音声サブスクリプション キーを取得し](get-started.md)、[Speech Studio](https://speech.microsoft.com) のカスタム コマンド (プレビュー) ビルダーにアクセスします。 そこから、新しいカスタム コマンド (プレビュー) アプリケーションを作成して発行することができます。その後、デバイス上のアプリケーションが Speech SDK を使用してそれと通信できるようになります。

   ![カスタム コマンド (プレビュー) の作成フロー](media/voice-assistants/custom-commands-flow.png "カスタム コマンド (プレビュー) の作成フロー")

10 分もかからずにコードを実行できるように設計されたクイック スタートが用意されています。

* [カスタム コマンド (プレビュー) アプリケーションを作成する](quickstart-custom-speech-commands-create-new.md)
* [パラメーターを使用してカスタム コマンド (プレビュー) アプリケーションを作成する](quickstart-custom-speech-commands-create-parameters.md)
* [Speech SDK、C# を使用してカスタム コマンド (プレビュー) アプリケーションに接続する](quickstart-custom-speech-commands-speech-sdk.md)

クイックスタートを完了したら、操作方法について確認します。

- [カスタム コマンド パラメーターに検証を追加する](./how-to-custom-speech-commands-validations.md)
- [Speech SDK を使用してクライアント上でコマンドを実行する](./how-to-custom-speech-commands-fulfill-sdk.md)
- [カスタム コマンドに確認を追加する](./how-to-custom-speech-commands-confirmations.md)
- [カスタム コマンドにワンステップ修正を追加する](./how-to-custom-speech-commands-one-step-correction.md)

## <a name="next-steps"></a>次のステップ

* [Speech サービスのサブスクリプション キーを無料で取得する](get-started.md)
* [カスタム コマンドを試行するために Speech Studio にアクセスする](https://speech.microsoft.com)
* [Speech SDK を取得する](speech-sdk.md)
