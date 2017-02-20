---
title: "Azure DocumentDB のスループットのプロビジョニング | Microsoft Docs"
description: "DocumentDB コレクションのプロビジョニング済みスループットを設定する方法について説明します。"
services: documentdb
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 9919f9430626b02635272599e7d1f3f09e0068cd
ms.openlocfilehash: ae83dc01113e72576a35aa57acbac3b71d87e9f8


---

# <a name="set-throughput-for-azure-documentdb-collections"></a>Azure DocumentDB コレクションのスループットの設定

Azure Portal またはクライアントの SDK を使用して、DocumentDB コレクションのスループットを設定できます。 

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>Azure Portal を使用してスループットを設定するには

1. 新しいウィンドウで、[Azure Portal](https://portal.azure.com) を開きます。
2. 左側のバーで **[NoSQL (DocumentDB)]** をクリックします。または、下部の **[その他のサービス]** をクリックし、**[データベース]** までスクロールしてから **[NoSQL (DocumentDB)]** をクリックします。
3. DocumentDB アカウントを選択します。
4. この新しいウィンドウで、次のスクリーン ショットに示すように **[コレクション]** の **[スケール]** をクリックします。
5. この新しいウィンドウで、ドロップダウンからコレクションを選択し、**[スループット]** の値を変更して、**[保存]** をクリックします。

    ![Azure Portal で、アカウントに移動して [スケール] をクリックすることによって、コレクションのスループットを変更する方法を示すスクリーン ショット](./media/documentdb-set-throughput/azure-documentdb-change-throughput-value.png)

## <a name="to-set-the-throughput-by-using-the-net-sdk"></a>.NET SDK を使用してスループットを設定するには

```C#
    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
        .Where(r => r.ResourceLink == collection.SelfLink)    
        .AsEnumerable()
        .SingleOrDefault();

    // Set the throughput to the new value, for example 12,000 request units per second
    offer = new OfferV2(offer, 12000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);
```

## <a name="next-steps"></a>次のステップ

DocumentDB を使用したプロビジョニングと地球規模での使用の詳細については、[DocumentDB でのパーティション分割とスケーリング](documentdb-partition-data.md)に関するページをご覧ください。


<!--HONumber=Feb17_HO2-->


