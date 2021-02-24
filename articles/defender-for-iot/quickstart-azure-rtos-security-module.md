---
title: クイック スタート:Azure RTOS 用のセキュリティ モジュールを構成して有効にする
description: Azure IoT Hub で Azure RTOS サービス用のセキュリティ モジュールをオンボードし、有効にする方法について説明します。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: shhazam
ms.openlocfilehash: a11a8ec2d8eb82950cf0ab55eb6ca1913b41c84a
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522939"
---
# <a name="quickstart-security-module-for-azure-rtos-preview"></a>クイック スタート:Azure RTOS 用のセキュリティ モジュール (プレビュー)

この記事では、使い始める前の前提条件について説明し、IoT Hub で Azure RTOS サービス用のセキュリティ モジュールを有効にする方法について説明します。 現在 IoT Hub がない場合、作業を開始するには、「[Azure portal を使用して IoT Hub を作成する](../iot-hub/iot-hub-create-through-portal.md)」を参照してください。

## <a name="prerequisites"></a>前提条件 

### <a name="supported-devices"></a>サポートされるデバイス

- ST STM32F746G Discovery Kit
- NXP i.MX RT1060 EVK
- Microchip SAM E54 Xplained Pro EVK

[Azure RTOS 用のセキュリティ モジュール GitHub リソース](https://github.com/azure-rtos/azure-iot-preview/releases)で選択した特定のボードとツール (IAR、セミの IDE、または PC) の .zip ファイルの 1 つをダウンロードしてコンパイルし、実行します。

### <a name="azure-resources"></a>Azure リソース

作業を開始するための次のステージでは、Azure リソースを準備します。 IoT Hub が必要であり、Log Analytics ワークスペースを使用することをお勧めします。 IoT Hub を使用するには、デバイスに接続するための IoT Hub 接続文字列が必要です。 
  
### <a name="iot-hub-connection"></a>IoT Hub 接続

作業を始めるには、IoT Hub 接続が必要です。 

1. Azure portal で **[IoT Hub]** を開きます。

1. **[IoT デバイス]** に移動します。

1. **［作成］** を選択します

1. IoT 接続文字列を[構成ファイル](how-to-azure-rtos-security-module.md)にコピーします。

接続の資格情報は、ユーザー アプリケーション構成の **HOST_NAME**、**DEVICE_ID**、および **DEVICE_SYMMETRIC_KEY** から取得します。

Azure RTOS 用のセキュリティ モジュールでは、**MQTT** プロトコルに基づく Azure IoT ミドルウェア接続が使用されます。

## <a name="next-steps"></a>次のステップ

次の記事に進んで、ソリューションの構成とカスタマイズを完了します。

> [!div class="nextstepaction"]
> [Azure RTOS 用のセキュリティ モジュールを構成する](how-to-azure-rtos-security-module.md)
