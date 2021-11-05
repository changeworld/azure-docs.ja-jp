---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 11/04/2021
ms.author: juliako
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5b0f58607552d1891b116e463332f06ea8578b94
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131861235"
---
* アクティブなサブスクリプションが含まれる Azure アカウント。 まだお持ちでない場合は、[無料のアカウントを作成してください](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

    > [!NOTE]
    > 少なくとも共同作成者ロールがある Azure サブスクリプションが必要です。 適切なアクセス許可がない場合は、適切なアクセス許可をアカウント管理者に申請してください。
* [Visual Studio Code](https://code.visualstudio.com/) と次の拡張機能。
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    
    [!INCLUDE [install-docker-prompt](../../common-includes/install-docker-prompt.md)]
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。 

## <a name="set-up-azure-resources"></a>Azure リソースの設定

[![Azure へのデプロイ](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)  
[!INCLUDE [resources](../../../includes/common-includes/azure-resources.md)]

## <a name="overview"></a>概要

> [!div class="mx-imgBorder"]
> :::image type="content" source="./../../../media/analyze-live-video-use-your-model-grpc/overview.png" alt-text="gRPC の概要":::
 
この図は、このクイックスタートでのシグナルの流れを示しています。 [エッジ モジュール](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)は、リアルタイム ストリーミング プロトコル (RTSP) サーバーをホストする IP カメラをシミュレートします。 [RTSP ソース ノード](./../../../../pipeline.md#rtsp-source)は、このサーバーからビデオ フィードをプルし、[モーション検出プロセッサ](./../../../../pipeline.md#motion-detection-processor) ノードにビデオ フレームを送信します。 このプロセッサはモーションを検出し、検出時にビデオ フレームを [gRPC 拡張プロセッサ](./../../../../pipeline.md#grpc-extension-processor) ノードにプッシュします。

gRPC 拡張ノードはプロキシの役割を果たします。 ビデオ フレームを、指定した画像の種類に変換します。 次に、[共有メモリ](https://en.wikipedia.org/wiki/Shared_memory)を介して gRPC エンドポイントの背後で AI モデルを実行する別のエッジ モジュールに、画像を gRPC 経由で転送します。 この例では、そのエッジ モジュールは、さまざまな種類のオブジェクトを検出できる [YOLOv3](https://github.com/Azure/video-analyzer/tree/main/edge-modules/extensions/yolo/yolov3) モデルを使用して構築されています。 gRPC 拡張プロセッサ ノードは検出結果を収集し、イベントを [IoT Hub シンク](./../../../../pipeline.md#iot-hub-message-sink) ノードに発行します。 次に、このノードはこれらのイベントを [IoT Edge Hub](../../../../../../iot-fundamentals/iot-glossary.md?view=iotedge-2020-11&preserve-view=true#iot-edge-hub) に送信します。

このクイックスタートでは次の作業を行います。

1. パイプラインを作成してデプロイする。
1. 結果を解釈する。
1. リソースをクリーンアップする。

## <a name="set-up-your-development-environment"></a>開発環境を設定する
[!INCLUDE [setup development environment](./../../../includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]
