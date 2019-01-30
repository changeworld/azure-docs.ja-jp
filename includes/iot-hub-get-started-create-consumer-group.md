---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.author: dobett
ms.openlocfilehash: 2d041ccbcf85f1931ca8ebc8b17595c9221c03cc
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2019
ms.locfileid: "54414597"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>IoT ハブへのコンシューマー グループの追加

コンシューマー グループは、Azure IoT Hub からデータをプルするアプリケーションによって使用されます。 このチュートリアルでは、IoT ハブからデータを読み取る新しい Azure サービスによって使用されるコンシューマー グループを作成します。

コンシューマー グループを IoT ハブに追加するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com/) で、IoT ハブを開きます。

2. 左側のウィンドウで **[組み込みのエンドポイント]** をクリックし、上部のウィンドウで **[イベント]** を選択します。次に、右側のウィンドウの **[コンシューマー グループ]** で名前を入力します。 **[既定の TTL]** の値を変更し、元の値に戻した後で、**[保存]** クリックします。

   ![IoT ハブのコンシューマー グループの作成](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
