---
title: インクルード ファイル
description: インクルード ファイル
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9f7fee71fb2b80be93d978569791dbb57f137949
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346836"
---
## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

IoT Hub を作成したら、その IoT Hub にデバイスとアプリケーションを接続するために必要な重要な情報を把握します。 

IoT Hub ナビゲーション メニューの**共有アクセス ポリシー**を開きます。 **[iothubowner]** ポリシーを選択し、IoT Hub の **[接続文字列 --- 主キー]** をコピーします。 詳細については、「[IoT Hub へのアクセスの制御](../articles/iot-hub/iot-hub-devguide-security.md)」を参照してください。

   > [!NOTE] 
   > このセットアップ チュートリアルでは、iothubowner 接続文字列は必要ありません。 ただし、このセットアップの完了後、一部のチュートリアルや異なる IoT シナリオでは必要になる場合があります。

   ![IoT Hub の接続文字列を取得する](./media/iot-hub-get-started-create-hub-and-device/create-iot-hub5.png)

## <a name="register-your-device-in-the-iot-hub"></a>IoT ハブへのデバイスの登録

1. IoT Hub ナビゲーション メニューの **[IoT デバイス]** を開き、**[追加]** をクリックして IoT Hub でデバイスを登録します。

   ![IoT ハブの [IoT デバイス] でデバイスを追加する](./media/iot-hub-get-started-create-hub-and-device/create-identity-portal.png)

2. 新しいデバイスの **[デバイス ID]** を入力します。 デバイス ID には大文字と小文字の区別があります。

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

3. **[Save]** をクリックします。

4. デバイスが作成された後、**[IoT デバイス]** ウィンドウの一覧からデバイスを開きます。

5. 後で使用するために **[接続文字列 --- 主キー]** をコピーします。

   ![デバイスの接続文字列を取得する](./media/iot-hub-get-started-create-hub-and-device/device-connection-string.png)
