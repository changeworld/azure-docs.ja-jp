---
title: クイック スタート:音声を合成する、C# (Unity) - Speech サービス
titleSuffix: Azure Cognitive Services
description: このガイドでは、Unity と Unity 用 Speech SDK を使用してテキスト読み上げアプリケーションを作成します。 完了すると、テキストから音声を合成し、デバイスのスピーカーにリアルタイムで出力できます。
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 9/19/2019
ms.author: yinhew
ms.openlocfilehash: 73a4477547c562a9960fe72b352e906676eadae5
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "78926009"
---
> [!NOTE]
> Unity では、Windows デスクトップ (x86 および x64) またはユニバーサル Windows プラットフォーム (x86、x64、ARM、ARM64)、Android (x86、ARM32、ARM64)、および iOS (x64 シミュレーター、ARM32 および ARM64) がサポートされています。

## <a name="prerequisites"></a>前提条件

開始する前に、必ず次のことを行ってください。

> [!div class="checklist"]
> * [Azure Speech リソースを作成する](../../../../get-started.md)
> * [開発環境を設定し、空のプロジェクトを作成する](../../../../quickstarts/setup-platform.md?tabs=unity)

## <a name="add-a-ui"></a>UI を追加する

合成するテキストを入力するための入力フィールド、音声合成をトリガーするボタン、および結果を表示するテキスト フィールドから成る最小限の UI をシーンに追加します。

