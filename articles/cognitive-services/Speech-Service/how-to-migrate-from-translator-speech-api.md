---
title: Translator Speech API から Speech Service に移行する
titleSuffix: Azure Cognitive Services
description: アプリケーションを Translator Speech API から Speech Service に移行するにあたっては､このトピックを参考にしてください。
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: Speech
ms.topic: article
ms.date: 10/01/2018
ms.author: aahi
ms.openlocfilehash: b50cdc6978519c0ec9da447c324237c00577d9fd
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2018
ms.locfileid: "48885274"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Translator Speech API から Speech Service に移行する

アプリケーションを Microsoft Translator Speech API から [Speech Service](index.yml) に移行するにあたっては､この記事を参考にしてください。 このガイドでは、Translator Speech API と Speech Service の違いを簡単に説明し、アプリケーションの移行方法を提案します。

> [!NOTE]
> Speech Service によって Translator Speech API サブスクリプション キーが受け付けられることはありません。 新たな Speech Service サブスクリプションを開始する必要があります。

## <a name="comparison-of-features"></a>機能の比較

| 機能                                           | Translator Speech API                                  | Speech Service | 詳細                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| テキストに変換                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 音声に変換                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| グローバル エンドポイント                                   | :heavy_check_mark:                                              | :heavy_minus_sign:                 | 現在､Speech Service はグローバル エンドポイントを提供していません。 グローバル エンドポイントでは、最も近いリージョン固有エンドポイントにトラフィックに自動的に誘導するため、アプリケーションでの待機時間を短縮できます。                                                    |
| 地域のエンドポイント                                | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 接続時間の制限                             | 90 分                                               | SDK では無制限です。 Websocket 接続で 10 分                                                                                                                                                                                                                                                                                   |
| ヘッダーに Auth キー                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 1 つの要求での複数言語への翻訳 | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| SDK が使用可能                                    | :heavy_minus_sign:                                              | :heavy_check_mark:                 | 使用できる SDK については､[Speech Service のドキュメント](index.yml)をご覧ください。                                                                                                                                                    |
| Websocket 接続                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 言語 API                                     | :heavy_check_mark:                                              | :heavy_minus_sign:                 | Speech Service は、[Translator API の言語リファレンス](../translator-speech/languages-reference.md)記事に記載された同じ言語範囲をサポートしています。 |
| 不適切な表現のフィルターとマーカー                       | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 入力として .WAV/PCM                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 入力としてその他のファイルの種類                         | :heavy_minus_sign:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| 部分結果                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| タイミング情報                                       | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                 |
| 関連付け ID                                    | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Custom Speech モデル                              | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Speech Service には、音声認識を組織の一意のボキャブラリにカスタマイズできるカスタム音声モデルが用意されています。                                                                                                                                           |
| カスタム変換モデル                         | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Microsoft Text Translation API に登録すると、[Custom Translator](https://www.microsoft.com/translator/business/customization/) (現在プレビュー) により、独自のデータを使ってより正確な翻訳ができます。                                                 |

## <a name="migration-strategies"></a>移行方法

Translator Speech API を使用しているアプリケーションを開発中の場合､あるいはそうしたアプリケーションを運用している場合は､Speech Service を使用するように更新することをお勧めします。 使用できる SDK やサンプル コード､チュートリアルについては､[Speech Service](index.yml) のドキュメントをご覧ください。 次には、移行する場合の考慮事項をいくつか示します。

* 現在､Speech Service はグローバル エンドポイントを提供していません。 アプリケーションの全トラフィックに同じ地域エンドポイントを使用したときにアプリケーションが効率的に機能するかどうかは､お客様自身が判断する必要があります。 効率的に機能しない場合は､geolocation を使って最も効率的なエンドポイントを探す必要があります。

* アプリケーションが長時間の接続を使用し､現在ある SDK を使用できない場合は､Websockets 接続を利用し､適切なタイミングで接続し直すことで 10 分間のタイムアウト制限に対処することができます。

* アプリケーションで Translator Text API と Translator Speech API を使用してカスタム変換を有効にしている場合は、Speech Service を使用して直接「カテゴリ」の ID を追加することができます。

* Speech Sevice では、Translator Speech API とは異なり、1 つの要求で複数の言語への翻訳を完了できます。

## <a name="next-steps"></a>次の手順

* [Speech Service を無料で試す](get-started.md)
* [クイック スタート: UWP アプリで Speech SDK を使用して音声を認識する](quickstart-csharp-uwp.md)

## <a name="see-also"></a>関連項目

* [Speech Service とは](overview.md)
* [Speech Service および Speech SDK のドキュメント](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
