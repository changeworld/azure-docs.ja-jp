---
title: "Azure Cosmos DB のスループットのプロビジョニング | Microsoft Docs"
description: "Azure Cosmos DB コレクションのプロビジョニング済みスループットを設定する方法について説明します。"
services: cosmosdb
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5258ba0bc37442c983d91c5dd7435fd5fbefd56f
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---

# <a name="set-throughput-for-azure-cosmos-db-collections"></a>Azure Cosmos DB コレクションのスループットの設定

Azure Portal またはクライアントの SDK を使用して、Azure Cosmos DB コレクションのスループットを設定できます。 

次の表に、コレクションで使用できるスループットを示します。

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>単一パーティション コレクション</strong></p></td>
            <td valign="top"><p><strong>パーティション分割コレクション</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>最小スループット</p></td>
            <td valign="top"><p>400 要求ユニット/秒</p></td>
            <td valign="top"><p>2,500 要求ユニット/秒</p></td>
        </tr>
        <tr>
            <td valign="top"><p>最大スループット</p></td>
            <td valign="top"><p>10,000 要求ユニット/秒</p></td>
            <td valign="top"><p>無制限</p></td>
        </tr>
    </tbody>
</table>

> [!NOTE] 
> 分割コレクションのスループット値を 2,500 RU/秒～ 10,000 RU/秒に設定するには、一時的に Azure Portal を使用する必要があります。 SDK では、まだこの機能を使用できません。

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>Azure Portal を使用してスループットを設定するには

1. 新しいウィンドウで、[Azure Portal](https://portal.azure.com) を開きます。
2. 左側のバーで **[Azure Cosmos DB]** をクリックします。または、下部の **[その他のサービス]** をクリックし、**[データベース]** までスクロールして、**[Azure Cosmos DB]** をクリックします。
3. Azure Cosmos DB アカウントを選択します。
4. この新しいウィンドウで、次のスクリーン ショットに示すように **[コレクション]** の **[スケール]** をクリックします。
5. この新しいウィンドウで、ドロップダウンからコレクションを選択し、**[スループット]** の値を変更して、**[保存]** をクリックします。

    ![Azure Portal で、アカウントに移動して [スケール] をクリックすることによって、コレクションのスループットを変更する方法を示すスクリーン ショット](./media/documentdb-set-throughput/azure-documentdb-change-throughput-value.png)

<a id="set-throughput-sdk"></a>

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

## <a name="throughput-faq"></a>スループットについてよく寄せられる質問

**スループットを 400 RU/秒未満に設定することはできますか?**

Cosmos DB のシングル パーティション コレクションで使用できる最小スループットは 400 RU/秒です (パーティション分割コレクションの最小スループットは 2,500 RU/秒)。 要求ユニットは 100 RU/秒の間隔で設定されますが、スループットを 100 RU/秒または 400 RU/秒未満の値に設定することはできません。 コストを抑えて Cosmos DB の開発とテストを行いたい場合は、無料の [Azure Cosmos DB Emulator](documentdb-nosql-local-emulator.md) を利用して無償でローカルにデプロイすることができます。 

## <a name="next-steps"></a>次のステップ

Cosmos DB を使用したプロビジョニングと地球規模での使用の詳細については、[Cosmos DB でのパーティション分割とスケーリング](documentdb-partition-data.md)に関するページをご覧ください。

