---
title: Azure Data Box Edge デバイスを監視する | Microsoft Docs
description: Azure portal とローカル Web UI を使用して Azure Data Box Edge を監視する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 188f5c6cfbb4650ad1ff767955d064f8e0c3cb70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "60756758"
---
# <a name="monitor-your-azure-data-box-edge"></a>Azure Data Box Edge を監視する

この記事では、Azure Data Box Edge を監視する方法について説明します。 このデバイスは Azure portal またはローカル Web UI を使用して監視できます。 デバイスのイベントを確認したり、アラートを構成して管理したり、メトリックを確認したりするには、Azure portal を使用します。 さまざまなデバイス コンポーネントのハードウェアの状態を確認するには、物理デバイスのローカル Web UI を使用します。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * デバイス イベントとそれに対応するアラートを確認する
> * デバイス コンポーネントのハードウェアの状態を確認する
> * デバイスの容量とトランザクションのメトリックを確認する
> * アラートを構成して管理する

## <a name="view-device-events"></a>デバイスのイベントを確認する

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>ハードウェアの状態を確認する

デバイス コンポーネントのハードウェアの状態を確認するには、ローカル Web UI から次の手順を実行します。

1. お使いのデバイスのローカル Web UI に接続します。
2. **[メンテナンス] > [ハードウェアの状態]** に移動します。 さまざまなデバイス コンポーネントの正常性を確認できます。

    ![ハードウェアの状態を確認する](media/data-box-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>メトリックを表示する

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

## <a name="manage-alerts"></a>Manage alerts

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-manage-alerts.md)]

## <a name="next-steps"></a>次のステップ 

[帯域幅を管理する](data-box-edge-manage-bandwidth-schedules.md)方法について学習します。