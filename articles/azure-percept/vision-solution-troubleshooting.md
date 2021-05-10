---
title: Azure Percept Vision とビジョン モジュールに関する問題をトラブルシューティングする
description: ビジョン AI のプロトタイプ作成エクスペリエンスで多く見られるいくつかの問題をトラブルシューティングするためのヒントを紹介します
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/29/2021
ms.custom: template-how-to
ms.openlocfilehash: 78de5ef0ef77a181d4a2da91e4b468db1b47f208
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106074694"
---
# <a name="vision-solution-troubleshooting"></a>ビジョン ソリューションのトラブルシューティング

Azure Percept Studio でコーディングなしのビジョン ソリューションをトラブルシューティングする方法については、次のガイダンスを参照してください。

## <a name="delete-a-vision-project"></a>ビジョン プロジェクトを削除する

1. https://www.customvision.ai/projects にアクセスします。

1. 削除したいプロジェクトにマウス ポインターを合わせ、ごみ箱アイコンをクリックすると、プロジェクトが削除されます。

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-project.png" alt-text="削除アイコンが強調表示されている Custom Vision の [プロジェクト] ページ。":::

## <a name="check-which-modules-are-on-a-device"></a>デバイスに存在するモジュールを確認する

1. [Azure ポータル](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home)にアクセスします。

1. **[IoT Hub]** アイコンをクリックします。

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub-2-inline.png" alt-text="[IoT Hub] アイコンが強調表示されている Azure portal ホームページ。" lightbox= "./media/vision-solution-troubleshooting/vision-iot-hub-2.png":::

1. ターゲット デバイスの接続先の IoT ハブを選択します。

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub.png" alt-text="IoT ハブの一覧。":::

1. **[IoT Edge]** を選択し、 **[デバイス ID]** タブで目的のデバイスをクリックします。

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-edge.png" alt-text="IoT Edge ホームページ。":::

1. **[モジュール]** タブにデバイス モジュールが一覧表示されます。

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-device-modules-inline.png" alt-text="選択したデバイスの [IoT Edge] ページに表示される [モジュール] タブの内容。" lightbox= "./media/vision-solution-troubleshooting/vision-device-modules.png":::

## <a name="delete-a-device"></a>デバイスの削除

1. [Azure ポータル](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home)にアクセスします。

1. **[IoT Hub]** アイコンをクリックします。

1. ターゲット デバイスの接続先の IoT ハブを選択します。

1. **[IoT Edge]** を選択し、ターゲット デバイス ID の横にあるチェック ボックスをオンにします。 ごみ箱アイコンをクリックすると、デバイスが削除されます。

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-device.png" alt-text="IoT Edge ホームページで強調表示された削除アイコン。":::

## <a name="eye-module-troubleshooting-tips"></a>Eye モジュールのトラブルシューティングのヒント

### <a name="check-the-runtime-status-of-azureeyemodule"></a>azureeyemodule のランタイムの状態を確認する

**WebStreamModule** に問題がある場合は、ビジョン モデル推論を処理する **azureeyemodule** が実行されていることを確認してください。 ランタイムの状態をチェックするには、[Azure portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) に移動し、 **[すべてのリソース]**  ->  **\<your IoT hub>**  ->  **[IoT Edge]**  ->  **\<your device ID>** に移動します。 **[モジュール]** タブをクリックすると、インストールされているすべてのモジュールのランタイムの状態が表示されます。

:::image type="content" source="./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page-inline.png" alt-text="デバイス モジュールのランタイム状態画面。" lightbox= "./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page.png":::

**azureeyemodule** のランタイムの状態が **[running]\(実行中\)** になっていない場合は、 **[モジュールの設定]**  ->  **[azureeyemodule]** をクリックします。 **[モジュールの設定]** ページの **[必要な状態]** を **[running]\(実行中\)** に設定し、 **[更新]** をクリックします。

 :::image type="content" source="./media/vision-solution-troubleshooting/firmware-desired-status-stopped.png" alt-text="モジュール設定の構成画面。":::

