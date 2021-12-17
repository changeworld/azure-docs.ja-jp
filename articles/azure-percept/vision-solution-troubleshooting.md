---
title: Azure Percept Vision とビジョン モジュールのトラブルシューティング
description: ビジョン AI のプロトタイプ作成エクスペリエンスで多く見られるいくつかの問題をトラブルシューティングするためのヒントを紹介します。
author: NabilaBabar
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/29/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 0863a4e327749618c215343f1f43fc5a2550a3bf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087497"
---
# <a name="troubleshoot-azure-percept-vision-and-vision-modules"></a>Azure Percept Vision とビジョン モジュールのトラブルシューティング

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

## <a name="check-the-runtime-status-of-azureeyemodule"></a>azureeyemodule のランタイムの状態を確認する

**WebStreamModule** に問題がある場合は、ビジョン モデル推論を処理する **azureeyemodule** が実行されていることを確認してください。 ランタイムの状態を確認するには、次の操作を行います。

1. [Azure portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home)にアクセスし、 **[すべてのリソース]**  >  *\<your IoT hub>*  >  **[IoT Edge]**  >  *\<your device ID>* にアクセスし ます。 
1. **[モジュール]** タブを選択すると、インストールされているすべてのモジュールのランタイムの状態が表示されます。

   :::image type="content" source="./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page-inline.png" alt-text="デバイス モジュールのランタイム状態画面を示すスクリーンショット。" lightbox= "./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page.png":::

1. **azureeyemodule** のランタイムの状態が **[実行中]** になっていない場合は、 **[モジュールの設定]**  >  **[azureeyemodule]** を選択します。 
1. **[モジュールの設定]** ページの **[必要な状態]** を **[実行中]** に設定し、 **[更新]** を選択します。

    :::image type="content" source="./media/vision-solution-troubleshooting/firmware-desired-status-stopped.png" alt-text="モジュール設定の構成画面を示すスクリーンショット。":::

## <a name="change-how-often-messages-are-sent-from-the-azureeyemodule"></a>azureeyemodule からのメッセージ送信の頻度を変更する

お客様のサブスクリプション レベルでは、お使いのデバイスから IoT Hub に送ることができるメッセージ数が制限されている可能性があります。 たとえば Free レベルでは、メッセージ数が 1 日あたり 8,000 件に制限されます。 その制限に達すると、azureeyemodule の機能が停止し、次のエラーが発生します。

|エラー メッセージ|
|------|
|*Total number of messages on IotHub 'xxxxxxxxx' exceeded the allocated quota. Max allowed message count: '8000', current message count: 'xxxx'. Send and Receive operations are blocked for this hub until the next UTC day. Consider increasing the units for this hub to increase the quota. (IotHub 'xxxxxxxxx' のメッセージの総数が割り当てられたクォータを超えました。許可される最大メッセージ数: '8000'、現在のメッセージ数: 'xxxx'。このハブの送受信操作は、次の UTC 日までブロックされます。クォータを増やすには、このハブのユニットの増加を検討してください。)*|

azureeyemodule モジュール ツインを使用すると、メッセージの送信頻度の間隔を変更できます。 この間隔に入力された値によって、各メッセージが送信される頻度がミリ秒単位で示されます。 数値が大きいほど、各メッセージ間の時間が長くなります。 たとえば、間隔を 12,000 に設定すると、12 秒ごとに 1 つのメッセージが送信されます。 1 日を通して実行されているモデルの場合、この数値では 1 日あたり 7,200 件のメッセージになります。これは、Free レベルの制限以下です。 選択する値は、ビジョン モデルの応答性によって変わります。

> [!NOTE]
> メッセージ間隔を変更しても、各メッセージのサイズに影響はありません。 メッセージ サイズは、モデルの種類や各メッセージで検出されるオブジェクトの数など、いくつかの異なる要因によって変わります。 そのため、メッセージ サイズを決定するのは困難です。

メッセージ間隔を更新するには、こちらの手順に従います。

1. [Azure portal](https://ms.portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod#home) にサインインし、 **[すべてのリソース]** を開きます。

1. **[すべてのリソース]** ページで、セットアップ中に開発キットにプロビジョニングされた IoT ハブの名前を選択します。

1. **[IoT Hub]** ページの左側の **[デバイスの自動管理]** で、 **[IoT Edge]** を選択します。 [IoT Edge デバイス] ページで、開発キットのデバイス ID を探します。 開発キットのデバイス ID を選択して、その [IoT Edge デバイス] ページを開きます。

1. **[モジュール]** タブで **[azureeyemodule]** を選択します。

1. **[azureeyemodule]** ページで、 **[モジュール ID ツイン]** を開きます。

    :::image type="content" source="./media/vision-solution-troubleshooting/module-page-inline.png" alt-text="モジュール ページのスクリーンショット。" lightbox= "./media/vision-solution-troubleshooting/module-page.png":::

1. 下にスクロールして、 **[プロパティ]** に移動します。
1. **[TelemetryInterval]** を見つけて、それを **[TelemetryIntervalNeuralNetworkMs]** に置き換えます。

    :::image type="content" source="./media/vision-solution-troubleshooting/module-identity-twin-inline-02.png" alt-text="モジュール ツインのプロパティのスクリーンショット。" lightbox= "./media/vision-solution-troubleshooting/module-identity-twin.png":::

1. **[TelemetryIntervalNeuralNetworkMs]** の値を必要な値に更新します。

1. **[Save]\(保存\)** アイコンを選択します。

## <a name="view-device-rtsp-video-stream"></a>デバイスの RTSP ビデオ ストリームを表示する

デバイスの RTSP ビデオ ストリームは、[Azure Percept Studio](./how-to-view-video-stream.md) または [VLC メディア プレーヤー](https://www.videolan.org/vlc/index.html)で表示します。

VLC メディア プレーヤーで RTSP ストリームを開くには、 **[Media]\(メディア\)**  >  **[Open network stream]\(ネットワーク ストリームを開く\)**  >  **[rtsp://<デバイスの IP アドレス>:8554/result]** に移動します。

RTSP ストリームが灰色のボックスによって部分的にブロックされている場合、低速なネットワーク接続を使用して表示しようとしている可能性があります。 ビデオ ストリームのための十分な帯域幅が接続にあることを確認してください。

## <a name="next-steps"></a>次のステップ

Azure Percept DK のトラブルシューティングの詳細については、[一般的なトラブルシューティング ガイド](./troubleshoot-dev-kit.md)を参照してください。
