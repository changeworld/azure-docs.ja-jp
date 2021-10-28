---
title: インクルード ファイル
description: インクルード ファイル
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 10/19/2021
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 63afee858ce8f536c5573a3db65957611af16bc3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130287821"
---
エンドポイントとメッセージのルートを設定したら、その構成をポータルで確認できます。 [Azure portal](https://portal.azure.com) にサインインし、**[リソース グループ]** に移動します。 次に、目的のリソース グループを選択し、該当するハブを選択します (このチュートリアルのハブ名は `ContosoTestHub` で始まります)。 [IoT Hub] ウィンドウが表示されます。

:::image type="content" source="./media/iot-hub-include-view-routing-in-portal/01-show-hub-properties.png" alt-text="IoT Hub のプロパティ画面" border="true":::

[IoT Hub] のオプションで、**[メッセージ ルーティング]** を選択します。 設定したルートが正しく表示されます。

![設定したルート](./media/iot-hub-include-view-routing-in-portal/02-show-message-routes.png)

**[メッセージ ルーティング]** 画面で **[カスタム エンドポイント]** を選択すると、そのルートに対して定義したエンドポイントが表示されます。

![ルートに対して設定したエンドポイント](./media/iot-hub-include-view-routing-in-portal/03-show-routing-endpoints.png)