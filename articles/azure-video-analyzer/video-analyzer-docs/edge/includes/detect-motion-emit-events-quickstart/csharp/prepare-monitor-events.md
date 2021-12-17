---
author: fvneerden
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 11/04/2021
ms.author: faneerde
ms.openlocfilehash: 2bb305673897daa264dafc9b766061a01717099a
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131860489"
---
Video Analyzer エッジ モジュールを使用して、受信したライブ ビデオ ストリーム内で動きを検出し、イベントを IoT Hub に送信します。 それらのイベントを確認するには、次の手順に従います。

1. Visual Studio Code の [エクスプローラー] ペインを開き、左下隅にある [Azure IoT Hub] を探します。
1. **[デバイス]** ノードを展開します。
1. **avasample-iot-edge-device** を右クリックし、 **[組み込みイベント エンドポイントの監視を開始します]** を選択します。

   ![組み込みイベント エンドポイントの監視を開始する](../../../media/vscode-common-screenshots/start-monitoring.png)

    [!INCLUDE [provide-builtin-endpoint](../../common-includes/provide-builtin-endpoint.md)]
