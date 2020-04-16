---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: c2b844b3b1aa7a848e2668b3dca5a1cac5b5baaf
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400697"
---
## <a name="prerequisites"></a>前提条件

開始する前に、以下の操作を行います。

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Azure Speech リソースを作成する <span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [開発環境を設定し、空のプロジェクトを作成する](../../../../quickstarts/setup-platform.md?tabs=jre&pivots=programming-language-java)
> * オーディオ キャプチャ用のマイクにアクセスできることを確認する

## <a name="source-code"></a>ソース コード

新しい空のクラスを Java プロジェクトに追加するために、 **[File]**  >  **[New]**  >  **[Class]** の順に選択します。 **[New Java Class]** ウィンドウで、 **[Package]** フィールドに **speechsdk.quickstart** と入力し、 **[Name]** フィールドに **Main** と入力します。

![[New Java Class] ウィンドウのスクリーンショット](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

*Main.java* ファイルの内容を次のスニペットに置き換えます。

[!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/src/speechsdk/quickstart/Main.java#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>コードの説明

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>アプリをビルドして実行する

<kbd>F11</kbd> キーを押すか、 **[Run]**  >  **[Debug]** の順に選択します。
その後 15 秒間、マイクからの音声入力が認識され、コンソール ウィンドウにログが記録されます。

![認識が成功した後のコンソール出力のスクリーンショット](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>次のステップ

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

