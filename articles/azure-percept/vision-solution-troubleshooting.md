---
title: Azure Percept Vision とビジョン モジュールに関する問題をトラブルシューティングする
description: ビジョン AI のプロトタイプ作成エクスペリエンスで多く見られるいくつかの問題をトラブルシューティングするためのヒントを紹介します。
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/29/2021
ms.custom: template-how-to
ms.openlocfilehash: 28ac7d0d7079d8ba8c9483e7da816430295941c9
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2021
ms.locfileid: "112027859"
---
# <a name="vision-solution-troubleshooting"></a>ビジョン ソリューションのトラブルシューティング

この記事では、Azure Percept Studio でコーディングなしのビジョン ソリューションのトラブルシューティングに関する情報を説明します。

## <a name="delete-a-vision-project"></a>ビジョン プロジェクトを削除する

1. [[Custom Vision プロジェクト]](https://www.customvision.ai/projects) ページにアクセスします。

1. 削除したいプロジェクトにマウス ポインターを合わせ、ごみ箱アイコンを選択すると、プロジェクトが削除されます。

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-project.png" alt-text="[削除] アイコンが強調表示されている Custom Vision の [プロジェクト] ページを示すスクリーンショット。":::

## <a name="check-which-modules-are-on-a-device"></a>デバイスに存在するモジュールを確認する

1. [Azure ポータル](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home)にアクセスします。

1. **[IoT ハブ]** アイコンを選択します。

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub-2-inline.png" alt-text="Iot ハブ アイコンが強調表示されている Azure portal ホームページを示すスクリーンショット。" lightbox= "./media/vision-solution-troubleshooting/vision-iot-hub-2.png":::

1. ターゲット デバイスの接続先の IoT ハブを選択します。

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub.png" alt-text="IoT ハブの一覧を示すスクリーンショット。":::

1. **[IoT Edge]** を選択し、 **[デバイス ID]** タブで目的のデバイスを選択します。

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-edge.png" alt-text="IoT Edge ホームページを示すスクリーンショット。":::

1. デバイス モジュールは、 **[モジュール]** タブの一覧に表示されます。

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-device-modules-inline.png" alt-text="[モジュール] タブの内容を表示する、選択したデバイスの [IoT Edge] ページを示すスクリーンショット。" lightbox= "./media/vision-solution-troubleshooting/vision-device-modules.png":::

## <a name="delete-a-device"></a>デバイスの削除

1. [Azure ポータル](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home)にアクセスします。

1. **[IoT ハブ]** アイコンを選択します。

1. ターゲット デバイスの接続先の IoT ハブを選択します。

1. **[IoT Edge]** を選択し、ターゲット デバイス ID の横にあるチェックボックスを選択します。 **[削除]** を選択してデバイスを削除します。

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-device.png" alt-text="IoT Edge ホームページで強調表示されている [削除] ボタンを示すスクリーンショット。":::

## <a name="eye-module-troubleshooting-tips"></a>Eye モジュールのトラブルシューティングのヒント

次のトラブルシューティングするためのヒントは、ビジョン AI のプロトタイプ作成エクスペリエンスで多く見られるいくつかの問題で役立ちます。

### <a name="check-the-runtime-status-of-azureeyemodule"></a>azureeyemodule のランタイムの状態を確認する

**WebStreamModule** に問題がある場合は、ビジョン モデル推論を処理する **azureeyemodule** が実行されていることを確認してください。 ランタイムの状態を確認するには、次の操作を行います。

1. [Azure portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home)にアクセスし、 **[すべてのリソース]**  >  *\<your IoT hub>*  >  **[IoT Edge]**  >  *\<your device ID>* にアクセスし ます。 
1. **[モジュール]** タブを選択すると、インストールされているすべてのモジュールのランタイムの状態が表示されます。

   :::image type="content" source="./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page-inline.png" alt-text="デバイス モジュールのランタイム状態画面を示すスクリーンショット。" lightbox= "./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page.png":::

1. **azureeyemodule** のランタイムの状態が **[実行中]** になっていない場合は、 **[モジュールの設定]**  >  **[azureeyemodule]** を選択します。 
1. **[モジュールの設定]** ページの **[必要な状態]** を **[実行中]** に設定し、 **[更新]** を選択します。

    :::image type="content" source="./media/vision-solution-troubleshooting/firmware-desired-status-stopped.png" alt-text="モジュール設定の構成画面を示すスクリーンショット。":::

### <a name="update-telemetryintervalneuralnetworkms"></a>TelemetryIntervalNeuralNetworkMs を更新する

次のカウント制限エラーが見られた場合は、azureeyemodule モジュール ツイン設定の TelemetryIntervalNeuralNetworkMs 値を更新する必要があります。

|エラー メッセージ|
|------|
|Total number of messages on IotHub 'xxxxxxxxx' exceeded the allocated quota. (IotHub 'xxxxxxxxx' 上のメッセージの合計数が、割り当てられたクォータを超えました。) Max allowed message count: '8000', current message count: 'xxxx'. (許容される最大メッセージ数: '8000'、現在のメッセージ数: 'xxxx'。) Send and Receive operations are blocked for this hub until the next UTC day. (このハブの送信および受信操作は、次の UTC 日までブロックされます。) Consider increasing the units for this hub to increase the quota. (クォータを増やすには、このハブのユニット数を増やすことを検討してください。)|

TelemetryIntervalNeuralNetworkMs ではニューラル ネットワークからメッセージを送信する頻度を決定します。 メッセージは、ミリ秒単位で送信されます。 Azure サブスクリプションでは、1 日あたりのメッセージ数が制限されています。

メッセージの量は、サブスクリプション レベルに基づいています。 送信されたメッセージが多すぎることが原因でロックアウトされた場合は、この量を大きくします。 12,000 の場合、12 秒ごとに 1 つのメッセージが表示されます。 この量では、1 日あたり 7,200 のメッセージが得られます。これは、無料サブスクリプションの上限である 8,000 メッセージを下回ります。

TelemetryIntervalNeuralNetworkMs 値を更新するには、次を実行します。

1. [Azure portal](https://ms.portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod#home) にサインインし、 **[すべてのリソース]** を開きます。

1. **[すべてのリソース]** ページで、セットアップ中に開発キットにプロビジョニングされた IoT ハブの名前を選択します。

1. **[IoT Hub]** ページの左側の **[デバイスの自動管理]** で、 **[IoT Edge]** を選択します。 [IoT Edge デバイス] ページで、開発キットのデバイス ID を探します。 開発キットのデバイス ID を選択して、その [IoT Edge デバイス] ページを開きます。

1. **[モジュール]** タブで **[azureeyemodule]** を選択します。

1. **[azureeyemodule]** ページで、 **[モジュール ID ツイン]** を開きます。

    :::image type="content" source="./media/vision-solution-troubleshooting/module-page-inline.png" alt-text="モジュール ページのスクリーンショット。" lightbox= "./media/vision-solution-troubleshooting/module-page.png":::

1. 下にスクロールして、**properties** に移動します。 この時点では、**実行中** プロパティと **ログ** プロパティはアクティブではありません。

    :::image type="content" source="./media/vision-solution-troubleshooting/module-identity-twin-inline.png" alt-text="モジュール ツインのプロパティのスクリーンショット。" lightbox= "./media/vision-solution-troubleshooting/module-identity-twin.png":::

1. 必要に応じて **TelemetryIntervalNeuralNetworkMs** の値を更新し、 **[保存]** アイコンを選択します。

## <a name="view-device-rtsp-video-stream"></a>デバイスの RTSP ビデオ ストリームを表示する

デバイスの RTSP ビデオ ストリームは、[Azure Percept Studio](./how-to-view-video-stream.md) または [VLC メディア プレーヤー](https://www.videolan.org/vlc/index.html)で表示します。

VLC メディア プレーヤーで RTSP ストリームを開くには、 **[Media]\(メディア\)**  >  **[Open network stream]\(ネットワーク ストリームを開く\)**  >  **[rtsp://<デバイスの IP アドレス>:8554/result]** に移動します。

## <a name="next-steps"></a>次のステップ

Azure Percept DK のトラブルシューティングの詳細については、[一般的なトラブルシューティング ガイド](./troubleshoot-dev-kit.md)を参照してください。