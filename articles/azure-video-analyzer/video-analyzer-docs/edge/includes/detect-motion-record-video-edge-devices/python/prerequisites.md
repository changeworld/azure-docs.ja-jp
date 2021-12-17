---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 11/04/2021
ms.author: juliako
ms.custom: ignite-fall-2021
ms.openlocfilehash: 57d367535226203a67a2ffbf3abe4e6246f09a88
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131860466"
---
* アクティブなサブスクリプションが含まれる Azure アカウント。 まだお持ちでない場合は、[無料のアカウントを作成してください](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

    [!INCLUDE [azure-subscription-permissions](../../common-includes/azure-subscription-permissions.md)]
    * [Visual Studio Code](https://code.visualstudio.com/) と次の拡張機能。
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

        [!INCLUDE [install-docker-prompt](../../common-includes/install-docker-prompt.md)]
    * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
* [Python 3](https://www.python.org/downloads/) (3.6.9 以降)、[Pip 3](https://pip.pypa.io/en/stable/installing/)、必要に応じて [venv](https://docs.python.org/3/library/venv.html)。        
   
## <a name="set-up-azure-resources"></a>Azure リソースの設定

[![Azure へのデプロイ](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)  
[!INCLUDE [resources](../../../includes/common-includes/azure-resources.md)]

## <a name="overview"></a>概要

> [!div class="mx-imgBorder"]
> :::image type="content" source="./../../../media/detect-motion-record-video-edge-devices/overview.png" alt-text="関連付けられている推論イベントを IoT Edge ハブに発行する":::

上の図は、このクイックスタートでのシグナルの流れを示しています。 [エッジ モジュール](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)は、リアルタイム ストリーミング プロトコル (RTSP) サーバーをホストする IP カメラをシミュレートします。 [RTSP ソース](./../../../../pipeline.md#rtsp-source) ノードは、このサーバーからビデオ フィードをプルし、[モーション検出プロセッサ](./../../../../pipeline.md#motion-detection-processor) ノードにビデオ フレームを送信します。 RTSP ソースは、同じビデオ フレームを[シグナル ゲート プロセッサ](./../../../../pipeline.md#signal-gate-processor) ノードに送信します。このノードは、イベントによってトリガーされるまで閉じたままになっています。

モーション検出プロセッサは、ビデオでモーションを検出すると、イベントをシグナル ゲート プロセッサ ノードに送信してトリガーします。 ゲートは、構成されている期間開いて、[ファイル シンク](./../../../../pipeline.md#file-sink) ノードにビデオ フレームを送信します。 このシンク ノードは、エッジ デバイスのローカル ファイル システムに、ビデオを MP4 ファイルとして記録します。 ファイルは、構成した場所に保存されます。

このクイックスタートでは次の作業を行います。

1. パイプラインを作成してデプロイする。
1. 結果を解釈する。
1. リソースをクリーンアップする。
## <a name="set-up-your-development-environment"></a>開発環境を設定する
[!INCLUDE [setup development environment](./../../../includes/set-up-dev-environment/python/python-set-up-dev-env.md)]
