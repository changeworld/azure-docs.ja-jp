---
title: インクルード ファイル
description: インクルード ファイル
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: fc2dd8938f627be669519b843a97b87ddf1e3203
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130288236"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

**サービス接続** と **レジストリ読み取り** のアクセス許可を付与する共有アクセス ポリシーを作成し、そのポリシーの接続文字列を取得するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) で、 **[リソース グループ]** を選択します。 ハブが配置されているリソース グループを選択し、リソースの一覧からハブを選択します。

1. ハブの左側のウィンドウで、 **[共有アクセス ポリシー]** を選択します。

1. ポリシーの一覧の上にあるトップ メニューから **[追加]** を選択します。

1. **[共有アクセス ポリシーを追加]** にポリシーのわかりやすい名前を入力します (*serviceAndRegistryRead* など)。 **[アクセス許可]** で **[レジストリ読み取り]** と **[サービス接続]** を選び、 **[追加]** を選びます。

    :::image type="content" source="./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png" alt-text="新しい共有アクセス ポリシーを追加する方法を示すスクリーン キャプチャ" border="true":::

1. ポリシーの一覧から、新しいポリシーを選択します。

1. **[プライマリ接続文字列]** のコピー アイコンを選び、その値を保存します。

    :::image type="content" source="./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png" alt-text="接続文字列を取得する方法を示すスクリーン キャプチャ" border="true":::

IoT Hub の共有アクセス ポリシーとアクセス許可の詳細については、「[アクセス制御とアクセス許可](../articles/iot-hub/iot-hub-dev-guide-sas.md#access-control-and-permissions)」を参照してください。
