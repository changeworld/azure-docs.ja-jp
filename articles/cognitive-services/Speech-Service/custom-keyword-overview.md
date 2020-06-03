---
title: カスタム キーワード - Speech サービス
titleSuffix: Azure Cognitive Services
description: Speech Software Development Kit (SDK) を使用したカスタム キーワードの特徴、機能、制限の概要。
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: hasshah
ms.openlocfilehash: 0a6df626f9567b51a8371c17004b454f9c5b4597
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202047"
---
# <a name="what-is-a-keyword"></a>キーワードとは

キーワードは、音声で製品を有効できるようにする単語または短い語句です。 たとえば、"コルタナさん" は、Cortana アシスタントのキーワードです。 ボイス アクティベーションを使用すると、ユーザーがキーワードを話すだけで、完全にハンドフリーで製品の操作を開始することができます。 製品ではキーワードが継続的にリッスンされているため、データを可能な限りプライベートに保つために、すべての音声は検出が行われるまで、ユーザーのデバイスでローカルに処理されます。

## <a name="core-features-of-custom-keyword"></a>カスタム キーワードのコア機能

カスタム キーワードのカスタマイズ、パフォーマンス、および統合機能を使用すると、お客様の製品のビジョンとユーザーのニーズに合わせてボイス アクティベーションを調整できます。

| 機能 | 説明 |
|----------|----------|
| キーワードのカスタマイズ | ブランドの延長として、キーワードは、お客様と築き上げてきたブランド エクイティを強化します。 Speech Studio のカスタム キーワード ポータルでは、ブランドを最もよく表す単語または短い語句を指定できます。 適切な発音を選択してキーワードをさらにカスタマイズでき、これは生成されたキーワード モデルで採用されます。
| キーワード検証 | ローカルで検出されたキーワードの信頼度が高い場合は、ユーザーがキーワードを発したことを確認するために、音声がクラウドに送信されます。 キーワード検証では、不適切なローカル検出による影響を軽減し、ユーザーのプライバシーを保護することで、セキュリティを強化することができます。
| 音声アシスタントと Speech SDK の統合 | Speech Studio のカスタム キーワードで生成されたキーワードは、Speech SDK を使用してデバイスまたはアプリケーション内に簡単に統合できます。 SDK を Speech Studio で提供されているキーワード モデルにポイントするだけで、キーワード検証を使用して音声で製品を有効にできるようになります。 独自の[音声アシスタント](voice-assistants.md)を作成して、製品の音声エクスペリエンスを完成させることができます。

## <a name="get-started-with-custom-keywords"></a>カスタム キーワードの概要

* チュートリアル:[Speech Studio を使用してカスタム キーワードを作成する](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws)方法
* チュートリアル:[C# と Speech SDK を使用して音声で製品を有効にする](tutorial-voice-enable-your-bot-speech-sdk.md)方法
* クイック スタート:[ユニバーサル Windows プラットフォームで C# と Speech SDK を使用してキーワードを認識する](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer)
* クイック スタート:[Android で Java と Speech SDK を使用してキーワードを認識する](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer)

## <a name="next-steps"></a>次のステップ

* [Speech サービスのサブスクリプション キーを無料で取得する](get-started.md)
* [Speech SDK を取得する](speech-sdk.md)
* [音声アシスタントの詳細情報を確認する](voice-assistants.md)
