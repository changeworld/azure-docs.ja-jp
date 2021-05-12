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
ms.openlocfilehash: 605a0441bd01ab88edb482c0ade22a6f21f4c8ba
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2021
ms.locfileid: "109508399"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

**サービス接続** と **レジストリ読み取り** のアクセス許可を付与する共有アクセス ポリシーを作成し、そのポリシーの接続文字列を取得するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) で、 **[リソース グループ]** を選択します。 ハブが配置されているリソース グループを選択し、リソースの一覧からハブを選択します。

1. ハブの左側のウィンドウで、 **[共有アクセス ポリシー]** を選択します。

1. ポリシーの一覧の上にあるトップ メニューから **[追加]** を選択します。

1. **[共有アクセス ポリシーを追加]** の下で、ポリシーのわかりやすい名前を入力します (*serviceAndRegistryRead* など)。 **[アクセス許可]** で **[レジストリ読み取り]** と **[サービス接続]** を選択し、 **[作成]** を選択します。

    ![新しい共有アクセス ポリシーを追加する方法を示す画面](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

1. ポリシーの一覧から、新しいポリシーを選択します。

1. **[共有アクセス キー]** で、 **[接続文字列 - プライマリ キー]** のコピー アイコンを選択し、その値を保存します。

    ![接続文字列を取得する方法を示す画面](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

IoT Hub の共有アクセス ポリシーとアクセス許可の詳細については、「[アクセス制御とアクセス許可](../articles/iot-hub/iot-hub-dev-guide-sas.md#access-control-and-permissions)」を参照してください。
