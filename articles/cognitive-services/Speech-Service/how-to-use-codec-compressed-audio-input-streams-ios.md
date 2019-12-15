---
title: iOS で Speech SDK を使用してコーデック圧縮オーディオをストリーミングする
titleSuffix: Azure Cognitive Services
description: iOS で Speech SDK を使用して圧縮オーディオを Speech Service にストリーミングする方法について学習します。
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: chlandsi
ms.openlocfilehash: 2089f4191ddd57fa8dc19862bd195756c166f2d4
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805860"
---
# <a name="how-to-use-codec-compressed-audio-input-with-the-speech-sdk-on-ios"></a>方法:iOS で Speech SDK を使用してコーデック圧縮オーディオ入力を使用する

Speech SDK の**圧縮オーディオ入力ストリーム** API では、プル ストリームまたはプッシュ ストリームを使用して、圧縮オーディオを Speech Services にストリーミングする方法を提供します。

> [!IMPORTANT]
> iOS で圧縮オーディオをストリーミングするには、Speech SDK バージョン 1.7.0 以上が必要です。 また、[Linux (Ubuntu 16.04、Ubuntu 18.04、Debian 9) では C++、C#、Java](how-to-use-codec-compressed-audio-input-streams.md)、[Android では Java](how-to-use-codec-compressed-audio-input-streams-android.md) でもサポートされています。

wav/PCM については、メインライン音声のドキュメントを参照してください。 wav/PCM 以外では、次のコーデック圧縮入力形式がサポートされています。

- MP3
- OPUS/OGG
- FLAC
- wav コンテナー内の ALAW
- wav コンテナー内の MULAW

## <a name="prerequisites"></a>前提条件

圧縮オーディオの処理は、[GStreamer](https://gstreamer.freedesktop.org) を使用して実装されます。 ライセンスの理由により、これらの関数を SDK に付属させることはできませんが、これらの関数を含むラッパー ライブラリは、アプリケーション開発者によって構築され、SDK を使用してアプリに付属させる必要があります。

このラッパー ライブラリを構築するには、まず [GStreamer SDK](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg) をダウンロードしてインストールします。 次に、[ラッパー ライブラリ](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper)の Xcode プロジェクトをダウンロードします。

Xcode でプロジェクトを開いて、**汎用 iOS デバイス** ターゲット用に構築します。これは、特定のターゲット用に構築することはできません。

この構築のステップでは、`GStreamerWrapper.framework` の名前のすべての必要なアーキテクチャに対して、ダイナミック ライブラリを含むダイナミック フレームワーク バンドルが生成されます。

このフレームワークは、Speech Service SDK で圧縮オーディオ ストリームを使用するすべてのアプリに含まれている必要があります。

Xcode プロジェクトに次の設定を適用してこれを完了します。

1. 構築した `GStreamerWrapper.framework` および[ここ](https://aka.ms/csspeech/iosbinary)からダウンロードできる Cognitive Services Speech SDK のフレームワークの両方を、サンプル プロジェクトを含むディレクトリにコピーします。
1. _[プロジェクトの設定]_ で、フレームワークへのパスを調整します。
   1. **[Embedded Binaries]\(埋め込みバイナリ\)** ヘッダーの下の **[全般]** タブで、フレームワークとして SDK ライブラリを追加します **[Add embedded binaries]/(埋め込みバイナリの追加/)**  >  **[その他の追加]** に移動し、選択したディレクトリに移動して、両方のフレームワークを選択します。
   1. **[Build Settings]** タブに移動し、 **[All]** の設定をアクティブにします。
1. ディレクトリ `$(SRCROOT)/..` を、 **[Search Paths]** 見出しの下にある _[Framework Search Paths]_ に追加します。

## <a name="example-code-using-codec-compressed-audio-input"></a>コーデック圧縮オーディオ入力を使用するコード例

圧縮オーディオ形式で Speech Services にストリーミングするには、`SPXPullAudioInputStream` または `SPXPushAudioInputStream` を作成します。

以下のスニペットは、ストリームの圧縮形式として mp3 を指定して、`SPXPushAudioInputStream` のインスタンスから `SPXAudioConfiguration` を作成する方法を示しています。

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=66-77&highlight=2-11)]

次のスニペットは、圧縮オーディオ データをファイルから読み取り、`SPXPushAudioInputStream` に追加する方法を示しています。

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=105-151&highlight=19-44)]

## <a name="next-steps"></a>次の手順

- [Speech 試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
- [Java で音声を認識する方法を確認する](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