* [[Hierarchy]\(ヒエラルキー\) ウィンドウ](https://docs.unity3d.com/Manual/Hierarchy.html) (既定では左側) に、Unity の新しいプロジェクトで作成されたサンプルのシーンが表示されます。
* **[Hierarchy]\(ヒエラルキー\)** ウィンドウの上部にある **[Create]\(作成\)** をクリックし、 **[UI]**  >  **[Input Field]\(入力フィールド\)** の順に選択します。
* このオプションにより、3 つのゲーム オブジェクトが作成され、 **[Hierarchy]\(ヒエラルキー\)** ウィンドウに表示されます。 **[Canvas]\(キャンバス\)** オブジェクト、その中で入れ子になった **[Input Field]\(入力フィールド\)** オブジェクト、および **[EventSystem]** オブジェクトです。
* [[Scene]\(シーン\) ビューに移動](https://docs.unity3d.com/Manual/SceneViewNavigation.html)して、[[Scene]\(シーン\) ビュー](https://docs.unity3d.com/Manual/UsingTheSceneView.html)内でキャンバスと入力フィールドが適切に表示されていることを確認します。
* **[Hierarchy]\(ヒエラルキー\)** ウィンドウ内で **[Input Field]\(入力フィールド\)** オブジェクトを選択して、その設定を [[Inspector]\(インスペクター\)](https://docs.unity3d.com/Manual/UsingTheInspector.html) ウィンドウに表示します (既定では右側)。
* **[Pos X]\(座標 X\)** プロパティと **[Pos Y]\(座標 Y\)** プロパティを **0** に設定して、入力フィールドがキャンバスの中央に配置されるようにします。
* もう一度 **[Hierarchy]\(ヒエラルキー\)** ウィンドウの上部にある **[Create]\(作成\)** を選択します。 **[UI]**  >  **[Button]\(ボタン\)** の順に選択してボタンを作成します。
* **[Hierarchy]\(ヒエラルキー\)** ウィンドウ内で **[Button]\(ボタン\)** オブジェクトを選択して、その設定を [[Inspector]\(インスペクター\)](https://docs.unity3d.com/Manual/UsingTheInspector.html) ウィンドウに表示します (既定では右側)。
* **[Pos X]\(座標 X\)** プロパティと **[Pos Y]\(座標 Y\)** プロパティを **0** と **-48** に設定します。 **[Width]\(幅\)** プロパティと **[Height]\(高さ\)** プロパティを **160** と **30** に設定して、ボタンと入力フィールドが重ならないようにします。
* もう一度 **[Hierarchy]\(ヒエラルキー\)** ウィンドウの上部にある **[Create]\(作成\)** を選択します。 **[UI]**  >  **[Text]\(テキスト\)** の順に選択して、テキスト フィールドを作成します。
* **[Hierarchy]\(ヒエラルキー\)** ウィンドウ内で **[Text]\(テキスト\)** オブジェクトを選択して、その設定を [[Inspector]\(インスペクター\) ウィンドウ](https://docs.unity3d.com/Manual/UsingTheInspector.html)に表示します (既定では右側)。
* **[Pos X]\(座標 X\)** プロパティと **[Pos Y]\(座標 Y\)** プロパティを **0** と **80** に設定します。 **[Width]\(幅\)** プロパティと **[Height]\(高さ\)** プロパティを **320** と **80** に設定して、テキスト フィールドと入力フィールドが重ならないようにします。
* もう一度 **[Hierarchy]\(ヒエラルキー\)** ウィンドウの上部にある **[Create]\(作成\)** を選択します。 **[Audio]\(オーディオ\)**  >  **[Audio Source]\(オーディオ ソース\)** の順に選択して、オーディオ ソースを作成します。

完了したら、次のスクリーンショットのような UI になります。

[![Unity エディターにおけるクイック スタートのユーザー インターフェイスを示すスクリーンショット](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>サンプル コードを追加する

1. [[Project]\(プロジェクト\) ウィンドウ](https://docs.unity3d.com/Manual/ProjectView.html) (既定では左下) で **[Create]\(作成\)** を選択し、 **[C# script]\(C# スクリプト\)** を選択します。 スクリプトに `HelloWorld` という名前を付けます。

1. ダブルクリックしてスクリプトを編集します。

   > [!NOTE]
   > **[Edit]\(編集\)**  >  **[Preferences]\(設定\)** の順に選択して、起動するコード エディターを構成できます。 詳細については、[Unity のユーザー マニュアル](https://docs.unity3d.com/Manual/Preferences.html)を参照してください。

1. すべてのコードを次のものに置き換えます。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/text-to-speech/Assets/Scripts/HelloWorld.cs#code)]

1. `YourSubscriptionKey` という文字列を探し、実際の Speech サービスのサブスクリプション キーに置き換えます。

1. `YourServiceRegion` という文字列を探し、実際のサブスクリプションに関連付けられている[リージョン](~/articles/cognitive-services/Speech-Service/regions.md)に置き換えます。 たとえば、無料試用版を使用している場合、リージョンは `westus` になります。

1. スクリプトへの変更を保存します。

1. Unity エディターに戻り、ゲーム オブジェクトの 1 つにコンポーネントとしてスクリプトを追加します。

   * **[Hierarchy]\(ヒエラルキー\)** ウィンドウ内で **[Canvas]\(キャンバス\)** オブジェクトを選択して、その設定を [[Inspector]\(インスペクター\) ウィンドウ](https://docs.unity3d.com/Manual/UsingTheInspector.html)に表示します (既定では右側)。
   * **[Inspector]\(インスペクター\)** ウィンドウで、 **[Add Component]\(コンポーネントの追加\)** を選択します。 次に、先ほど作成した `HelloWorld` スクリプトを検索して、追加します。
   * HelloWorld コンポーネントには、初期化されていない 4 つのプロパティ、**Output Text**、**Input Field**、**Speak Button**、および **Audio Source** が含まれています。これらは `HelloWorld` クラスのパブリック プロパティに一致します。
     これらを接続するには、オブジェクト ピッカー (プロパティの右側にある小さな円アイコン) を選択します。 先ほど作成したテキスト オブジェクトとボタン オブジェクトを選択します。

     > [!NOTE]
     > 入力フィールドとボタンには、入れ子になったテキスト オブジェクトも含まれています。 テキスト出力用として、誤ってこれを選択しないようにしてください。 または、このような混乱を避けるために、 **[Inspector]\(インスペクター\)** ウィンドウ内で **[Name]\(名前\)** フィールドを使用するテキスト オブジェクトの名前を変更してもかまいません。

## <a name="run-the-application-in-the-unity-editor"></a>Unity エディターでアプリケーションを実行する

* Unity エディターのメニュー バーの下のツール バーの **[Play]\(再生\)** を選択します。
* アプリの起動後、入力フィールドに何かテキストを入力して、ボタンを選択します。 テキストが Speech サービスに転送されて音声に合成され、スピーカーで再生されます。

  [![Unity の [Game]\(ゲーム\) ウィンドウで実行中のクイックスタートのスクリーンショット](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-inline.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* [[Console]\(コンソール\) ウィンドウ](https://docs.unity3d.com/Manual/Console.html)でデバッグ メッセージを確認します。

## <a name="additional-options-to-run-this-application"></a>このアプリケーションを実行するための追加のオプション

このアプリケーションは、Windows のスタンドアロン アプリまたは UWP アプリケーションとして Android にもデプロイできます。
これらの追加のターゲットの構成について説明している、quickstart/csharp-unity フォルダー内の[サンプル リポジトリ](https://aka.ms/csspeech/samples)を参照してください。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>関連項目

- [Custom Voice を作成する](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [カスタム音声サンプルを録音する](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
