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
ms.openlocfilehash: 38dac73d253245fce86ac419c7a4eb3a106df3f0
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818326"
---
## <a name="prerequisites"></a>前提条件

開始する前に、必ず次のことを行ってください。

> [!div class="checklist"]
> * [Azure Speech リソースを作成する](../../../../get-started.md)
> * [使用する開発環境を設定する](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [空のサンプル プロジェクトを作成する](../../../../quickstarts/create-project.md?tabs=dotnet)

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

## <a name="next-steps"></a>次の手順

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>関連項目

- [カスタム音声を作成する](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [カスタム音声サンプルを録音する](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
