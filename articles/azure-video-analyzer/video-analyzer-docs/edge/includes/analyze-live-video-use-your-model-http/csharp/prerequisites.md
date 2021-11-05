---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 11/04/2021
ms.author: juliako
ms.custom: ignite-fall-2021
ms.openlocfilehash: b24d17fe43b260f10270c655449cbed4a4f26bbb
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131861311"
---
* アクティブなサブスクリプションが含まれる Azure アカウント。 まだお持ちでない場合は、[無料のアカウントを作成してください](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

    > [!NOTE]
    > 少なくとも共同作成者ロールがある Azure サブスクリプションが必要です。 適切なアクセス許可がない場合は、適切なアクセス許可をアカウント管理者に申請してください。
* [Visual Studio Code](https://code.visualstudio.com/) と次の拡張機能。
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

    [!INCLUDE [install-docker-prompt](../../common-includes/install-docker-prompt.md)]   
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。
* 「[モーションの検出とイベントの生成](../../../detect-motion-emit-events-quickstart.md)」クイックスタートを確認する

## <a name="set-up-azure-resources"></a>Azure リソースの設定

[![Azure へのデプロイ](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)  
[!INCLUDE [resources](../../../includes/common-includes/azure-resources.md)]

## <a name="overview"></a>概要
このクイックスタートでは、Video Analyzer を使用して、車両や人物などのオブジェクトを検出します。 関連付けられている推論イベントを IoT Edge Hub に発行します。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./../../../media/analyze-live-video-use-your-model-http/overview.png" alt-text="関連付けられている推論イベントを IoT Edge Hub に発行する":::

この図は、このクイックスタートでのシグナルの流れを示しています。 [エッジ モジュール](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)は、リアルタイム ストリーミング プロトコル (RTSP) サーバーをホストする IP カメラをシミュレートします。 [RTSP ソース](./../../../../pipeline.md#rtsp-source) ノードは、このサーバーからビデオ フィードをプルし、[HTTP 拡張プロセッサ](./../../../../pipeline.md#http-extension-processor)ノードにビデオ フレームを送信します。

HTTP 拡張ノードは、プロキシの役割を果たします。 これは、samplingOptions フィールドによって設定された受信ビデオフ レームをサンプリングし、ビデオ フレームを指定された画像の種類に変換します。 次に、その画像を、HTTP エンドポイントの背後で AI モデルを実行する別のエッジ モジュールに、REST 経由で転送します。 この例では、そのエッジ モジュールは、さまざまな種類のオブジェクトを検出できる YOLOv3 モデルを使用して構築されています。 HTTP 拡張プロセッサ ノードは、検出結果を収集し、イベントを [IoT Hub メッセージ シンク ノード](./../../../../pipeline.md#iot-hub-message-sink)に発行します。 次に、このノードはこれらのイベントを [IoT Edge Hub](../../../../../../iot-fundamentals/iot-glossary.md?view=iotedge-2018-06&preserve-view=true#iot-edge-hub) に送信します。

このクイックスタートでは次の作業を行います。

* livePipeline を作成してデプロイする。
* 結果を解釈する。
* リソースをクリーンアップする。

## <a name="set-up-your-development-environment"></a>開発環境を設定する
[!INCLUDE [setup development environment](./../../../includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]
