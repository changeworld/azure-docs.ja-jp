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
ms.openlocfilehash: ce13f1cdd3effdd08bfa76a996587edc213c5701
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130287689"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

**registryReadWrite** ポリシーの IoT Hub 接続文字列を取得するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) で、 **[リソース グループ]** を選択します。 ハブが配置されているリソース グループを選択し、リソースの一覧からハブを選択します。

2. ハブの左側のウィンドウで、 **[共有アクセス ポリシー]** を選択します。

3. ポリシーの一覧から、 **[registryReadWrite]** ポリシーを選択します。

4. **[共有アクセス キー]** の **[プライマリ接続文字列]** のコピー アイコンを選び、その値を保存します。

    :::image type="content" source="./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png" alt-text="接続文字列を取得する方法を示すスクリーン キャプチャ" border="true":::

IoT Hub の共有アクセス ポリシーとアクセス許可の詳細については、「[アクセス制御とアクセス許可](../articles/iot-hub/iot-hub-dev-guide-sas.md#access-control-and-permissions)」を参照してください。
