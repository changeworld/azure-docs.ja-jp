---
title: Azure IoT Toolkit for VS Code を使用した Azure IoT ハブの作成 | Microsoft Docs
description: Azure IoT Toolkit for VS Code を使用して IoT ハブを作成する方法。
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: junhan
ms.openlocfilehash: 71b188443ee48d742b14753cd5526edac493d9e3
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2018
ms.locfileid: "39368620"
---
# <a name="create-an-iot-hub-using-the-azure-iot-toolkit-for-visual-studio-code"></a>Azure IoT Toolkit for Visual Studio Code を使用した IoT ハブの作成

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

[Azure IoT Toolkit for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) を使用して Azure IoT ハブを作成することができます。 この記事では、Azure IoT Toolkit を使用して IoT ハブを作成する方法を説明します。

この記事を完了するには、以下が必要です。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。
- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure IoT ツールキット](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)

## <a name="create-an-iot-hub"></a>IoT Hub の作成

1. Visual Studio Code で**エクスプローラー** ビューを開きます。

2. エクスプローラーの下部で、**[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを展開します。 

   ![Azure IoT Hub デバイスを展開する](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクション ヘッダーで **[...]** をクリックします。 省略記号が表示されない場合は、ヘッダーをポイントします。 

4. **[Create IoT Hub]\(IoT ハブを作成する\)** を選択します。

5. 右下隅にポップアップが表示され、Azure への初めてのサインインを行うことができます。

6. Azure サブスクリプションを選択します。 

7. リソース グループを選択します。

8. 場所を選択します。

9. 価格レベルを選択します。

10. IoT ハブのグローバルに一意の名前を入力します。

11. IoT ハブが作成されるまで数分待ちます。

## <a name="next-steps"></a>次の手順

ここでは、Azure IoT Toolkit for Visual Studio Code を使用して IoT ハブをデプロイしました。次の手順に進んでください。

* [Visual Studio Code 用 Azure IoT Toolkit 拡張機能を使用してデバイスと IoT Hub の間のメッセージを送受信する](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)。
* Azure IoT Toolkit の [Wiki ページ](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki)。
