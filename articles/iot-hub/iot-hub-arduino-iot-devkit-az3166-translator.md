---
title: Azure Functions と Cognitive Services を使用した IoT DevKit 翻訳ツールの作成 | Microsoft Docs
description: IoT DevKit のマイクを使用して音声メッセージを受信し、そのメッセージを英語のテキストに翻訳するために Azure Cognitive Services を使用します
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/28/2018
ms.author: liydu
ms.openlocfilehash: cd67e612dd020ba600e33ac8baf77bc094d8afd3
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2018
ms.locfileid: "42142671"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>IoT DevKit AZ3166 と Azure Functions および Cognitive Services を使用して言語翻訳ツールを作成する

この記事では、[Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) を使用して、IoT DevKit をトランスレーターにする方法について説明します。 このトランスレーターでは、声を記録し、それを英語に翻訳して、DevKit の画面に表示します。

[MXChip IoT DevKit](https://aka.ms/iot-devkit) は、豊富な周辺機器とセンサーを備えたオールインワンの Arduino 互換ボードです。 [Arduino 向け Visual Studio Code 拡張機能](https://aka.ms/arduino)を使用して開発できます。 また、Microsoft Azure サービスを活用したモノのインターネット (IoT) ソリューションのプロトタイプを作成する手順を示す、増加している[プロジェクト カタログ](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)が付属しています。

## <a name="what-you-need"></a>必要なもの

[ファースト ステップ ガイド](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)に従って以下のことを行います。

* DevKit をWi-Fi に接続
* 開発環境の準備

有効な Azure サブスクリプション 持っていない場合は、次の 2 つの方法のいずれかを使用して登録できます。

* [30 日間の無料試用版 Microsoft Azure アカウント](https://azure.microsoft.com/free/)をアクティブにする
* MSDN または Visual Studio サブスクライバーの場合、[Azure クレジット](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を要求する

## <a name="open-the-project-folder"></a>プロジェクト フォルダーを開く

まず、プロジェクト フォルダーを開きます。 

### <a name="start-vs-code"></a>VS Code の起動

- DevKit がお使いの PC に接続されていることを確認します。

- VS Code を起動します。

- DevKit をコンピューターに接続します。

### <a name="open-the-arduino-examples-folder"></a>[Arduino Examples]\(Arduino の例\) フォルダーを開く

左側の **[Arduino Examples]\(Arduino の例\) > [Examples for MXCHIP AZ3166] > [AzureIoT]** を展開し、**[DevKitTranslator]** を選択します。 プロジェクト フォルダーを表示する、新しい VS Code ウィンドウが開きます。 MXCHIP AZ3166 セクションの例を表示できない場合は、ご利用のデバイスが正しく接続されていることを確認し、VS Code を再起動します。  

![IoT DevKit のサンプル](media/iot-hub-arduino-iot-devkit-az3166-translator/vscode_examples.png)

コマンド パレットから例を開くこともできます。 `Ctrl+Shift+P` キー (macOS: `Cmd+Shift+P` キー) を使用してコマンド パレットを開き、「**Arduino**」と入力します。次に、**[Arduino: Examples]\(Arduino: 例\)** を見つけて選択します。

## <a name="provision-azure-services"></a>Azure サービスのプロビジョニング

ソリューション ウィンドウで、`Ctrl+P` (macOS: `Cmd+P`) と入力し、`task cloud-provision` と入力します。

VS Code ターミナルでは、対話型コマンド ラインを使用して、必要なすべての Azure サービスをプロビジョニングできます。

![クラウド プロビジョニング タスク](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-provision.png)

## <a name="deploy-the-azure-function"></a>Azure 関数のデプロイ

`Ctrl+P` (macOS: `Cmd+P`) を使用して `task cloud-deploy` を実行し、Azure Functions コードをデプロイします。 通常、この処理は完了するまでに 2 から 5 分かかります。

![クラウド デプロイ タスク](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-deploy.png)

Azure Function が正常にデプロイされたら、azure_config.h ファイルに関数アプリ名を入力します。 これは [Azure Portal ](https://portal.azure.com/)で確認できます。

![Azure Function App 名の確認](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-function.png)

> [!NOTE]
> Azure Function が適切に動作しない場合は、[IoT DevKit の FAQ の「Compilation error for Azure Function」 (Azure Function のコンパイル エラー) ページ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function)を確認して、エラーを解決します。

## <a name="build-and-upload-the-device-code"></a>デバイス コードのビルドとアップロード

1. `Ctrl+P` (macOS: `Cmd+P`) を使用して `task config-device-connection` を実行します。

2. ターミナルにより、`task cloud-provision` の手順から取得される接続文字列を使用するかどうか確認するメッセージが表示されます。 **[新規作成...]** を選択して、独自のデバイスの接続文字列を入力することもできます。

3. ターミナルによって、構成モードを開始するよう求められます。 これを行うには、A ボタンを押しながら、リセット ボタンを押して離します。 画面に、DevKit の ID と "構成" が表示されます。

   ![Arduino スケッチの確認とアップロード](media/iot-hub-arduino-iot-devkit-az3166-translator/config-device-connection.png)

4. `task config-device-connection` が完了したら、`F1` をクリックして VS Code コマンドをロードして `Arduino: Upload` を選択すると、VS Code によって Arduino スケッチの確認とアップロードが開始されます。

   ![Arduino スケッチの確認とアップロード](media/iot-hub-arduino-iot-devkit-az3166-translator/arduino-upload.png)

DevKit が再起動され、コードの実行が開始されます。

## <a name="test-the-project"></a>プロジェクトのテスト

アプリの初期化が終わったら、DevKit の画面の指示に従います。 既定のソース言語は中国語です。

別の言語を選択するには、次の手順に従います。

1. A ボタン を押してセットアップ モードにします。

2. B ボタンを押すと、サポートされているすべてのソース言語をスクロールできます。

3. A ボタンを押して、ソース言語の選択を確定します。

4. B ボタンを押しながら話した後、B ボタンを放すと、翻訳が開始されます。

5. 翻訳されたテキスト (英語) が画面に表示されます。

![スクロールして言語を選択します](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![翻訳結果](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

翻訳結果画面では、次の操作を実行できます。

- A ボタンを押し、B ボタンを押してスクロールし、ソース言語を選択します。

- B ボタンを押して話します。 音声を送信し、翻訳テキストを表示するには、B ボタンを放します。

## <a name="how-it-works"></a>動作のしくみ

![mini-solution-voice-to-tweet-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

Arduino スケッチは音声を記録し、HTTP 要求をポストして Azure Functions をトリガーします。 Azure Functions では、コグニティブ サービスの音声翻訳 API を呼び出して翻訳を行います。 Azure Functions は翻訳テキストを取得すると、デバイスに C2D (cloud-to-device) メッセージを送信します。 その後、画面に翻訳テキストが表示されます。

## <a name="problems-and-feedback"></a>問題とフィードバック

問題が発生した場合は、[IoT DevKit の FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) に関するページを参照するか、以下のチャネルを使用して Microsoft までお問い合わせください。

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [StackOverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>次の手順

Azure Functions と Cognitive Services を使用して IoT DevKit を翻訳ツールとして使用する方法について学習しました。 この使い方で学習した内容は次のとおりです。

> [!div class="checklist"]
> * Visual Studio Code タスクを使用してクラウド プロビジョニングを自動化する
> * Azure IoT デバイス接続文字列を構成する
> * Azure 関数のデプロイ
> * 音声メッセージ翻訳をテストする

他のチュートリアルに進んで、次の方法を学習してください。

> [!div class="nextstepaction"]
> [IoT DevKit AZ3166 を Azure IoT リモート監視ソリューション アクセラレータに接続する](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)
