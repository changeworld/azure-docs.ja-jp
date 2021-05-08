---
title: 特定のゾーン上のデバイスについて
description: オンプレミスの管理コンソールを使用して、特定のゾーンごとに、包括的なビュー情報を取得します
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 03/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 685d7b1df4389c356ee7b531d179919025d298d0
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104776106"
---
# <a name="view-information-per-zone"></a>ゾーンごとに情報を表示する


## <a name="view-a-device-map-for-a-zone"></a>ゾーンのデバイス マップを表示する

センサー上で選択したゾーンのデバイス マップを表示します。 このビューでは、選択したゾーンに関連するすべてのネットワーク要素 (センサー、その接続元のデバイス、およびその他の情報) が表示されます。

:::image type="content" source="media/how-to-work-with-asset-inventory-information/zone-map-screenshot.png" alt-text="ゾーン マップのスクリーンショット。":::


- **[サイトの管理]** ウィンドウで、ゾーン名が表示されているバーで **[ゾーン マップの表示]** を選択します。

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-region-to-default-business-unit-v2.png" alt-text="既定のリージョンから既定の事業単位。":::

**[デバイス マップ]** ウィンドウが表示されます。 マップからデバイスとデバイス情報を表示するには、次のツールを使用できます。 これらの各機能の詳細については、*Defender for IoT プラットフォーム ユーザー ガイド* を参照してください。

- **マップの拡大ビュー**: 簡略化されたビュー、接続ビュー、および詳細ビュー。 表示されるマップ ビューは、マップのズーム レベルによって異なります。 ズーム レベルを調整することで、マップ ビューを切り替えることができます。

  :::image type="icon" source="media/how-to-work-with-asset-inventory-information/zoom-icon.png" border="false":::

- **マップの検索とレイアウトのツール**: さまざまなネットワーク セグメント、デバイス、デバイス グループ、またはレイヤーを表示するために使用されるツール。

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/search-and-layout-tools.png" alt-text="検索およびレイアウト ツールのビューのスクリーンショット。":::

- **デバイスのラベルとインジケーター:** たとえば、IT ネットワークのサブネットにグループ化されたデバイスの数などです。 この例では、8 です。

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/labels-and-indicators.png" alt-text="ラベルとインジケーターのスクリーンショット。":::

- **デバイスのプロパティの表示**: たとえば、デバイスとデバイスの基本的なプロパティを監視しているセンサーなどです。 デバイスを右クリックすると、デバイスのプロパティが表示されます。

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-properties-v2.png" alt-text="デバイスのプロパティ ビューのスクリーンショット。":::

- **デバイスに関連付けられているアラート:** デバイスを右クリックすると、関連するアラートが表示されます。

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/show-alerts.png" alt-text="[アラートの表示] ビューのスクリーンショット。":::

## <a name="view-alerts-associated-with-a-zone"></a>ゾーンに関連するアラートを表示する

特定のゾーンに関連するアラートを表示するには、次の操作を行います。

- **[ゾーン]** ウィンドウでアラート アイコンを選択します。 

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/business-unit-view-v2.png" alt-text="例を含む既定の事業単位ビュー。":::

詳細については、「[概要:アラートの使用](how-to-work-with-alerts-on-premises-management-console.md)」を参照してください。

### <a name="view-the-device-inventory-of-a-zone"></a>ゾーンのデバイス インベントリを表示する

特定のゾーンに関連するデバイス インベントリを表示するには、次の操作を行います。

- **[ゾーン]** ウィンドウから **[デバイス インベントリの表示]** を選択します。

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-business-unit.png" alt-text="デバイス インベントリ画面が表示されます。":::

詳細については、[デバイス インベントリのすべてのエンタープライズ センサー検出の調査](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)に関するページを参照してください。

## <a name="view-additional-zone-information"></a>その他のゾーン情報を表示する

利用可能なその他のゾーン情報は以下のとおりです。

- **ゾーンの詳細**:ゾーンに関連付けられているデバイス、アラート、センサーの数を表示します。

- **センサーの詳細**:ゾーンに割り当てられている各センサーの名前、IP アドレス、およびバージョンを表示します。

- **接続性の状態**:センサーが切断されている場合は、センサーから接続します。 [オンプレミスの管理コンソールへのセンサーの接続](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console)に関するページを参照してください。 

- **更新の進行状況**:接続されているセンサーがアップグレード中の場合は、アップグレードの状態が表示されます。 アップグレード中、オンプレミスの管理コンソールはセンサーからデバイス情報を受信しません。

## <a name="next-steps"></a>次のステップ

[グローバル、地域、ローカルの脅威に関する分析情報を得る](how-to-gain-insight-into-global-regional-and-local-threats.md)
