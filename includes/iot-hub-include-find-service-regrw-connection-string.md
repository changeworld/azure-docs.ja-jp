---
title: インクルード ファイル
description: インクルード ファイル
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 04/03/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 9d5710e78b323af65a12b1f92095cdfa1f385ecb
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130288227"
---
<!-- This tells how to create a custom shared access policy that has service connect and registry RW permissions for your IoT hub and get the connection string for it-->

**サービス接続** および **レジストリ書き込み** のアクセス許可を付与する共有アクセス ポリシーを作成し、そのポリシーの接続文字列を取得するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) で、 **[リソース グループ]** を選択します。 ハブが配置されているリソース グループを選択し、リソースの一覧からハブを選択します。

1. ハブの左側のウィンドウで、 **[共有アクセス ポリシー]** を選択します。

1. ポリシー一覧の上にあるメニューから、 **[共有アクセス ポリシーの追加]** を選びます。

1. **[共有アクセス ポリシーを追加]** の下で、ポリシーのわかりやすい名前を入力します (*serviceAndRegistryReadWrite* など)。 **[アクセス許可]** で **[レジストリ書き込み]** と **[サービス接続]** を選び、 **[追加]** を選びます ( **[レジストリ読み取り]** アクセス許可は、 **[レジストリ書き込み]** を選択すると自動的に含まれます)。

    :::image type="content" source="./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-add-svc-regrw-policy.png" alt-text="新しい共有アクセス ポリシーを追加する方法を示すスクリーン キャプチャ" border="true":::

1. ポリシーの一覧から、新しいポリシーを選択します。

1. **[共有アクセス キー]** の **[プライマリ接続文字列]** のコピー アイコンを選び、その値を保存します。

    :::image type="content" source="./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-get-connection-string.png" alt-text="接続文字列を取得する方法を示すスクリーン キャプチャ" border="true":::

IoT Hub の共有アクセス ポリシーとアクセス許可の詳細については、「[アクセス制御とアクセス許可](../articles/iot-hub/iot-hub-dev-guide-sas.md#access-control-and-permissions)」を参照してください。
