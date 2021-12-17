---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 04/16/2021
ms.author: vlvinogr
ms.openlocfilehash: 890cb1dc8d8a59f6b567497b968b53bb89ec1ecc
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2021
ms.locfileid: "108230724"
---
## <a name="append-other-apis"></a>その他の API の追加

次のように、各種サービスで公開される API を構成できます。
* OpenAPI の仕様
* SOAP API
* Azure App Service でホストされる Web アプリ
* Azure Function App
* Azure Logic Apps
* Azure Service Fabric

次の手順に従って、既存の API に別の API を追加します。 

>[!NOTE] 
> 別の API をインポートすると、操作が現在の API に追加されます。

1. Azure portal で Azure API Management インスタンスに移動します。

    :::image type="content" source="./media/api-management-append-apis/service-page.png" alt-text="Azure API Management インスタンスに移動する":::

1. **[概要]** ページまたは左側のメニューから **[API]** を選択します。

    :::image type="content" source="./media/api-management-append-apis/api-select.png" alt-text="[API] を選択する":::

1. 別の API を追加する API の隣の **[...]** をクリックします。
1. ドロップダウン メニューから **[インポート]** を選択します。

    :::image type="content" source="./media/api-management-append-apis/append-01.png" alt-text="[インポート] を選択する":::

1. API のインポート元のサービスを選択します。

    :::image type="content" source="./media/api-management-append-apis/select-to-import.png" alt-text="サービスを選択する":::
