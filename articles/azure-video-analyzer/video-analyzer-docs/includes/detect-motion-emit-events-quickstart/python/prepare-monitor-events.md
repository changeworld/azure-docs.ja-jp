---
author: fvneerden
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 05/03/2021
ms.author: faneerde
ms.openlocfilehash: e98039dfe4c8c44be2fc375956339956a495da4f
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2021
ms.locfileid: "113559441"
---
Azure Video Analyzer モジュールを使用して、受信したライブ ビデオ ストリーム内で動きを検出し、イベントを IoT Hub に送信します。 それらのイベントを確認するには、次の手順に従います。

1. Visual Studio Code の [エクスプローラー] ペインを開き、左下隅にある [Azure IoT Hub] を探します。
1. **[デバイス]** ノードを展開します。
1. **avasample-iot-edge-device** を右クリックし、 **[組み込みイベント エンドポイントの監視を開始します]** を選択します。

![組み込みイベント エンドポイントの監視を開始する](../../../media/vscode-common-screenshots/start-monitoring.png)

[!INCLUDE [provide-builtin-endpoint](../../common-includes/provide-builtin-endpoint.md)]