### <a name="update-telemetryintervalneuralnetworkms"></a>TelemetryIntervalNeuralNetworkMs を更新する

次のカウント制限エラーが発生した場合は、azureeyemodule モジュール ツイン設定の TelemetryIntervalNeuralNetworkMs 値を更新する必要があります。

|エラー メッセージ|
|------|
|Total number of messages on IotHub 'xxxxxxxxx' exceeded the allocated quota. (IotHub 'xxxxxxxxx' 上のメッセージの合計数が、割り当てられたクォータを超えました。) Max allowed message count: '8000', current message count: 'xxxx'. (許容される最大メッセージ数: '8000'、現在のメッセージ数: 'xxxx'。) Send and Receive operations are blocked for this hub until the next UTC day. (このハブの送信および受信操作は、次の UTC 日までブロックされます。) Consider increasing the units for this hub to increase the quota. (クォータを増やすには、このハブのユニット数を増やすことを検討してください。)|

TelemetryIntervalNeuralNetworkMs ではニューラル ネットワークからメッセージを送信する頻度をミリ秒単位で決定します。 Azure サブスクリプションでは、サブスクリプション レベルに応じて 1 日あたりのメッセージ数が制限されています。 送信されたメッセージが多すぎることが原因でロックアウトされた場合は、この値を大きくします。 12000 (つまり 12 秒ごとに 1 回) を指定すると、メッセージ数は 1 日あたり約 7200 という最適な数になります。これは、無料サブスクリプションの上限である 8000 メッセージを下回ります。

TelemetryIntervalNeuralNetworkMs 値を更新するには、こちらの手順を実行します。

1. [Azure portal](https://ms.portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod#home) にログインし、 **[すべてのリソース]** を開きます。

1. **[すべてのリソース]** ページで、セットアップ エクスペリエンス中に開発キットにプロビジョニングされた IoT Hub の名前をクリックします。

1. [IoT Hub] ページの左側で、 **[デバイスの自動管理]** の **[IoT Edge]** をクリックします。 [IoT エッジ デバイス] ページで、開発キットのデバイス ID を探します。 開発キットのデバイス ID をクリックして、その [IoT Edge デバイス] ページを開きます。

1. **[モジュール]** タブで **[azureeyemodule]** を選択します。

1. [azureeyemodule ] ページで、 **[モジュール ID ツイン]** を開きます。

    :::image type="content" source="./media/vision-solution-troubleshooting/module-page-inline.png" alt-text="モジュール ページのスクリーンショット。" lightbox= "./media/vision-solution-troubleshooting/module-page.png":::

1. 下にスクロールして、**properties** に移動します。 現時点では、"Running" と "Logging" のプロパティはアクティブではないことに注意してください。

    :::image type="content" source="./media/vision-solution-troubleshooting/module-identity-twin-inline.png" alt-text="モジュール ツインのプロパティのスナップショット。" lightbox= "./media/vision-solution-troubleshooting/module-identity-twin.png":::

1. 必要に応じて **TelemetryIntervalNeuralNetworkMs** の値を更新し、 **[保存]** アイコンをクリックします。

## <a name="view-device-rtsp-video-stream"></a>デバイスの RTSP ビデオ ストリームを表示する

デバイスの RTSP ビデオ ストリームは、[Azure Percept Studio](./how-to-view-video-stream.md) または [VLC メディア プレーヤー](https://www.videolan.org/vlc/index.html)で表示します。

VLC メディア プレーヤーで RTSP ストリームを開くには、 **[Media]\(メディア\)**  ->  **[Open network stream]\(ネットワーク ストリームを開く\)**  ->  **[rtsp://<デバイスの IP アドレス>/result]** に移動します。

## <a name="next-steps"></a>次のステップ

Azure Percept DK のトラブルシューティングの詳細については、[一般的なトラブルシューティング ガイド](./troubleshoot-dev-kit.md)を参照してください。