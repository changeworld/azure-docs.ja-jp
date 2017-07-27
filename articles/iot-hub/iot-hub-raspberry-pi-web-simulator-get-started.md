---
title: "シミュレートした Raspberry Pi から クラウドへ (Node.js) - Raspberry Pi Web シミュレーターの Azure IoT Hub への接続 | Microsoft Docs"
description: "Raspberry Pi Web シミュレーターを Azure IoT Hub に接続し、Raspberry Pi で Azure クラウドにデータを送信します。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "raspberry pi シミュレーター, azure iot raspberry pi, raspberry pi iot hub, raspberry pi でクラウドにデータを送信する raspberry pi からクラウドへ"
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/27/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: fcba6f37f4a71da78f0e8b1c83c7a60711bbed09
ms.contentlocale: ja-jp
ms.lasthandoff: 06/28/2017


---

# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Raspberry Pi オンライン シミュレーターの Azure IoT Hub への接続 (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

このチュートリアルでは、まず Raspberry Pi オンライン シミュレーターの操作の基礎について説明します。 次に、[Azure IoT Hub](iot-hub-what-is-iot-hub.md) を使って、シミュレーターをクラウドにシームレスに接続する方法について説明します。 

物理デバイスがある場合、「[Raspberry Pi の Azure IoT Hub への接続](iot-hub-raspberry-pi-kit-node-get-started.md)」に移動して作業を開始してください。 

## <a name="what-you-do"></a>作業内容

* Raspberry Pi オンライン シミュレーターの基礎を説明します。
* IoT Hub を作成します。
* Pi のデバイスを IoT Hub に登録します。
* Pi でサンプル アプリケーションを実行し、シミュレートしたセンサー データを IoT Hub に送信します。

作成した IoT Hub にシミュレートした Raspberry Pi を接続します。 次にシミュレーターを使用してサンプル アプリケーションを実行し、センサー データを生成します。 最後に、センサー データを IoT Hub に送信します。

## <a name="what-you-learn"></a>学習内容

* Azure IoT Hub を作成し、新しいデバイス接続文字列を取得する方法。 Azure アカウントがない場合は、[無料試用版の Azure アカウント](https://azure.microsoft.com/free/)を数分で作成できます。
* Raspberry Pi オンライン シミュレーターの操作方法。
* センサー データを IoT Hub に送信する方法。

## <a name="overview-of-raspberry-pi-web-simulator"></a>Raspberry Pi Web シミュレーターの概要

Raspberry Pi オンライン シミュレーターを起動するボタンをクリックします。

> [!div class="button"]
[Raspberry Pi シミュレーターの起動](https://azure-samples.github.io/raspberry-pi-web-simulator/)

Web シミュレーターには3 つの領域があります。
* アセンブリ領域 - 既定の回線は、Pi が BME280 センサーと LED に接続する回線です。 プレビュー バージョンではこの領域はロックされているため、今のところカスタマイズを行うことはできません。
* コーディング領域 - Raspberry Pi を使用してコーディングするためのオンライン コード エディター。 既定のサンプル アプリケーションは、BME280 センサーからセンサー データを収集し、、Azure IoT Hub に送信する際に役立ちます。 このアプリケーションは、実際の Pi デバイスとの完全に互換性があります。 
* 統合されたコンソール ウィンドウ - コードの出力が表示されます。 このウィンドウの上部には、3 つのボタンがあります。
   * **[実行]** - コーディング領域でアプリケーションを実行します。
   * **[リセット]** - コーディング領域を既定のサンプル アプリケーションにリセットします。
   * **[折りたたむ/展開する]** - 右側には、コンソール ウィンドウの折りたたみおよび展開を行うボタンがあります。

> [!NOTE] 
Raspberry Pi Web シミュレーターは、プレビュー バージョンで使用できるようになりました。 「[Gitter チャット ルーム](https://gitter.im/Microsoft/raspberry-pi-web-simulator)」にご意見をお寄せください。 ソース コードは [GitHub](https://github.com/Azure-Samples/raspberry-pi-web-simulator) から入手できます。

![Pi オンライン シミュレーターの概要](media/iot-hub-raspberry-pi-web-simulator/0_overview.png)

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]


## <a name="run-a-sample-application-on-pi-web-simulator"></a>Pi Web シミュレーターでのサンプル アプリケーションの実行

1. コーディング領域で、既定のサンプル アプリケーションで作業していることを確認します。 行 15 のプレースホルダーを Azure IoT Hub デバイスの接続文字列に置き換えます。
   ![デバイスの接続文字列を置き換える](media/iot-hub-raspberry-pi-web-simulator/1_connectionstring.png)

2. **[実行]** をクリックまたは `npm start` と入力してアプリケーションを実行します。


IoT Hub に送信されるセンサー データとメッセージを示す次の出力が表示されます。![出力 - Raspberry Pi から IoT Hub に送信されるセンサー データ](media/iot-hub-raspberry-pi-web-simulator/2_run_application.png)


## <a name="next-steps"></a>次のステップ

サンプル アプリケーションを実行してセンサー データを収集し、IoT Hub に送信します。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

