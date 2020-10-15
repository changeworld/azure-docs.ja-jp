---
title: インクルード ファイル
description: インクルード ファイル
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 60a62733a17d1a3dcc4ba80ed7ceb1c37c8ac5d6
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876666"
---
## <a name="create-the-azure-iot-edge-gateway-device"></a>Azure IoT Edge ゲートウェイ デバイスを作成する

ビデオ分析 (物体とモーションの検出) アプリケーションには、**LVA Edge Object Detector** デバイス テンプレートと **LVA Edge Motion Detection** デバイス テンプレートが含まれています。 このセクションでは、配置マニフェストを使用してゲートウェイ デバイス テンプレートを作成し、そのゲートウェイ デバイスを IoT Central アプリケーションに追加します。

### <a name="create-a-device-template-for-the-lva-edge-gateway"></a>LVA Edge Gateway のデバイス テンプレートを作成する

配置マニフェストをインポートして **LVA Edge Gateway** デバイス テンプレートを作成するには:

1. IoT Central アプリケーションで、 **[デバイス テンプレート]** に移動し、 **[+ 新規]** を選択します。

1. **[Select template type]\(テンプレートの種類の選択\)** ページで **[Azure IoT Edge]** タイルを選択します。 次に、 **[次のステップ: カスタマイズ]** を選択します。

1. **[Upload an Azure IoT Edge deployment manifest]\(Azure IoT Edge 配置マニフェストをアップロードする\)** ページで、テンプレート名として「*LVA Edge Gateway*」を入力し、 **[Gateway device with downstream devices]\(ダウンストリーム デバイスがあるゲートウェイ デバイス\)** チェック ボックスをオンにします。

    まだ配置マニフェストを参照しないでください。 そのようにした場合、デプロイ ウィザードによって各モジュールのインターフェイスが要求されますが、インターフェイスを公開する必要があるのは **LvaEdgeGatewayModule** だけです。 マニフェストは、後続の手順でアップロードします。

    :::image type="content" source="./media/iot-central-video-analytics-part3/upload-deployment-manifest.png" alt-text="配置マニフェストをアップロードしない":::

    **確認\)** をクリックします。

1. **[Review]\(レビュー\)** ページで、 **[Create]\(作成\)** を選択します。

### <a name="import-the-device-capability-model"></a>デバイス機能モデルをインポートする

デバイス テンプレートには、デバイス機能モデルが含まれている必要があります。 **[LVA Edge Gateway]** ページの **[機能モデルのインポート]** タイルを選択します。 先ほど作成した *lva-configuration* フォルダーに移動し、*LvaEdgeGatewayDcm.json* ファイルを選択します。

**LVA Edge Gateway** デバイス テンプレートに、 **[LVA Edge Gateway Module]\(LVA Edge Gateway モジュール\)** が、 **[Device information]\(デバイス情報\)** 、 **[LVA Edge Gateway Settings]\(LVA Edge Gateway 設定\)** 、 **[LVA Edge Gateway Interface]\(LVA Edge Gateway インターフェイス\)** の 3 つのインターフェイスと共に含まれています。
