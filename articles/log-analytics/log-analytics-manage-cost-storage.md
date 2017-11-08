---
title: "Azure Log Analytics でのデータ保持のコストの管理 | Microsoft Docs"
description: "Azure Portal で Log Analytics ワークスペースの料金プランとデータ保持ポリシーを変更する方法について説明します。"
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 35064c792b72222d59b1d3f0913a92a4a2b34612
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/01/2017
---
# <a name="manage-cost-of-data-retention-with-your-log-analytics-workspace"></a>Log Analytics ワークスペースによるデータ保持のコストの管理
Log Analytics にサインアップすると、選択したプランに応じて、接続されたソースが生成したデータをワークスペースに格納する期間が制限されます。  この記事では、このデータをさまざまな期間に保持するためのコストに影響を与える事項とその制限を構成する方法について取り上げます。   

Log Analytics はオンプレミスのソース、クラウド環境、およびハイブリッド環境から大量のデータを消費するため、一定の期間そのデータを格納するには、以下に示す要因に応じてかなりのコストが必要になります。

* 収集元のシステム、インフラストラクチャのコンポーネント、クラウド リソースなどの数
* メッセージ キュー、ログ、イベント、セキュリティ関連データ、パフォーマンス メトリックなど、ソースによって作成されるデータの種類
* これらのソースによって生成されるデータのボリューム 
* 有効な管理ソリューションの数、データ ソース、および収集の頻度

> [!NOTE]
> 各ソリューションが収集するデータ量を見積もる際には、各ソリューションのドキュメントをご覧ください。   

*無料*プランをご利用の場合は、データ保持期間が 7 日間に制限されます。  *スタンドアロン*または*有料*プランの場合は、収集したデータを 31 日間ご利用いただけます。  

*無料*プランをご利用の際、許可されている量を常に超えることが確実な場合は、ワークスペースを有料プランに変更し、この制限を超えてデータを収集できます。 

> [!NOTE]
> 有料プランで長い保持期間を選択する場合は、料金がかかります。 プランの種類はいつでも変更できます。価格について詳しくは、[価格の詳細](https://azure.microsoft.com/pricing/details/log-analytics/)に関するページをご覧ください。 

## <a name="change-the-data-retention-period"></a>データ保持期間の変更 

1. [Azure Portal](http://portal.azure.com) にサインインします。 
2. 左下隅にある **[その他のサービス]** をクリックします。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics]** を選択します。
3. Log Analytics のサブスクリプション ウィンドウで、変更するワークスペースを一覧から選択します。
4. [ワークスペース] ページで、左側のウィンドウの **[リテンション期間]** をクリックします。
5. ワークスペースの保持期間のウィンドウで、スライダーを移動して日数を増減し、**[保存]** をクリックします。  *無料*プランをご利用の場合は、データ保持期間を変更できません。この設定を制御するには、有料プランにアップグレードする必要があります。<br><br> ![ワークスペースのデータ保持の設定の変更](media/log-analytics-manage-cost/manage-cost-change-retention.png)

## <a name="next-steps"></a>次のステップ  

使用量とコストの管理に役立つデータの収集量、データを送信するソース、および送信されるデータの種類を指定するには、「[Log Analytics でのデータ使用状況の分析](log-analytics-usage.md)」をご覧ください。