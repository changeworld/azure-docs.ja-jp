---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: c164433efc6a34a3a06676a3145feb18d3de80b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "66249061"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>IoT ハブへのコンシューマー グループの追加

[コンシューマー グループ](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers)は、イベント ストリームへの独立したビューを提供します。これにより、アプリと Azure サービスは、同じイベント ハブのエンドポイントからデータを別々に使用することができます。 このセクションでは、エンドポイントからデータを取得するためにこのチュートリアルの後半で使用される、IoT ハブの組み込みのエンドポイントにコンシューマー グループを追加します。

コンシューマー グループを IoT ハブに追加するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com/) で、IoT ハブを開きます。

2. 左側のウィンドウで **[組み込みのエンドポイント]** を選択し、右側のウィンドウで **[イベント]** を選択して、 **[コンシューマー グループ]** の下に名前を入力します。 **[保存]** を選択します。

   ![IoT ハブのコンシューマー グループの作成](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
