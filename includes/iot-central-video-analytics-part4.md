---
title: インクルード ファイル
description: インクルード ファイル
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: de916fcbe0623185821e2f5da15a8f9cf71dfd4e
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94426779"
---
### <a name="publish-the-device-template"></a>デバイス テンプレートを発行する

デバイスをアプリケーションに追加する前に、デバイス テンプレートを発行する必要があります。

1. **LVA Edge Gateway v2** デバイス テンプレートで **[発行]** を選択します。

1. **[このデバイス テンプレートのアプリケーションへの発行]** ページで、 **[発行]** を選択します。

アプリケーションの **[デバイス]** ページに、使用するデバイスの種類として **LVA Edge Gateway v2** が表示されるようになります。

## <a name="migrate-the-gateway-device"></a>ゲートウェイ デバイスを移行する

既存の **gateway-001** デバイスは、**LVA Edge Gateway** デバイス テンプレートを使用します。 新しいデプロイ マニフェストを使用するには、デバイスを新しいデバイス テンプレートに移行します。

**gateway-001** デバイスを移行するには、次のようにします。

1. **[デバイス]** ページに移動し、 **[gateway-001]** デバイスを選択します。一覧で、このデバイスが強調表示されます。

1. **[移行]** を選択します。 **[移行]** アイコンが表示されない場合は、 **[...]** を選択して、その他のオプションを表示します。

    :::image type="content" source="media/iot-central-video-analytics-part4/migrate-device.png" alt-text="ゲートウェイ デバイスを新しいバージョンに移行する":::

1. **[移行]** ダイアログの一覧で、 **[LVA Edge Gateway v2]** を選択し、 **[移行]** を選択します。

数秒後、移行が完了します。 デバイスでは、カスタマイズしたデプロイ マニフェストと共に **LVA Edge Gateway v2** デバイス テンプレートが使用されるようになりました。

### <a name="get-the-device-credentials"></a>デバイスの資格情報を取得する

お使いの IoT Central アプリケーションへの接続をデバイスに許可する資格情報が必要になります。 デバイスの資格情報を取得するには、次の手順を実行します。

1. **[デバイス]** ページで、**gateway-001** デバイスを選択します。

1. **[接続]** を選択します。

1. **[デバイス接続]** ページで、**ID スコープ**、**デバイス ID**、デバイスの **プライマリ キー** を *scratchpad.txt* ファイルに書き留めます。 これらの値は後で使用します。

1. 接続方法が **Shared Access Signature** に設定されていることを確認します。

1. **[閉じる]** を選択します。

## <a name="next-steps"></a>次のステップ

**ビデオ分析 (物体とモーションの検出)** アプリケーション テンプレートを使用して IoT Central アプリケーションを作成し、ゲートウェイ デバイス用のデバイス テンプレートを作成して、ゲートウェイ デバイスをアプリケーションに追加しました。

クラウド VM を実行する IoT Edge モジュールとシミュレートされたビデオ ストリームを使用して、ビデオ分析 (物体とモーションの検出) アプリケーションを試したい場合:

> [!div class="nextstepaction"]
> [ビデオ分析用の IoT Edge インスタンスを作成する (Linux VM)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-vm.md)

実デバイスを実行する IoT Edge モジュールと実際の **ONVIF** カメラを使用して、ビデオ分析 (物体とモーションの検出) アプリケーションを試したい場合:

> [!div class="nextstepaction"]
> [ビデオ分析用の IoT Edge インスタンスを作成する (Intel NUC)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-nuc.md)
