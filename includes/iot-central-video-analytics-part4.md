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
ms.openlocfilehash: 164f5803b6e9e62447423735e98f6e4c36c73f13
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876667"
---
### <a name="add-relationships"></a>リレーションシップの追加

**[LVA Edge Gateway]** デバイス テンプレートの **[Modules/LVA Edge Gateway Module]\(モジュール/LVA Edge Gateway モジュール\)** で **[Relationships]\(リレーションシップ\)** を選択します。 **[+ リレーションシップの追加]** を選択し、次の 2 つのリレーションシップを追加します。

|表示名               |名前          |移行先 |
|-------------------------- |------------- |------ |
|LVA Edge Motion Detector   |既定値を使用します。   |LVA Edge Motion Detector Device |
|LVA Edge Object Detector   |既定値を使用します。   |LVA Edge Object Detector Device |

次に、 **[保存]** を選択します。

:::image type="content" source="media/iot-central-video-analytics-part4/relationships.png" alt-text="リレーションシップの追加":::

### <a name="add-views"></a>ビューを追加する

**LVA Edge Gateway** デバイス テンプレートには、ビューの定義が含まれていません。

デバイス テンプレートにビューを追加するには:

1. **LVA Edge Gateway** デバイス テンプレートで、 **[Views]\(ビュー\)** に移動し、 **[デバイスの視覚化]** タイルを選択します。

1. ビューの名前として「*LVA Edge Gateway device*」と入力します。

1. 次のタイルをビューに追加します。

    * **[デバイス情報]** のプロパティ (**デバイス モデル**、**製造元**、**オペレーティング システム**、**プロセッサ アーキテクチャ**、**ソフトウェア バージョン**、**メモリ合計**、**ストレージの合計**) を含むタイル。
    * **[Free Memory]\(空きメモリ\)** と **[System Heartbeat]\(システム ハートビート\)** のテレメトリ値を含む折れ線グラフのタイル。
    * **[Create Camera]\(カメラの作成\)** 、 **[Delete Camera]\(カメラの削除\)** 、 **[Module Restart]\(モジュールの再起動\)** 、 **[Module Started]\(モジュール開始\)** 、 **[Module Stopped]\(モジュール停止\)** の各イベントを含むイベント履歴タイル。
    * **[IoT Central Client State]\(IoT Central クライアントの状態\)** のテレメトリを表示する最後の既知の値タイル (2x1)。
    * **[Module State]\(モジュールの状態\)** のテレメトリを表示する最後の既知の値タイル (2x1)。
    * **[System Heartbeat]\(システム ハートビート\)** のテレメトリを表示する最後の既知の値タイル (1x1)。
    * **[Connected Cameras]\(接続されているカメラ\)** のテレメトリを表示する最後の既知の値タイル (1x1)。

    :::image type="content" source="media/iot-central-video-analytics-part4/gateway-dashboard.png" alt-text="リレーションシップの追加":::

1. **[保存]** を選択します。

### <a name="publish-the-device-template"></a>デバイス テンプレートを発行する

デバイスをアプリケーションに追加する前に、デバイス テンプレートを発行する必要があります。

1. **LVA Edge Gateway** デバイス テンプレートで **[発行]** を選択します。

1. **[このデバイス テンプレートのアプリケーションへの発行]** ページで、 **[発行]** を選択します。

アプリケーションの **[デバイス]** ページに、使用するデバイスの種類として **LVA Edge Gateway** が表示されるようになります。

## <a name="add-a-gateway-device"></a>ゲートウェイ デバイスを追加する

**LVA Edge Gateway** デバイスをアプリケーションに追加するには:

1. **[デバイス]** ページに移動し、 **[LVA Edge Gateway]** デバイス テンプレートを選択します。

1. **[+新規]** を選択します。

1. **[新しいデバイスを作成します]** ダイアログで、デバイス名を *LVA Gateway 001* に変更し、デバイス ID を *lva-gateway-001* に変更します。

    > [!NOTE]
    > デバイス ID は、アプリケーション内で一意である必要があります。

1. **［作成］** を選択します

デバイスの状態が **[登録済み]** になります。

### <a name="get-the-device-credentials"></a>デバイスの資格情報を取得する

お使いの IoT Central アプリケーションへの接続をデバイスに許可する資格情報が必要になります。 デバイスの資格情報を取得するには、次の手順を実行します。

1. **[デバイス]** ページで、先ほど作成した **lva-gateway-001** デバイスを選択します。

1. **[接続]** を選択します。

1. **[デバイス接続]** ページで、**ID スコープ**、**デバイス ID**、デバイスの**プライマリ キー**を *scratchpad.txt* ファイルに書き留めます。 これらの値は後で使用します。

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
