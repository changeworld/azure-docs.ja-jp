---
title: クイック スタート:マイクから音声を認識する、C# (UWP) - Speech サービス
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 5f83f249228c96021979edc14c092eded03b96e4
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818669"
---
## <a name="prerequisites"></a>前提条件

開始する前に、以下の操作を行います。

> [!div class="checklist"]
> * [Azure Speech リソースを作成する](../../../../get-started.md)
> * [使用する開発環境を設定する](../../../../quickstarts/setup-platform.md?tabs=uwp)
> * [空のサンプル プロジェクトを作成する](../../../../quickstarts/create-project.md?tabs=uwp)
> * オーディオ キャプチャ用のマイクにアクセスできることを確認する

既にこれを行っている場合は、これで完了です。 続けましょう。

## <a name="open-your-project-in-visual-studio"></a>Visual Studio でプロジェクトを開きます。

最初の手順として、ご利用のプロジェクトを Visual Studio で開いていることを確認します。

## <a name="start-with-some-boilerplate-code"></a>スケルトン コードを使用して開始する

このプロジェクトのスケルトンとして機能するコードを追加してみましょう。

1. **ソリューション エクスプローラー**で、`MainPage.xaml` を開きます。

2. デザイナーの XAML ビューで、次の XAML スニペットを **Grid** タグに挿入します (`<Grid>` と `</Grid>` の間)。

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml#StackPanel)]

3. **ソリューション エクスプローラー**で、コードビハインドのソース ファイル `MainPage.xaml.cs` を開きます (`MainPage.xaml` にグループ化されています)。

4. コードを次の基本コードに置き換えます。

   [!code-csharp[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=6-50,55-56,94-154)]

## <a name="create-a-speech-configuration"></a>Speech 構成を作成する

`SpeechRecognizer` オブジェクトを初期化するには、サブスクリプション キーとサブスクリプション リージョンを使用する構成を作成する必要があります。 このコードを `RecognizeSpeechAsync()` メソッドに挿入します。

> [!NOTE]
> このサンプルでは、`FromSubscription()` メソッドを使用して `SpeechConfig` をビルドします。 使用可能なメソッドの完全な一覧については、「[SpeechConfig クラス](https://docs.microsoft.com/dotnet/api/)」を参照してください [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=51-53)]

## <a name="initialize-a-speechrecognizer"></a>SpeechRecognizer を初期化する

次に、`SpeechRecognizer` を作成してみましょう。 アンマネージド リソースが適切に解放されるよう、このオブジェクトは using ステートメント内で作成します。 このコードを `RecognizeSpeechAsync()` メソッドの Speech 構成のすぐ下に挿入してください。
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=58,59,93)]

## <a name="recognize-a-phrase"></a>語句を認識する

`SpeechRecognizer` オブジェクトから、`RecognizeOnceAsync()` メソッドの呼び出しを行います。 認識の対象として 1 つの語句を送信しようとしていること、また、その語句が識別された後で、音声認識を停止しようとしていることが、このメソッドを通じて Speech サービスに伝えられます。

using ステートメント内に、このコードを追加します。[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=66)]

## <a name="display-the-recognition-results-or-errors"></a>認識結果 (またはエラー) を表示する

Speech サービスによって認識結果が返されたら、それを使用して何らかの操作を行います。 シンプルに保ち、結果を状態パネルに出力します。

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=68-93)]

## <a name="build-and-run-the-application"></a>アプリケーションの構築と実行

アプリケーションをビルドしてテストする準備が整いました。

1. メニュー バーから **[ビルド]**  >  **[ソリューションのビルド]** を選択してアプリケーションをビルドします。 これで、コードは、エラーなしでコンパイルされます。

1. **[デバッグ]**  >  **[デバッグの開始]** の順に選択するか、**F5** キーを押して、アプリケーションを起動します。 **[helloworld]** ウィンドウが表示されます。

   ![サンプル UWP 音声認識アプリケーション (C#) - クイックスタート](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-helloworld-window.png)

1. **[Enable Microphone]** を選択し、アクセス許可要求がポップアップ表示されたら、 **[はい]** を選択します。

   ![マイクへのアクセス許可要求](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. **[Speech recognition with microphone input]** を選択し、デバイスのマイクに向かって英語のフレーズを話します。 音声が Speech サービスに送信されてテキストに変換され、ウィンドウに表示されます。

   ![音声認識ユーザー インターフェイス](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>次の手順

[!INCLUDE [footer](./footer.md)]
