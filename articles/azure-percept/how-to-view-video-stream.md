---
title: Azure Percept DK の RTSP ビデオ ストリームを表示する
description: Azure Percept DK からの RTSP ビデオ ストリームを表示する方法を調べる
author: NabilaBabar
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 19b47b0971ee59d7b029a7b6b0dde77a83d1447b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006242"
---
# <a name="view-your-azure-percept-dk-rtsp-video-stream"></a>Azure Percept DK の RTSP ビデオ ストリームを表示する

Azure Percept Studio 内で Azure Percept DK からの RTSP ビデオ ストリームを表示するには、このガイドに従ってください。 デバイスにデプロイされたビジョン AI モデルからの推論が Web ストリームで表示できるようになります。

## <a name="prerequisites"></a>前提条件

- Azure Percept DK (開発キット)
- [Azure サブスクリプション](https://azure.microsoft.com/free/)
- [Azure Percept DK セットアップ エクスペリエンス](./quickstart-percept-dk-set-up.md): 開発キットを Wi-Fi ネットワークに接続し、IoT ハブを作成して、開発キットを IoT ハブに接続済みであること

## <a name="view-the-rtsp-video-stream"></a>RTSP ビデオストリームを表示する

1. 開発キットの電源をオンにします。

1. [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) に移動します。

1. 概要ページの左側にある **[デバイス]** をクリックします。

    :::image type="content" source="./media/how-to-view-video-stream/overview-devices-inline.png" alt-text="Azure Percept Studio の概要画面。" lightbox="./media/how-to-view-video-stream/overview-devices.png":::

1. 一覧から開発キットを選択します。

    :::image type="content" source="./media/how-to-view-video-stream/select-device.png" alt-text="Azure Percept Studio で使用可能なデバイスのスクリーンショット。":::

1. **[View your device stream]\(デバイス ストリームの表示\)** をクリックします。

    :::image type="content" source="./media/how-to-view-video-stream/view-device-stream.png" alt-text="使用可能なビジョン プロジェクト アクションを示すデバイス ページのスクリーンショット。":::

    新しいタブが開き、Azure Percept DK からのライブ Web ストリームが表示されます。

    :::image type="content" source="./media/how-to-view-video-stream/webstream.png" alt-text="デバイス Web ストリームのスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

[Azure Percept DK のテレメトリ](./how-to-view-telemetry.md)を表示する方法を学習します。
