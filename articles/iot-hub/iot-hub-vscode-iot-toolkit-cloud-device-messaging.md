---
title: Visual Studio Code 用 Azure IoT Toolkit 拡張機能を使用して Azure IoT Hub クラウド デバイス メッセージングを管理する | Microsoft Docs
description: Visual Studio Code 用 Azure IoT Toolkit 拡張機能を使用して、Azure IoT Hub でデバイスからクラウドへのメッセージを監視し、クラウドからデバイスへのメッセージを送信する方法について説明します。
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 7/20/2018
ms.author: junhan
ms.openlocfilehash: 7bcb6eebdb6ceba6b07aeb19c1a74309fd4227d0
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39206689"
---
# <a name="use-azure-iot-toolkit-extension-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Visual Studio Code 用 Azure IoT Toolkit 拡張機能を使用してデバイスと IoT Hub の間のメッセージを送受信する

![エンド ツー エンド ダイアグラム](media/iot-hub-get-started-e2e-diagram/2.png)

[Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) は、IoT Hub の管理を容易にする便利な Visual Studio Code 拡張機能です。 この記事では、Visual Studio Code 用 Azure IoT Toolkit 拡張機能を使用してデバイスと IoT Hub の間のメッセージを送受信する方法を説明します。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>学習内容

Visual Studio Code 用 Azure IoT Toolkit 拡張機能を使用して、デバイスからクラウドへのメッセージを監視し、クラウドからデバイスへのメッセージを送信する方法について説明します。 D2C メッセージは、デバイスが収集し、IoT Hub に送信するセンサー データである可能性があります。 C2D メッセージは、IoT Hub がデバイスに送信するコマンドである可能性があります。このコマンドによって、そのデバイスに接続されている LED が点滅します。

## <a name="what-you-will-do"></a>学習内容

- Visual Studio Code 用 Azure IoT Toolkit 拡張機能を使用して、デバイスからクラウドへのメッセージを監視します。
- Visual Studio Code 用 Azure IoT Toolkit 拡張機能を使用して、クラウドからデバイスへのメッセージを送信します。

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
   
## <a name="monitor-device-to-cloud-messages"></a>D2C メッセージの監視

デバイスから IoT Hub に送信されたメッセージを監視するには、次の手順に従います。

1. デバイスを右クリックして、**[Start Monitoring D2C Message]\(D2C メッセージの監視を開始する\)** を選択します。
1. 監視対象のメッセージが、**[OUTPUT]\(出力\)** > **[Azure IoT Toolkit]\(Azure IoT Toolkit\)** ビューに表示されます。
1. 監視を停止するには、**[OUTPUT]\(出力\)** ビューを右クリックして、**[Stop Monitoring D2C Message]\(D2C メッセージの監視を停止する\)** を選択します。

## <a name="send-cloud-to-device-messages"></a>C2D メッセージの送信

IoT Hub からデバイスにメッセージを送信するには、次の手順に従います。

1. デバイスを右クリックして、**[Send C2D Message to Device]\(C2D メッセージをデバイスに送信する\)** を選択します。 
1. 入力ボックスにメッセージを入力します。
1. 結果が、**[OUTPUT]\(出力\)** > **[Azure IoT Toolkit]\(Azure IoT Toolkit\)** ビューに表示されます。

## <a name="next-steps"></a>次の手順

使用している IoT デバイスと Azure IoT Hub の間で D2C メッセージを監視し、C2D メッセージを送信する方法については学習しました。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
