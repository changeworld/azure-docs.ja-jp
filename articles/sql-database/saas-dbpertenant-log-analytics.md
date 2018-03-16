---
title: "SQL Database のマルチテナント アプリで Log Analytics を使用する | Microsoft Docs"
description: "マルチテナント Azure SQL Database SaaS アプリで Log Analytics (OMS) を設定して使用する"
keywords: "SQL データベース チュートリアル"
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: billgib; sstein
ms.openlocfilehash: 7747092d5613a40fa0aff09cfbdfb9b786b37954
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2018
---
# <a name="set-up-and-use-log-analytics-oms-with-a-multi-tenant-azure-sql-database-saas-app"></a>マルチテナント Azure SQL Database SaaS アプリで Log Analytics (OMS) を設定して使用する

このチュートリアルでは、エラスティック プールおよびデータベースを監視するために、*Log Analytics ([OMS](https://www.microsoft.com/cloud-platform/operations-management-suite))* を設定して使用します。 このチュートリアルは、[パフォーマンスの監視と管理のチュートリアル](saas-dbpertenant-performance-monitoring.md)を基礎とします。 *Log Analytics* を使用して、Azure Portal で提供された監視とアラート設定を拡張する方法を示します。 Log Analytics では、何千単位のエラスティック プールと何十万単位のデータベースの監視をサポートしています。 Log Analytics は単一監視ソリューションを提供し、複数の Azure サブスクリプションのさまざまなアプリケーションと Azure サービスの監視を統合することができます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Log Analytics (OMS) をインストールして構成する
> * Log Analytics を使用してプールとデータベースを監視する

このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。

* Wingtip Tickets SaaS Database Per Tenant アプリをデプロイします。 5 分未満でデプロイするには、「[Deploy and explore the Wingtip Tickets SaaS Multi-tenant Database application (Wingtip Tickets SaaS Database Per Tenant アプリケーションのデプロイと探索)](saas-dbpertenant-get-started-deploy.md)」を参照してください
* Azure PowerShell がインストールされている。 詳しくは、「[Azure PowerShell を使ってみる](https://docs.microsoft.com/powershell/azure/get-started-azureps)」をご覧ください。

SaaS のシナリオとパターン、および監視ソリューションの要件に対する影響については、[パフォーマンスの監視と管理を行うためのチュートリアル](saas-dbpertenant-performance-monitoring.md)を参照してください。

## <a name="monitoring-and-managing-database-and-elastic-pool-performance-with-log-analytics-or-operations-management-suite-oms"></a>Log Analytics または Operations Management Suite (OMS) によるデータベースとエラスティック プール パフォーマンスの監視および管理

SQL Database については、Azure Portal でデータベースとプールに対する監視とアラートを利用できます。 この組み込みの監視とアラートは便利ですが、リソース固有であり、多数のインストールの監視や、リソースとサブスクリプション間の統一されたビューの提供には適していません。

大規模なシナリオでは、監視とアラートに Log Analytics を使用できます。 Log Analytics は、潜在的に多くのサービスからワークスペースに集められた診断ログやテレメトリに対する分析を可能にする、別個の Azure サービスです。 Log Analytics には、オペレーション データを分析できるクエリ言語とデータ視覚化ツールが組み込まれています。 SQL Analytics ソリューションには、エラスティック プールとデータベースの監視とアラートを行うためのさまざまな定義済みのビューとクエリが用意されています。 OMS には、カスタム ビュー デザイナーも用意されています。

Log Analytics のワークスペースと分析ソリューションは、Azure ポータルと OMS の両方で開くことができます。 Azure ポータルは新しいアクセス ポイントですが、一部の領域では OMS ポータルの背後に配置される場合があります。

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>テナントでワークロードをシミュレートしてパフォーマンスの診断データを作成する 

1. **PowerShell ISE** で、*..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\**Demo-PerformanceMonitoringAndManagement.ps1*** を開きます。 このスクリプトは、このチュートリアル中にいくつかのロード生成シナリオで実行するため、開いたままにしておいてください。
1. まだ実行していない場合は、より興味深い監視コンテキストを提供するために、テナントのバッチをプロビジョニングします。 これには、数分かかります。
   1. **$DemoScenario = 1** _(テナントのバッチのプロビジョニング)_ を設定します。
   1. スクリプトを実行し、追加の 17 テナントをデプロイして、**f5** キーを押します。  

1. ロード ジェネレーターを開始して、すべてのテナントで疑似ロードを実行します。  
    1. **$DemoScenario = 2** _(標準強度負荷の生成 (およそ 30 DTU))_ を設定します。
    1. **F5** キーを押して、スクリプトを実行します。

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Wingtip Tickets SaaS Database Per Tenant アプリケーション スクリプトを入手する

Wingtip Tickets SaaS マルチテナント データベースのスクリプトとアプリケーション ソース コードは、[WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub リポジトリで入手できます。 Wingtip Tickets PowerShell スクリプトをダウンロードし、ブロックを解除する手順については、[一般的なガイダンス](saas-tenancy-wingtip-app-guidance-tips.md)に関する記事をご覧ください。

## <a name="installing-and-configuring-log-analytics-and-the-azure-sql-analytics-solution"></a>Log Analytics と Azure SQL Analytics ソリューションをインストールして構成する

Log Analytics は構成する必要がある独立したサービスです。 Log Analytics は、ログ データ、テレメトリ、およびメトリックをログ分析ワークスペース内に収集します。 Azure の他のリソースと同じように、Log Analytics ワークスペースはリソースであるため、作成する必要があります。 ワークスペースは監視対象のアプリケーションと同じリソース グループ内に作成する必要はありませんが、多くの場合は、そうすることが最も道理にかなっています。 Wingtip Tickets アプリの場合、単一のリソース グル―プを使用することで、アプリケーションによってワークスペースが削除されることが保証されます。

1. **PowerShell ISE** で、*..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\Log Analytics\\**Demo-LogAnalytics.ps1*** を開きます。
1. **F5** キーを押して、スクリプトを実行します。

この時点で、Azure ポータル (または、OMS ポータル) で Log Analytics を開くことができます。 Log Analytics ワークスペース内にテレメトリが収集されて視覚化されるまで、数分かかります。 システムが診断データを収集する時間を長くすればするほど、エクスペリエンスは興味深いものになります。 このタイミングで、ロード ジェネレーターが実行中であることを確認してから少し休憩することをお勧めします。

## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Log Analytics と SQL Analytics ソリューションを使用してプールとデータベースを監視する


この演習では、Log Analytics と OMS ポータルを開き、データベースとプールで収集されるテレメトリを調べます。

1. [Azure Portal](https://portal.azure.com) に移動し、**[その他のサービス]** をクリックして Log Analytics を検索することで、Log Analytics を開きます。

   ![Log Analytics を開く](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. _wtploganalytics-&lt;user&gt;_ という名前のワークスペースを選択します。

1. **[概要]** を選択して、Azure ポータルで Log Analytics ソリューションを選択します。

   ![overview-link](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > ソリューションがアクティブになるまで数分かかる場合があります。 しばらくお待ちください。

1. [Azure SQL Analytics] タイルをクリックして開きます。

    ![概要](media/saas-dbpertenant-log-analytics/overview.png)

    ![分析](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. ソリューションのビューは横方向にスクロールし、固有の内部スクロール バーが下部に表示されます (必要に応じてブレードを更新してください)。

1. タイルまたは個別のデータベースをクリックしてドリルダウン エクスプローラーを開き、概要ページを確認します。

1. フィルター設定を切り替えて、時間の範囲を変更します。このチュートリアルでは、 _[過去 1 時間]_ を選択します。

    ![時間フィルター](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. クエリの使用状況と該当のデータベースのメトリックを調査する 1 つのデータベースを選択します。

    ![データベースの分析](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. 使用状況のメトリックを表示するには、分析ページを右にスクロールします。
 
     ![データベースのメトリック](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. 分析ページを左にスクロールして、[リソース情報] 一覧のサーバー タイルをクリックします。 これにより、サーバー上のプールとデータベースを表示するページが開かれます。 

     ![リソース情報](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

 
     ![プールとデータベースを備えたサーバー](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. サーバー上のプールとデータベースを表示する開かれたサーバー ページで、プールをクリックします。  開かれたプール ページで、右にスクロールしてプールのメトリックを表示します。  

     ![プールのメトリック](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)



1. Log Analytics ワークスペースに戻り、**[OMS ポータル]** を選択して、このポータルでワークスペースを開きます。

    ![OMS](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

OMS ポータルでは、ワークスペース内のログとメトリック データをさらに詳細に調査できます。  

Log Analytics と OMS での監視とアラートは、Azure ポータルの各リソースで定義されているアラートとは異なり、ワークスペース内のデータに対するクエリに基づいています。 アラートがクエリに基づくようにすることで、すべてのデータベースを対象とする単一のアラートを定義でき、データベース別に 1 つずつアラートを定義する必要はありません。 クエリは、ワークスペースで利用可能なデータによってのみ制限されます。

OMS を使用してクエリを実行し、アラートを設定する方法の詳細については、「[Log Analytics のアラート ルールの操作](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating)」をご覧ください。

Log Analytics for SQL Database は、ワークスペース内のデータ量に基づいて課金されます。 このチュートリアルでは、Free ワークスペースを作成しました。このワークスペースの制限は、1 日あたり 500 MB です。 この制限に達すると、データはワークスペースに追加されなくなります。


## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Log Analytics (OMS) をインストールして構成する
> * Log Analytics を使用してプールとデータベースを監視する

[テナント分析のチュートリアル](saas-dbpertenant-log-analytics.md)

## <a name="additional-resources"></a>その他のリソース

* [Wingtip Tickets SaaS Database Per Tenant アプリケーションの初期のデプロイに基づく作業のための追加のチュートリアル](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)
* [OMS](https://blogs.technet.microsoft.com/msoms/2017/02/21/azure-sql-analytics-solution-public-preview/)
