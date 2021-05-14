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
ms.openlocfilehash: fffbb01cba89335c47cc1ec8f5ea5dcb52b318de
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2021
ms.locfileid: "109508348"
---
<!-- This tells how to get the connection string for the service shared access policy of your IoT hub -->

**サービス** ポリシーの IoT Hub 接続文字列を取得するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) で、 **[リソース グループ]** を選択します。 ハブが配置されているリソース グループを選択し、リソースの一覧からハブを選択します。

1. IoT ハブの左側のウィンドウで、 **[共有アクセス ポリシー]** を選択します。

1. ポリシーの一覧から、**サービス** ポリシーを選択します。

1. **[共有アクセス キー]** で、 **[接続文字列 - プライマリ キー]** のコピー アイコンを選択し、その値を保存します。

    ![接続文字列を取得する方法を示す画面](./media/iot-hub-include-find-service-connection-string/iot-hub-get-connection-string.png)

IoT Hub の共有アクセス ポリシーとアクセス許可の詳細については、「[アクセス制御とアクセス許可](../articles/iot-hub/iot-hub-dev-guide-sas.md#access-control-and-permissions)」を参照してください。
