---
title: SQL Database のマルチテナント アプリで Log Analytics を使用する | Microsoft Docs
description: マルチテナントの Azure SQL Database SaaS アプリで Log Analytics を設定して使用する
keywords: SQL データベース チュートリアル
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.reviewer: billgib
ms.openlocfilehash: 3ca2f811ff0ac81ea70ec0b22d7429cdc5604171
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39420184"
---
# <a name="set-up-and-use-log-analytics-with-a-multitenant-sql-database-saas-app"></a>マルチテナントの SQL Database SaaS アプリで Log Analytics を設定して使用する

このチュートリアルでは、エラスティック プールおよびデータベースを監視するために、[Azure Log Analytics](/azure/log-analytics/log-analytics-overview) を設定して使用します。 このチュートリアルは、[パフォーマンスの監視と管理のチュートリアル](saas-dbpertenant-performance-monitoring.md)を基礎とします。 Log Analytics を使用して、Azure Portal で提供された監視とアラート設定を拡張する方法を示します。 Log Analytics では、何千単位のエラスティック プールと何十万単位のデータベースの監視をサポートしています。 Log Analytics は単一の監視ソリューションを提供し、複数の Azure サブスクリプションのさまざまなアプリケーションと Azure サービスの監視を統合することができます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Log Analytics をインストールして構成します。
> * Log Analytics を使用してプールとデータベースを監視する。

このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。

