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
ms.openlocfilehash: 1a0502568c1673bcd7f57d3e9bc9c95ed90bbefa
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756944"
---
<!-- This tells how to create a custom shared access policy that has service connect and registry RW permissions for your IoT hub and get the connection string for it-->

**サービス接続**および**レジストリ書き込み**のアクセス許可を付与する共有アクセス ポリシーを作成し、そのポリシーの接続文字列を取得するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) で、 **[リソース グループ]** を選択します。 ハブが配置されているリソース グループを選択し、リソースの一覧からハブを選択します。

1. ハブの左側のウィンドウで、 **[共有アクセス ポリシー]** を選択します。

1. ポリシーの一覧の上にあるトップ メニューから **[追加]** を選択します。

1. **[共有アクセス ポリシーを追加]** の下で、ポリシーのわかりやすい名前を入力します (*serviceAndRegistryReadWrite* など)。 **[アクセス許可]** で **[レジストリ書き込み]** と **[サービス接続]** を選択し、 **[作成]** を選択します ( **[レジストリ読み取り]** アクセス許可は、 **[レジストリ書き込み]** を選択すると自動的に含まれます)。

    ![新しい共有アクセス ポリシーを追加する方法を示す画面](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-add-svc-regrw-policy.png)

1. ポリシーの一覧から、新しいポリシーを選択します。

1. **[共有アクセス キー]** で、 **[接続文字列 - プライマリ キー]** のコピー アイコンを選択し、その値を保存します。

    ![接続文字列を取得する方法を示す画面](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-get-connection-string.png)

IoT Hub の共有アクセス ポリシーとアクセス許可の詳細については、「[アクセス制御とアクセス許可](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)」を参照してください。
