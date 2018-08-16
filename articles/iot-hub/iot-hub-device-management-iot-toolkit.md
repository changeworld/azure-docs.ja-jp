---
title: Visual Studio Code 用 Azure IoT Toolkit 拡張機能を使用した Azure IoT デバイスの管理 | Microsoft Docs
description: Azure IoT Hub デバイスの管理に Visual Studio Code 用 Azure IoT Toolkit 拡張機能を使用します。この拡張機能では、ダイレクト メソッドとデバイス ツインの必要なプロパティを管理するためのオプションを使用できます。
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 8/3/2018
ms.author: junhan
ms.openlocfilehash: ef99f4be97f5168add44d373a7e74de62347d110
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39450272"
---
# <a name="use-azure-iot-toolkit-extension-for-visual-studio-code-for-azure-iot-hub-device-management"></a>Azure IoT Hub デバイスの管理に Visual Studio Code 用 Azure IoT Toolkit 拡張機能を使用する

![エンド ツー エンド ダイアグラム](media/iot-hub-get-started-e2e-diagram/2.png)

[Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) は、IoT Hub の管理を容易にする便利な Visual Studio Code 拡張機能です。 さまざまなタスクを実行するために使用できる管理オプションが付属しています。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| 管理オプション          | タスク                                                                                                                            |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| ダイレクト メソッド             | メッセージの送信開始や停止、デバイスの再起動などの機能をデバイスに実行させます。                                        |
| デバイス ツインの読み取り           | デバイスの報告される状態を取得します。 たとえば、デバイスは、現在 LED が点滅していることを報告します。                                    |
| デバイス ツインの更新         | デバイスを特定の状態にします (LED を緑に設定したり、テレメトリの送信間隔を 30 分に設定したりします)。         |
| クラウドからデバイスへのメッセージ   | デバイスに通知を送信します。 例: "今日はほぼ雨が降ります。 必ず傘を持っていってください。"              |

これらのオプションの相違点の詳細な説明と使用するためのガイダンスについては、「[device-to-cloud 通信に関するガイダンス](iot-hub-devguide-d2c-guidance.md)」と「[cloud-to-device 通信に関するガイダンス](iot-hub-devguide-c2d-guidance.md)」を参照してください。

デバイス ツインは、デバイスの状態に関する情報 (メタデータ、構成、状態) を格納する JSON ドキュメントです。 IoT Hub は、IoT Hub に接続する各デバイスにデバイス ツインを保持します。 デバイス ツインの詳細については、「[デバイス ツインの使用](iot-hub-node-node-twin-getstarted.md)」を参照してください。

## <a name="what-you-learn"></a>学習内容

さまざまな管理オプションを指定して、開発マシンに対して Visual Studio Code 用 Azure IoT Toolkit 拡張機能を使用する方法について学びます。

## <a name="what-you-do"></a>作業内容

さまざまな管理オプションを使用して、Visual Studio Code 用 Azure IoT Toolkit 拡張機能を実行します。

## <a name="what-you-need"></a>必要なもの

- 有効な Azure サブスクリプション
- サブスクリプションの Azure IoT Hub。
- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure IoT ツールキット](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)

## <a name="sign-in-to-access-your-iot-hub"></a>サインインして IoT ハブにアクセスする

1. VS Code の**エクスプローラー** ビューで、左下隅の **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを展開します。
1. コンテキスト メニューで **[Select IoT Hub]\(IoT Hub の選択\)** をクリックします。
1. 右下隅にポップアップが表示され、Azure への初めてのサインインを行うことができます。
1. サインインすると、Azure サブスクリプションの一覧が表示されるので、Azure サブスクリプションと IoT Hub を選択します。
1. 数秒で **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** タブにデバイスの一覧が表示されます。

   > [!Note]
   > **[Set IoT Hub Connection String]\(IoT Hub 接続文字列の設定\)** を選択して設定することもできます。 ポップアップ ウィンドウに、IoT デバイスの接続先 IoT ハブの接続文字列を入力します。

## <a name="direct-methods"></a>ダイレクト メソッド

1. デバイスを右クリックし、**[ダイレクト メソッドの呼び出し]** を選択します。 
1. 入力ボックスにメソッド名とペイロードを入力します。
1. 結果が、**[OUTPUT]\(出力\)** > **[Azure IoT Toolkit]\(Azure IoT Toolkit\)** ビューに表示されます。

## <a name="read-device-twin"></a>デバイス ツインの読み取り

1. デバイスを右クリックし、**[デバイス ツインの編集]** を選択します。 
1. **azure-iot-device-twin.json** ファイルが開き、デバイス ツインの内容が表示されます。

## <a name="update-device-twin"></a>デバイス ツインの更新

1. **tags** フィールドまたは **properties.desired** フィールドを編集します。
1. **azure-iot-device-twin.json** ファイルを右クリックします。
1. **[デバイス ツインの更新]** を選択してデバイス ツインを更新します。

## <a name="send-cloud-to-device-messages"></a>C2D メッセージの送信

IoT Hub からデバイスにメッセージを送信するには、次の手順に従います。
 
1. デバイスを右クリックして、**[Send C2D Message to Device]\(C2D メッセージをデバイスに送信する\)** を選択します。 
1. 入力ボックスにメッセージを入力します。
1. 結果が、**[OUTPUT]\(出力\)** > **[Azure IoT Toolkit]\(Azure IoT Toolkit\)** ビューに表示されます。

## <a name="next-steps"></a>次の手順

ここでは、さまざまな管理オプションを使用して、Visual Studio Code 用 Azure IoT Toolkit 拡張機能を実行する方法について学びました。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
