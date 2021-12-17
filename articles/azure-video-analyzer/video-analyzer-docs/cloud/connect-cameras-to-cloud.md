---
title: Azure Video Analyzer サービスへのカメラの接続
description: この記事では、Azure Video Analyzer サービスにカメラを直接接続する方法について説明します。
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: f463fafb4f11e935d45c42326c23e1a0e4ffb28f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297699"
---
# <a name="connect-cameras-to-the-cloud"></a>カメラをクラウドに接続する

[!INCLUDE [header](includes/cloud-env.md)]

Azure Video Analyzer サービスを使用すると、ユーザーは RTSP カメラを直接クラウドに接続し、[ライブ パイプライン](../pipeline.md)を使用してビデオをキャプチャして記録することができます。 これにより、エッジ デバイスの計算負荷が軽減されるか、エッジ デバイスが完全に不要になります。 Video Analyzer サービスでは現在、カメラをクラウドに接続するための 3 つの異なる方法がサポートされています。これらは、リモート デバイス アダプター経由で接続する方法、IoT PnP コマンドを使用してファイアウォールの背後から接続する方法、ファイアウォールを使用せずにインターネット経由で接続する方法です。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/connect-cameras-to-cloud/connect-cameras-to-cloud.svg" alt-text="カメラをクラウドに接続するための 3 つの異なる方法":::

## <a name="connect-via-a-remote-device-adapter"></a>リモート デバイス アダプター経由で接続する

Video Analyzer エッジ モジュールを RTSP カメラと同じ (プライベート) ネットワーク上の IoT Edge デバイスにデプロイし、エッジ デバイスをインターネットに接続できます。 これでエッジ モジュールは、Video Analyzer サービスを *リモート デバイス* (カメラ) に接続できる *アダプター* として設定できます。 エッジ モジュールは、RTSP カメラと Video Analyzer サービス間のビデオ トラフィックの[透過的なゲートウェイ](../../../iot-edge/iot-edge-as-gateway.md)として機能します。 このアプローチは次のシナリオで役立ちます。

* カメラおよびデバイスをインターネットへの露出から保護する必要がある場合
* カメラおよびデバイスに、IoT Hub に単独で接続する機能がない場合
* 電源、スペース、その他の考慮事項によって、軽量エッジ デバイスのみをオンプレミスにデプロイできる場合

Video Analyzer エッジ モジュールは、カメラから IoT Hub へのメッセージングとテレメトリの透過的なゲートウェイとして機能するのではなく、ビデオの透過的なゲートウェイとしてのみ機能します。

## <a name="connect-behind-a-firewall-using-an-iot-pnp-device-implementation"></a>IoT PnP デバイス実装を使用してファイアウォールの背後で接続する

この方法を使用すると、RTSP カメラまたはデバイスを、[IoT プラグ アンド プレイ コマンド インターフェイス](../../../iot-develop/overview-iot-plug-and-play.md)を使用してファイアウォールの背後にある Video Analyzer に接続でき、エッジ デバイスが不要になります。 この方法では、適切な IoT PnP デバイス実装をカメラまたはデバイスにインストールして実行する必要があります。 あらゆる製造元の互換性のあるデバイスを接続する方法については、[こちら](connect-devices.md)を参照してください。

## <a name="connect-over-the-internet-no-firewall"></a>インターネット経由で接続する (ファイアウォールなし)

この方法は、ファイアウォールなしでインターネット経由で Video Analyzer サービスからデバイスにアクセスすることが許容される、監視対象の概念実証演習にのみ使用する必要があります。 

関連する使用例は、この[クイックスタート](get-started-livepipelines-portal.md)に記載されているように、RTSP カメラをシミュレートするためにモジュールを Azure VM にデプロイする場合です。


## <a name="next-steps"></a>次の手順

- リモート デバイス アダプター経由でカメラを接続するには、[この攻略ガイド](use-remote-device-adapter.md)に従ってください
- IoT PnP 実装を使用してデバイスを接続する方法については、[この攻略ガイド](connect-devices.md)に従ってください
- インターネット経由でカメラを接続する方法については、[このクイックスタート](get-started-livepipelines-portal.md)に従ってください
