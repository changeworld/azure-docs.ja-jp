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
ms.openlocfilehash: 55dc712d323c43849fa3de23c83e29fc348a0f1e
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68403991"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

**サービス接続**と**レジストリ読み取り**のアクセス許可を付与する共有アクセス ポリシーを作成し、そのポリシーの接続文字列を取得するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) で IoT ハブを開きます。 IoT ハブに移動するための最も簡単な方法として、 **[リソース グループ]** を選択し、IoT ハブがあるリソース グループを選択した後、リソースの一覧から目的の IoT ハブを選択します。

2. IoT ハブの左側のウィンドウで、 **[共有アクセス ポリシー]** を選択します。

3. ポリシーの一覧の上にあるトップ メニューから **[追加]** を選択します。

4. **[共有アクセスポリシーを追加]** ウィンドウで、ポリシーのわかりやすい名前を入力します (例: *serviceAndRegistryRead*)。 **[アクセス許可]** で **[レジストリ読み取り]** と **[サービス接続]** を選択し、 **[作成]** を選択します。

    ![新しい共有アクセス ポリシーを追加する方法を示す画面](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

5. **[共有アクセス ポリシー]** ウィンドウに戻り、ポリシーの一覧から新しいポリシーを選択します。

6. **[共有アクセス キー]** で、 **[接続文字列 - プライマリ キー]** のコピー アイコンを選択してその値を保存します。

    ![接続文字列を取得する方法を示す画面](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

IoT Hub の共有アクセス ポリシーとアクセス許可の詳細については、「[アクセス制御とアクセス許可](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)」を参照してください。
