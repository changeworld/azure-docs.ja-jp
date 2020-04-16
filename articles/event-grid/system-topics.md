---
title: Azure Event Grid でのシステム トピック
description: Azure Event Grid でのシステム トピックについて説明します。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: spelluru
ms.openlocfilehash: 46bceeb31fa38068c6c4f9f3a86ed556ad39effb
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393163"
---
# <a name="system-topics-in-azure-event-grid"></a>Azure Event Grid でのシステム トピック
Azure イベント ソース用のイベント サブスクリプションを初めて作成すると、Azure Event Grid サービスによってシステム トピックが作成されます。 現在、Event Grid では、2020 年 3 月 15 日より前に作成されたトピック ソースに対するシステム トピックは作成されません。 この日付以降に作成したすべてのトピック ソースについては、Event Grid によりシステム トピックが自動的に作成されます。 この記事では、Azure Event Grid での**システム トピック**について説明します。

> [!NOTE]
> 現在、Azure Government クラウドではこの機能は有効になりません。 

## <a name="overview"></a>概要
Azure ストレージ アカウントなどの Azure イベント ソースに対して初めてイベント サブスクリプションを作成すると、サブスクリプションのプロビジョニング プロセスによって、**Microsoft.EventGrid/systemTopics** 型の追加のリソースが作成されます。 Azure イベント ソースに対する最後のイベント サブスクリプションを削除すると、システム トピックが自動的に削除されます。

システム トピックは、カスタム トピックのシナリオ、つまり Event Grid トピックと Event Grid ドメインには適用されません。 

## <a name="location"></a>場所
特定のリージョンまたは場所にある Azure イベント ソースの場合、システム トピックは Azure イベント ソースと同じ場所に作成されます。 たとえば、米国東部の Azure Blob Storage に対してイベント サブスクリプションを作成すると、米国東部にシステム トピックが作成されます。 Azure サブスクリプション、リソース グループ、Azure Maps などのグローバルな Azure イベント ソースの場合は、Event Grid によって**グローバル**な場所にシステム トピックが作成されます。 

## <a name="resource-group"></a>Resource group 
一般に、システム トピックは Azure イベント ソースと同じリソース グループに作成されます。 Azure サブスクリプションのスコープで作成されたイベント サブスクリプションの場合、システム トピックはリソース グループ **Default-EventGrid** の下に作成されます。 リソース グループが存在しない場合は、システム トピックが作成される前に、Azure Event Grid によってリソース グループが作成されます。 

ストレージ アカウントでリソース グループを削除しようとすると、影響を受けるリソースの一覧内のシステム トピックが表示されます。  

![リソース グループの削除](./media/system-topics/delete-resource-group.png)

## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。 

- [カスタム トピック](custom-topics.md)
- [ドメイン](event-domains.md)