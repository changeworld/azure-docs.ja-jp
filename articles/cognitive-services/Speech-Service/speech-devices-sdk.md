---
title: Speech Devices SDK について - Speech Services
titleSuffix: Azure Cognitive Services
description: Speech Devices SDK を使ってみましょう。 Speech Services は、さまざまなデバイスやオーディオ ソースと連携します。 適合するハードウェアやソフトウェアで音声アプリケーションを次のレベルに引き上げることができます。 Speech Devices SDK は、専用マイク配列開発キットとの組み合わせで事前に調整されたライブラリです。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 44e993375b17960ad5d9ced11d37ed3701a58f73
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53101740"
---
# <a name="about-the-speech-devices-sdk-preview"></a>Speech Devices SDK (プレビュー) について

[Speech Service](overview.md) は、さまざまなデバイスやオーディオ ソースと連携します。 適合するハードウェアやソフトウェアで音声アプリケーションを次のレベルに引き上げることができます。 Speech Devices SDK は、専用マイク配列開発キットとの組み合わせで事前に調整されたライブラリです。

Speech Devices SDK は、次の場合に役立ちます。
* 新しい音声シナリオをすばやくテストする。
* クラウド ベースの Speech サービスをより簡単にデバイスに統合する。
* 顧客向けに優れたユーザー エクスペリエンスを作成する。

Speech Devices SDK は、[Speech SDK](speech-sdk.md) を利用しています。 Speech SDK を使って、Microsoft の先進の音声処理アルゴリズムによって処理された音声をデバイスのマイク配列から [Speech サービス](overview.md)に送信します。 マルチチャンネル オーディオを使用して、ノイズの抑制、エコー キャンセレーション、ビームフォーミング、残響除去によってより正確な遠距離[音声認識](speech-to-text.md)が実現されます。

Speech Devices SDK を使用すると、[ウェイク ワードを自由にカスタマイズ](speech-devices-sdk-create-kws.md)してアンビエント デバイスを構築できるため、ユーザー操作を開始するキューがブランド固有のものになります。

Speech Devices SDK を使用すると、ドライブスルー注文システム、店舗内または家庭内アシスタント、スマート スピーカーなど、さまざまな音声対応シナリオが容易になります。 ユーザーへのテキストでの応答、既定の音声または[カスタム音声](how-to-customize-voice-font.md)での応答、検索結果の提供、他の言語への[翻訳](speech-translation.md)などを行うことができます。 皆様の開発の成果を楽しみにしております。

## <a name="development-kit-providers"></a>開発キット プロバイダー

現在、このような完全なエンドツーエンド システム リファレンス設計を使用できます。

|||
|-|-|
|[![ROOBO のロゴ](media/speech-devices-sdk/roobo-logo.png)](http://ddk.roobo.com/)|ROOBO は、家電製品、自動車、ロボット、玩具などの業界向けの完全な人工知能 (AI) システム ソリューションを提供しています。 ROOBO のリファレンス デザインは、Microsoft Speech Service との統合によって、開発から市場投入までの時間を大幅に短縮します。 [ROOBO のページを参照](http://ddk.roobo.com/)してください。|

## <a name="next-steps"></a>次の手順

使い始めるために、[無料の Azure アカウント](https://azure.microsoft.com/free/ai/)を取得し、Speech Devices SDK にサインアップします。

> [!div class="nextstepaction"]
> [Speech Devices SDK にサインアップする](get-speech-devices-sdk.md)
