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
ms.openlocfilehash: 8d7ac457041474f4e774414b1d5e6f9ed09dc856
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "67181272"
---
<!-- this tells how to get the connection string for your hub -->
<!-- This assumes the user is looking at his hub in the portal. -->

ハブが作成されたら、そのハブの接続文字列を取得します。 これは、デバイスとアプリケーションをハブに接続する際に使用します。 

1. ハブをクリックして、[IoT Hub] ウィンドウを表示します。このウィンドウには、[設定] などが表示されます。 **[共有アクセス ポリシー]** をクリックします。
   
2. **[共有アクセス ポリシー]** から **[iothubowner]** ポリシーを選びます。 

3. 後で使用するために、 **[共有アクセス キー]** の **[接続文字列 --- 主キー]** をコピーします。

    ![接続文字列を取得する方法を示す画面](./media/iot-hub-include-find-connection-string/iot-hub-get-connection-string.png)

    詳細については、「IoT Hub 開発者ガイド」の[アクセス制御](../articles/iot-hub/iot-hub-devguide-security.md)に関するページを参照してください。
