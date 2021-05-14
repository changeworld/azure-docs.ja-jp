---
title: インクルード ファイル
description: インクルード ファイル
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 5c3ea37730fb0833b700f115221466e8061c999a
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2021
ms.locfileid: "109508469"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

**registryReadWrite** ポリシーの IoT Hub 接続文字列を取得するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) で、 **[リソース グループ]** を選択します。 ハブが配置されているリソース グループを選択し、リソースの一覧からハブを選択します。

2. ハブの左側のウィンドウで、 **[共有アクセス ポリシー]** を選択します。

3. ポリシーの一覧から、 **[registryReadWrite]** ポリシーを選択します。

4. **[共有アクセス キー]** で、 **[接続文字列 - プライマリ キー]** のコピー アイコンを選択し、その値を保存します。

    ![接続文字列を取得する方法を示す画面](./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png)

IoT Hub の共有アクセス ポリシーとアクセス許可の詳細については、「[アクセス制御とアクセス許可](../articles/iot-hub/iot-hub-dev-guide-sas.md#access-control-and-permissions)」を参照してください。
