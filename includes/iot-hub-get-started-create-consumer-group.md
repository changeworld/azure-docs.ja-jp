---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 57a5de4c590ff98429aa1d4a0b96cebed6084a0f
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/23/2019
ms.locfileid: "66146430"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>IoT ハブへのコンシューマー グループの追加

コンシューマー グループは、Azure IoT Hub からデータをプルするアプリケーションによって使用されます。 このチュートリアルでは、IoT ハブからデータを読み取る新しい Azure サービスによって使用されるコンシューマー グループを作成します。

コンシューマー グループを IoT ハブに追加するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com/) で、IoT ハブを開きます。

2. 左側のウィンドウで **[組み込みのエンドポイント]** をクリックし、上部のウィンドウで **[イベント]** を選択します。次に、右側のウィンドウの **[コンシューマー グループ]** で名前を入力します。 **[既定の TTL]** の値を変更し、元の値に戻した後で、**[保存]** クリックします。

   ![IoT ハブのコンシューマー グループの作成](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
