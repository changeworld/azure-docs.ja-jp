---
author: robinsh
ms.author: robinsh
ms.topic: include
ms.date: 07/07/2021
ms.openlocfilehash: 090a7ae8e053b82e157d1e46e740d9b3911fef99
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131077597"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>IoT Hub へのコンシューマー グループの追加

[コンシューマー グループ](../articles/event-hubs/event-hubs-features.md#event-consumers)は、イベント ストリームへの独立したビューを提供します。これにより、アプリと Azure サービスは、同じイベント ハブのエンドポイントからデータを別々に使用することができます。 このセクションでは、エンドポイントからデータを取得するためにこのチュートリアルの後半で使用される、IoT ハブの組み込みのエンドポイントにコンシューマー グループを追加します。

コンシューマー グループを IoT ハブに追加するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com/) で、IoT ハブを開きます。

1. 左側のペインで、 **[組み込みのエンドポイント]** を選択します。 **[コンシューマー グループ]** の下のテキスト ボックスに、新しいコンシューマー グループの名前を入力します。

   :::image type="content" source="./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png" alt-text="IoT ハブでコンシューマー グループを作成する方法を示すスクリーンショット。" border="true":::

1. テキスト ボックスの外側のどこかをクリックして、コンシューマー グループを保存します。