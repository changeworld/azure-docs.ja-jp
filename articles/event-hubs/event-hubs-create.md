---
title: Azure イベント ハブを作成する | Microsoft Docs
description: Azure Portal を使用して Azure Event Hubs 名前空間とイベント ハブを作成します
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: 5b468e1758d752cd3001c85b328d064369429499
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2018
ms.locfileid: "42146256"
---
# <a name="create-an-event-hubs-namespace-and-an-event-hub-using-the-azure-portal"></a>Azure Portal を使用して Event Hubs 名前空間とイベント ハブを作成する

## <a name="create-an-event-hubs-namespace"></a>Event Hubs 名前空間を作成します

1. [Azure Portal][Azure portal] にログインし、画面の左上にある **[リソースの作成]** をクリックします。
2. **[モノのインターネット]**、**[Event Hubs]** の順にクリックします。
   
    ![](./media/event-hubs-create/create-event-hub9.png)

3. **[名前空間の作成]** で、名前空間の名前を入力します。 その名前が使用できるかどうかがすぐに自動で確認されます。  

4. 入力した名前空間の名前が使用できることを確認したら、価格レベル (Basic または Standard) を選択します。 Azure サブスクリプション、リソース グループ、リソースが作成される場所を選択します。
 
5. **[作成]** をクリックして、名前空間を作成します。 システムがリソースを完全にプロビジョニングするまで、数分間待つことが必要な場合があります。

    ![](./media/event-hubs-create/create-event-hub1.png)

6. 名前空間のポータルの一覧で、新しく作成した名前空間をクリックします。

7. **[共有アクセス ポリシー]** をクリックし、**[RootManageSharedAccessKey]** をクリックします。
    
    ![](./media/event-hubs-create/create-event-hub7.png)

8. コピー ボタンをクリックして **RootManageSharedAccessKey** 接続文字列をクリップボードにコピーします。 後で使用するため、メモ帳などの一時的な場所に、この接続文字列を保存します。
    
    ![](./media/event-hubs-create/create-event-hub8.png)

## <a name="create-an-event-hub"></a>イベント ハブの作成

1. Event Hubs 名前空間の一覧で、新しく作成された名前空間をクリックします。      
   
    ![](./media/event-hubs-create/create-event-hub2.png) 

2. 名前空間ブレードで **[Event Hubs]** をクリックします。
   
    ![](./media/event-hubs-create/create-event-hub3.png)

3. ブレードの上部で、**+ Event Hub**をクリックします。
   
    ![](./media/event-hubs-create/create-event-hub4.png)
4. イベント ハブの名前を入力し、 **[作成]** をクリックします。 

Event Hubs が作成され、イベントの送受信に必要な接続文字列が手元にあります。

## <a name="next-steps"></a>次の手順

Event Hubs の詳細については、次のリンク先を参照してください。

* [Event Hubs の概要](event-hubs-what-is-event-hubs.md)
* [Event Hubs API 概要](event-hubs-api-overview.md)

[Azure portal]: https://portal.azure.com/