---
title: クイック スタート:Azure RTOS 用のセキュリティ モジュールを構成して有効にする
description: Azure IoT Hub で Azure RTOS サービス用のセキュリティ モジュールをオンボードし、有効にする方法について説明します。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: 116361977177b708265232a917e7f31e5f8cd09a
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/08/2020
ms.locfileid: "89515135"
---
# <a name="quickstart-security-module-for-azure-rtos-preview"></a>クイック スタート:Azure RTOS 用のセキュリティ モジュール (プレビュー)

この記事では、使い始める前の前提条件について説明し、IoT Hub で Azure RTOS サービス用のセキュリティ モジュールを有効にする方法について説明します。 現在 IoT Hub がない場合、作業を開始するには、「[Azure portal を使用して IoT Hub を作成する](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal)」を参照してください。

> [!NOTE]
> Azure RTOS 用のセキュリティ モジュールは、Standard レベルの IoT Hub でのみサポートされています。

## <a name="prerequisites"></a>前提条件 

### <a name="supported-devices"></a>サポートされるデバイス

- ST STM32F746G Discovery Kit
- NXP i.MX RT1060 EVK
- Microchip SAM E54 Xplained Pro EVK

[Azure RTOS 用のセキュリティ モジュール GitHub リソース](hhtps://github.com/azure-rtos/azure-iot-preview/releases)で選択した特定のボードとツール (IAR、セミの IDE、または PC) の .zip ファイルの 1 つをダウンロードしてコンパイルし、実行します。

### <a name="azure-resources"></a>Azure リソース

作業を開始するための次のステージでは、Azure リソースを準備します。 IoT Hub が必要であり、Log Analytics ワークスペースを使用することをお勧めします。 IoT Hub を使用するには、デバイスに接続するための IoT Hub 接続文字列が必要です。 
  
### <a name="iot-hub-connection"></a>IoT Hub 接続

作業を始めるには、IoT Hub 接続が必要です。 

1. Azure portal で **[IoT Hub]** を開きます。
1. IoT 接続文字列を[構成ファイル](how-to-azure-rtos-security-module.md)にコピーします。


接続の資格情報は、ユーザー アプリケーション構成の **HOST_NAME**、**DEVICE_ID**、**DEVICE_SYMMETRIC_KEY** から取得します。

Azure RTOS 用のセキュリティ モジュールでは、**MQTT** プロトコルに基づく Azure IoT ミドルウェア接続が使用されます。


### <a name="log-analytics-workspace"></a>Log Analytics ワークスペース

IoT Hub での Log Analytics インジェストは、既定の Azure Security Center for IoT ソリューションによってオフになっています。 それを Azure RTOS 用のセキュリティ モジュールで動作させるには、次の手順のようにします。 
1. Azure portal で、IoT Hub に移動します。
1. **[セキュリティ]** メニューの **[設定]** を選択します。
   :::image type="content" source="media/quickstart/azure-rtos-hub-settings.png" alt-text="Azure RTOS のデータ収集オプションにアクセスする"::: 
1. **[データ収集]** を選択します。 
1. **[ワークスペースの構成]** オプションで、 **[オン]** に切り替えます。 
1. 新しい Log Analytics ワークスペースを作成するか、既存のものをアタッチします。 **[Access to raw security data]\(生セキュリティ データにアクセスする\)** オプションが選択されていることを確認します。 
 :::image type="content" source="media/quickstart/azure-rtos-data-collection-on.png" alt-text="データ収集オプションと生セキュリティ データ オプションの両方が選択されている Azure RTOS の構成":::
1. **[保存]** を選びます。
1. Azure リソースの一覧に戻り、作成またはアタッチした Log Analytics ワークスペースが IoT Hub に対して有効になっていることを確認します。
    :::image type="content" source="media/quickstart/verify-azure-resource-list.png" alt-text="Azure リソースの一覧で、IoT Hub に正しい Log Analytics ワークスペースが追加されていることを確認する"::: 

## <a name="next-steps"></a>次のステップ

次の記事に進んで、ソリューションの構成とカスタマイズを完了します。

> [!div class="nextstepaction"]
> [Azure RTOS 用のセキュリティ モジュールを構成する](how-to-azure-rtos-security-module.md)
