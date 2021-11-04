---
title: インクルード ファイル
description: インクルード ファイル
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/20/2021
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 0a2e035cdc5118cc7d952e4046e9093f95094585
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070410"
---
IoT Central アプリケーションがイベント ハブにデータを安全にエクスポートできるようにするマネージド ID を構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) で IoT Central アプリケーションに移動します。

    > [!TIP]
    > 既定では、IoT Central アプリケーションは、サブスクリプションの **IOTC** リソース グループに作成されます。

1. **[ID]** を選択します。 次に、 **[システム割り当て済み]** ページで、状態を **[オン]** に 変更し、 **[保存]** を選択します。

1. 数秒後に、システムによって割り当てられたIoT Central アプリケーションのマネージド ID が有効になるので、 **[Azure のロールの割り当て]** を選択できます。

    :::image type="content" source="media/iot-central-managed-identity/azure-role-assignments.png" alt-text="Azure portal の IoT Central アプリケーションの ID ページのスクリーンショット。":::

1. **[Azure のロールの割り当て]** ページで、 **[ロールの割り当ての追加]** を選択します。