* Wingtip Tickets SaaS テナント単位データベース アプリをデプロイします。 5 分未満でデプロイするには、[Wingtip Tickets SaaS テナント単位データベース アプリケーションのデプロイと探索](saas-dbpertenant-get-started-deploy.md)に関するページを参照してください。
* Azure PowerShell がインストールされている。 詳細については、[Azure PowerShell の概要](https://docs.microsoft.com/powershell/azure/get-started-azureps)に関するページを参照してください。

SaaS のシナリオとパターン、および監視ソリューションの要件に対する影響については、[パフォーマンスの監視と管理のチュートリアル](saas-dbpertenant-performance-monitoring.md)に関するページを参照してください。

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-log-analytics"></a>Log Analytics を使用してデータベースとエラスティック プールのパフォーマンスを監視および管理する

Azure SQL Database については、Azure Portal でデータベースとプールを監視してアラートを設定できます。 この組み込みの監視機能とアラート設定機能は便利ですが、リソースに固有でもあります。 つまり、大規模なインストールを監視することや、複数のリソースやサブスクリプションを統合されたビューで確認することには向いていません。

大規模なシナリオでは、監視とアラート設定に Log Analytics を使用できます。 Log Analytics は、潜在的に多くのサービスからワークスペースに集められた診断ログやテレメトリに対する分析を可能にする、別個の Azure サービスです。 Log Analytics には、オペレーション データを分析できるクエリ言語とデータ視覚化ツールが組み込まれています。 SQL Analytics ソリューションには、エラスティック プールとデータベースを監視およびアラートを設定するためのさまざまな定義済みのビューとクエリが用意されています。 Log Analytics には、カスタム ビュー デザイナーも用意されています。

Log Analytics のワークスペースと分析ソリューションは、Azure Portal と Operations Management Suite の両方で開きます。 Azure Portal は新しいアクセス ポイントですが、一部の領域では Operations Management Suite ポータルの背後に配置される場合があります。

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>テナントでワークロードをシミュレートしてパフォーマンスの診断データを作成する 

1. PowerShell ISE で、*..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\Demo-PerformanceMonitoringAndManagement.ps1* を開きます。 このスクリプトは、このチュートリアル中にいくつかのロード生成シナリオで実行することがあるため、開いたままにしておいてください。
1. まだ行っていない場合は、テナントのバッチをプロビジョニングすることで、監視コンテキストの関連性が高まります。 このプロセスには数分かかります。

   a. **$DemoScenario = 1** _(テナントのバッチのプロビジョニング)_ を設定します。

   b. F5 キーを押してスクリプトを実行し、追加の 17 テナントをデプロイします。

1. ロード ジェネレーターを開始して、すべてのテナントで疑似ロードを実行します。

    a. **$DemoScenario = 2** _(標準強度負荷の生成 (およそ 30 DTU))_ を設定します。

    b. F5 キーを押してスクリプトを実行します。

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Wingtip Tickets SaaS テナント単位データベース アプリケーションのスクリプトを入手する

Wingtip Tickets SaaS マルチテナント データベースのスクリプトとアプリケーション ソース コードは、[WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub リポジトリで入手できます。 Wingtip Tickets PowerShell スクリプトをダウンロードし、ブロックを解除する手順については、[一般的なガイダンス](saas-tenancy-wingtip-app-guidance-tips.md)に関する記事をご覧ください。

## <a name="install-and-configure-log-analytics-and-the-azure-sql-analytics-solution"></a>Log Analytics と Azure SQL Analytics ソリューションをインストールして構成する

Log Analytics は別個のサービスとして構成する必要があります。 Log Analytics は、ログ データ、テレメトリ、およびメトリックを Log Analytics ワークスペース内に収集します。 Azure の他のリソースと同じように、Log Analytics ワークスペースを作成する必要があります。 ワークスペースは監視対象のアプリケーションと同じリソース グループ内に作成する必要はありません。 多くの場合は、そうすることが最も理にかなっています。 Wingtip Tickets アプリの場合、単一のリソース グループを使用することで、アプリケーションによってワークスペースが確実に削除されます。

1. PowerShell ISE で、*..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\Log Analytics\\Demo-LogAnalytics.ps1* を開きます。
1. F5 キーを押してスクリプトを実行します。

これで Azure Portal または Operations Management Suite ポータルで Log Analytics を開くことができます。 Log Analytics ワークスペース内にテレメトリが収集されて視覚化されるまで、数分かかります。 システムが診断データを収集する時間を長くすればするほど、エクスペリエンスの関連性が高くなります。 

## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Log Analytics と SQL Analytics ソリューションを使用してプールとデータベースを監視する


この演習では、Log Analytics と Operations Management Suite ポータルを開き、データベースとプールで収集されるテレメトリを調べます。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。 **[すべてのサービス]** を選択して Log Analytics を選択します。 Log Analytics を検索します。

   ![Log Analytics を開く](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. _wtploganalytics-&lt;user&gt;_ という名前のワークスペースを選択します。

1. **[概要]** を選択して、Azure ポータルで Log Analytics ソリューションを選択します。

   ![概要](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > ソリューションがアクティブになるまで数分かかる場合があります。 

1. **[Azure SQL Analytics]** タイルを選択して開きます。

    ![概要タイル](media/saas-dbpertenant-log-analytics/overview.png)

1. ソリューションのビューは横方向にスクロールし、固有の内部スクロール バーが下部に表示されます。 必要に応じてページを更新します。

1. タイルまたは個別のデータベースをクリックしてドリルダウン エクスプローラーを開き、概要ページを確認します。

    ![Log Analytics のダッシュボード](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. フィルター設定を切り替えて、時間の範囲を変更します。 このチュートリアルでは、 **[過去 1 時間]** を選択します。

    ![時間フィルター](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. クエリの使用状況と該当のデータベースのメトリックを調査する単一のデータベースを選択します。

    ![データベースの分析](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. 使用状況のメトリックを表示するには、分析ページを右にスクロールします。
 
     ![データベースのメトリック](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. 分析ページを左にスクロールして、**[リソース情報]** 一覧のサーバー タイルを選択します。  

    ![[リソース情報] 一覧](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    サーバー上のプールとデータベースを表示するページが開かれます。

    ![プールとデータベースを備えたサーバー](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. プールを選択します。 開かれたプール ページで、右にスクロールしてプールのメトリックを表示します。 

    ![プールのメトリック](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


1. Log Analytics ワークスペースに戻り、**[OMS ポータル]** を選択して、このポータルでワークスペースを開きます。

    ![Operations Management Suite ポータル タイル](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

Operations Management Suite ポータルでは、ワークスペース内のログとメトリック データをさらに詳しく調査できます。 

Log Analytics の監視とアラートは、Azure Portal の各リソースで定義されているアラートとは異なり、ワークスペース内のデータに対するクエリに基づいています。 アラートがクエリに基づくようにすることで、すべてのデータベースを対象とする単一のアラートを定義でき、データベース別に 1 つずつアラートを定義する必要はありません。 クエリは、ワークスペースで利用可能なデータによってのみ制限されます。

Log Analytics を使用してアラートのクエリと設定を行う方法については、「[Log Analytics のアラート ルールの操作](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating)」を参照してください。

Log Analytics for SQL Database では、ワークスペース内のデータ量に基づいて課金されます。 このチュートリアルでは、無料のワークスペースを作成しました。このワークスペースの制限は、1 日あたり 500 MB です。 この制限に達すると、データはワークスペースに追加されなくなります。


## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Log Analytics をインストールして構成します。
> * Log Analytics を使用してプールとデータベースを監視する。

[テナント分析のチュートリアル](saas-dbpertenant-log-analytics.md)を試す。

## <a name="additional-resources"></a>その他のリソース

* [Wingtip Tickets SaaS テナント単位データベース アプリケーションの初期のデプロイに基づく作業のための追加のチュートリアル](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)
