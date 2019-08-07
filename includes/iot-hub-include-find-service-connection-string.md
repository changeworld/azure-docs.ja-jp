---
title: インクルード ファイル
description: インクルード ファイル
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/16/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: b6ca43616a2e7e7611b122bce5c95084e1fd5012
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68402393"
---
<!-- This tells how to get the connection string for the service shared access policy of your IoT hub -->

**サービス** ポリシーの IoT Hub 接続文字列を取得するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) で IoT ハブを開きます。  IoT ハブに移動するための最も簡単な方法は、 **[リソース グループ]** を選択し、IoT ハブがあるリソース グループを選択した後、リソースの一覧から目的の IoT ハブを選択することです。

2. IoT ハブの左側のウィンドウで、 **[共有アクセス ポリシー]** を選択します。

3. ポリシーの一覧から、**サービス** ポリシーを選択します。

4. **[共有アクセス キー]** で、 **[接続文字列 - プライマリ キー]** のコピー アイコンを選択し、その値を保存します。

    ![接続文字列を取得する方法を示す画面](./media/iot-hub-include-find-service-connection-string/iot-hub-get-connection-string.png)

IoT Hub の共有アクセス ポリシーとアクセス許可の詳細については、「[アクセス制御とアクセス許可](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)」を参照してください。
