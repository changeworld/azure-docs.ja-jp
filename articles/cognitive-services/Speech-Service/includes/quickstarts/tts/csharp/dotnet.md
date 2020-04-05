---
title: クイック スタート:音声を合成する、C# (Windows) - Speech サービス
titleSuffix: Azure Cognitive Services
description: このガイドでは、.NET framework for Windows と Speech SDK を使用してテキスト読み上げのコンソール アプリケーションを作成します。 完了すると、テキストから音声を合成し、その音声をスピーカーからリアルタイムで聞くことができます。
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/27/2019
ms.author: yinhew
ms.openlocfilehash: b615ba8085650e9aa686fb4a229d9752c4f6e2ce
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "78924849"
---
## <a name="prerequisites"></a>前提条件

開始する前に、必ず次のことを行ってください。

> [!div class="checklist"]
> * [Azure Speech リソースを作成する](../../../../get-started.md)
> * [開発環境を設定し、空のプロジェクトを作成する](../../../../quickstarts/setup-platform.md?tabs=dotnet)

## <a name="add-sample-code"></a>サンプル コードを追加する

1. **Program.cs** を開いて、自動的に生成されたコードを次のサンプルに置き換えます。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/text-to-speech/helloworld/Program.cs#code)]

1. `YourSubscriptionKey` という文字列を探し、実際の Speech サービスのサブスクリプション キーに置き換えます。

1. `YourServiceRegion` という文字列を探し、ご利用のサブスクリプションに関連付けられている[リージョン](~/articles/cognitive-services/Speech-Service/regions.md)に置き換えます。 たとえば、無料試用版サブスクリプションを使用している場合、リージョンは `westus` になります。

1. メニュー バーから **[ファイル]**  >  **[すべて保存]** を選択します。

## <a name="build-and-run-the-application"></a>アプリケーションの構築と実行

1. メニュー バーから **[ビルド]**  >  **[ソリューションのビルド]** を選択してアプリケーションをビルドします。 これで、コードは、エラーなしでコンパイルされます。

1. **[デバッグ]**  >  **[デバッグの開始]** の順に選択するか、**F5** キーを押して、**helloworld** アプリケーションを起動します。

1. 英語の語句または文を入力します。 そのテキストが Speech サービスに転送され、そこで合成された音声がアプリケーションに送信されてスピーカーで再生されます。

   ![音声合成ユーザー インターフェイス](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>次のステップ

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>関連項目

- [Custom Voice を作成する](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [カスタム音声サンプルを録音する](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
