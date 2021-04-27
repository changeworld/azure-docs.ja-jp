---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 04/16/2021
ms.author: vlvinogr
ms.openlocfilehash: 329ea156b296810395eb7b8e8310bed5ee0ee4c9
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2021
ms.locfileid: "107601913"
---
## <a name="append-other-apis"></a>その他の API の追加

次のように、各種サービスで公開される API を構成できます。
* OpenAPI の仕様
* SOAP API
* Azure App Service の API Apps 機能
* Azure Function App
* Azure Logic Apps
* Azure Service Fabric

次の手順に従って、既存の API に別の API を追加します。 

>[!NOTE] 
> 別の API をインポートすると、操作が現在の API に追加されます。

1. Azure portal で Azure API Management インスタンスに移動します。

    :::image type="content" source="./media/api-management-append-apis/service-page.png" alt-text="Azure API Management インスタンスに移動する":::

1. 左にあるメニューから **[API]** を選択します。

    :::image type="content" source="./media/api-management-append-apis/api-select.png" alt-text="[API] を選択する":::

1. 別の API を追加する API の隣の **[...]** をクリックします。
1. ドロップダウン メニューから **[インポート]** を選択します。

    :::image type="content" source="./media/api-management-append-apis/append-01.png" alt-text="[インポート] を選択する":::

1. API のインポート元のサービスを選択します。

    :::image type="content" source="./media/api-management-append-apis/select-to-import.png" alt-text="サービスを選択する":::