---
title: Azure Event Grid でのシステム トピック
description: Azure Event Grid でのシステム トピックについて説明します。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: spelluru
ms.openlocfilehash: 67746ebd8a16eb02b8f02d238b0e3c0125989189
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/03/2020
ms.locfileid: "84308270"
---
# <a name="system-topics-in-azure-event-grid"></a>Azure Event Grid でのシステム トピック
Azure イベント ソース用のイベント サブスクリプションを初めて作成すると、Azure Event Grid サービスによってシステム トピックが作成されます。 現在、Event Grid では、2020 年 3 月 15 日より前に作成されたトピック ソースに対するシステム トピックは作成されません。 この日付以降に作成したすべてのトピック ソースについては、Event Grid によりシステム トピックが自動的に作成されます。 この記事では、Azure Event Grid での**システム トピック**について説明します。

> [!NOTE]
> 現在、Azure Government クラウドではこの機能は有効になりません。 

## <a name="overview"></a>概要
Azure ストレージ アカウントなどの Azure イベント ソースに対して初めてイベント サブスクリプションを作成すると、サブスクリプションのプロビジョニング プロセスによって、**Microsoft.EventGrid/systemTopics** 型の追加のリソースが作成されます。 Azure イベント ソースに対する最後のイベント サブスクリプションを削除すると、システム トピックが自動的に削除されます。

システム トピックは、カスタム トピックのシナリオ、つまり Event Grid トピックと Event Grid ドメインには適用されません。 

## <a name="name"></a>名前 
以前は、Azure のソースによって生成されるイベントのサブスクリプションを作成すると、Event Grid サービスによって、**ランダムに生成された名前**を持つシステム トピックが自動的に作成されていました。 今では、Azure portal でトピックの作成中にシステム トピックの名前を指定できるようになっています。 このシステム トピック リソースを使用して、メトリックと診断ログを検出できます。

## <a name="location"></a>場所
特定のリージョンまたは場所にある Azure イベント ソースの場合、システム トピックは Azure イベント ソースと同じ場所に作成されます。 たとえば、米国東部の Azure Blob Storage に対してイベント サブスクリプションを作成すると、米国東部にシステム トピックが作成されます。 Azure サブスクリプション、リソース グループ、Azure Maps などのグローバルな Azure イベント ソースの場合は、Event Grid によって**グローバル**な場所にシステム トピックが作成されます。 

## <a name="resource-group"></a>Resource group 
一般に、システム トピックは Azure イベント ソースと同じリソース グループに作成されます。 Azure サブスクリプションのスコープで作成されたイベント サブスクリプションの場合、システム トピックはリソース グループ **Default-EventGrid** の下に作成されます。 リソース グループが存在しない場合は、システム トピックが作成される前に、Azure Event Grid によってリソース グループが作成されます。 

ストレージ アカウントでリソース グループを削除しようとすると、影響を受けるリソースの一覧内のシステム トピックが表示されます。  

![リソース グループの削除](./media/system-topics/delete-resource-group.png)


## <a name="next-steps"></a>次のステップ
次の記事を参照してください: [システム トピックの作成、表示、管理](create-view-manage-system-topics.md)。