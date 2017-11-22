---
title: "複数の Azure SQL データベースにわたるアドホック レポート クエリの実行 | Microsoft Docs"
description: "マルチテナント アプリ内の複数の SQL データベースにわたってアドホック レポート クエリを実行します。"
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
ms.topic: articles
ms.date: 11/13/2017
ms.author: billgib; sstein; AyoOlubeko
ms.openlocfilehash: db8a079c76f38bbf7b90f8d914ce1bbf192343d7
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2017
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-azure-sql-databases"></a>複数の Azure SQL データベースにわたるアドホック分析クエリの実行

このチュートリアルでは、テナント データベース セット全体を対象に分散クエリを実行し、アドホックのインタラクティブなレポートを有効にします。 これらのクエリでは、Wingtip Tickets SaaS アプリの日常業務データに埋もれている洞察を抽出できます。 これを行うには、追加の分析データベースをカタログ サーバーにデプロイし、エラスティック クエリを使用して分散クエリを有効にします。


このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]

> * テナントの境界を越えた効率的なクエリを可能にする、各データベースのグローバル ビューについて
> * アドホック レポート データベースをデプロイする方法
> * すべてのテナント データベースにわたって分散クエリを実行する方法



このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。


* Wingtip Tickets SaaS Database Per Tenant アプリをデプロイします。 5 分未満でデプロイするには、「[Deploy and explore the Wingtip Tickets SaaS Multi-tenant Database application (Wingtip Tickets SaaS Database Per Tenant アプリケーションのデプロイと探索)](saas-dbpertenant-get-started-deploy.md)」を参照してください。
* Azure PowerShell がインストールされている。 詳しくは、「[Azure PowerShell を使ってみる](https://docs.microsoft.com/powershell/azure/get-started-azureps)」をご覧ください。
* SQL Server Management Studio (SSMS) をインストールしている。 SSMS のダウンロードとインストールについては、[SQL Server Management Studio (SSMS) のダウンロード](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)に関するページを参照してください。


## <a name="ad-hoc-reporting-pattern"></a>アドホック レポートのパターン

![アドホック レポートのパターン](media/saas-tenancy-adhoc-analytics/adhocreportingpattern_dbpertenant.png)

SaaS アプリケーションで得られる優れた機会の 1 つは、アプリケーションの操作と使用状況に関する洞察を得るために、クラウドで一元的に保存されている膨大なテナント データを使用できることです。 このような洞察は、機能の開発、操作性の改良、アプリやサービスへのその他の投資に役立ちます。

単一のマルチテナント データベース内にあるこのデータにアクセスすることは簡単ですが、数千ものデータベースにわたる規模で分散されている場合は簡単ではありません。 1 つのアプローチは[エラスティック クエリ](sql-database-elastic-query-overview.md)を使用するものであり、この方法では、分散した一連のデータベースにわたって共通スキーマでクエリを行えます。 これらのデータベースは、別のリソース グループとサブスクリプションに分散できますが、共通のログインを共有する必要があります。 エラスティック クエリは、分散 (テナント) データベース内のテーブルまたはビューをミラー化する外部テーブルが定義されている単一の "*ヘッド*" データベースを使用します。 このヘッド データベースに送信されたクエリはコンパイルされ、分散クエリ プランが生成されます。このクエリの一部は、必要に応じてテナント データベースにプッシュダウンされます。 エラスティック クエリでは、カタログ データベースのシャード マップを使用して、すべてのテナント データベースの場所を特定します。 セットアップとクエリは、標準的な [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference) を使用して簡単に行うことができ、Power BI や Excel などのツールからのアドホック クエリをサポートしています。

テナント データベース全体でクエリを分散することで、エラスティック クエリはライブ プロダクション データを短時間で洞察できます。 ただし、エラスティック クエリはたくさんのデータベースからデータを引き出すため、1 つのマルチテナント データベースに同じようなクエリを送信する場合と比較し、待機時間が長くなることがあります。 返されるデータを最小限に抑えるように、クエリを設計してください。 頻繁に使用される、あるいは複雑な分析クエリ/レポートとは対照的に、エラスティック クエリは多くの場合、少量のリアルタイム データの問い合わせに最適です。 クエリの動作に問題がある場合、[実行プラン](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan)を見て、クエリのどの部分がリモート データベースにプッシュダウンされているか、どのくらいのデータが返されているかを確認してください。 複雑な分析処理を必要とするクエリは、分析クエリのために最適化された専用のデータベースやデータ ウェアハウスにテナント データを抽出することで、より効果的に機能することがあります。 このパターンは[テナント分析のチュートリアル](saas-tenancy-tenant-analytics.md)で説明されています。 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Wingtip Tickets SaaS Database Per Tenant アプリケーション スクリプトを入手する

Wingtip Tickets SaaS Database Per Tenant のスクリプトとアプリケーション ソース コードは、[WingtipTicketsSaaS-DbPerTenant Github リポジトリ](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/)で入手できます。 必ず readme ファイルで説明されているブロック解除手順に従ってください。

## <a name="create-ticket-sales-data"></a>チケット売り上げデータを作成する

より興味深いデータ セットにクエリを実行する目的で、チケットジェネレーターを実行し、チケット売り上げデータを作成します。

1. *PowerShell ISE* で、...\\Learning Modules\\Operational Analytics\\Adhoc Reporting\\*Demo-AdhocReporting.ps1* スクリプトを開き、次の値を設定します。
   * **$DemoScenario** = 1, **すべての会場のイベントのチケットを購入**.
2. **F5** キーを押してスクリプトを実行し、チケットの売り上げを生成します。 スクリプトが実行されている間、このチュートリアルの手順を続行します。 チケット データは、*アドホック分散クエリの実行*セクションで照会され、チケットジェネレーターが完了するまで待機します。

## <a name="explore-the-global-views"></a>グローバル ビューを詳しく知る

Wingtip Tickets SaaS Database Per Tenant アプリケーションでは、各テナントに 1 つのデータベースが指定されています。 そのため、データベース テーブルに含まれるデータは、単一テナントのパースペクティブにスコープを設定しています。 ただし、すべてのデータベースにクエリを実行するときは、エラスティック クエリがデータを、テナントによりシャードされた 1 つの論理データベースにデータが含まれるかのように処理できることが重要です。 

このパターンをシミュレートするために、'グローバル' ビュー セットがテナント データベースに追加されます。このビューは、グローバルにクエリ実行される各テーブルにテナント ID を与えます。 たとえば、*VenueEvents* ビューは、計算した *VenueId* を *Events* テーブルから与えられた列に追加します。 同様に、*VenueTicketPurchases* と *VenueTickets* ビューは、それぞれのテーブルから与えられた計算済みの *VenueId* 列を追加します。 これらのビューは、*VenueId* 列が存在する場合、クエリを並列化して適切なリモート テナント データベースにそれらをプッシュダウンするために、エラスティック クエリによって使用されます。 これにより返されるデータの量が劇的に減り、クエリがたくさんあってもパフォーマンスが相当上がります。 これらのグローバル ビューはすべてのテナント データベースで事前作成されています。

1. SSMS を開き、[tenants1-&lt;USER&gt; サーバーに接続します](saas-dbpertenant-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms)。
2. **[データベース]** を展開し、**[contosoconcerthall]** を右クリックして、**[新しいクエリ]** を選択します。
3. 次のクエリを実行して、シングル テナント テーブルとグローバル ビューの違いを調べます。

   ```T-SQL
   -- The base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   SELECT * FROM Venues

   -- The base Events table, which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

これらのビューでは、*VenueId* は Venue 名のハッシュとして計算されましたが、一意の値はどのような方法で求めてもかまいません。 この方法は、カタログで使用するためにテナント キーを計算する方法に似ています。

*Venues* ビューの定義を調べるには:

1. **オブジェクト エクスプローラー**で、**[contosoconcethall]** > **[ビュー]** の順に展開します。

   ![ビュー](media/saas-tenancy-adhoc-analytics/views.png)

2. **[dbo.Venues]** を右クリックします。
3. **[ビューをスクリプト化]** > **[新規作成]** > **[新しいクエリ エディター ウィンドウ]** の順に選択します。

他の *Venue* ビューをスクリプト化し、*VenueId* の追加方法を確認します。

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>アドホック分散クエリに使用されるデータベースをデプロイする

この演習では、*adhocreporting* データベースをデプロイします。 これは、すべてのテナント データベースに対してクエリを実行する場合に使用されるスキーマが含まれるヘッド データベースです。 このデータベースは既存のカタログ サーバーにデプロイされます。サンプル アプリのすべての管理関連データベースに使用されるサーバーです。

1. *PowerShell ISE*で ...\\Learning Modules\\Operational Analytics\\Adhoc Reporting\\*Demo-AdhocReporting.ps1* を開き、次の値を設定します。
   * **$DemoScenario** = 2、**アドホック分析データベースをデプロイする**。

2. **F5** キーを押してスクリプトを実行し、*adhocreporting* データベースを作成します。

次のセクションでは、分散クエリの実行に利用できるように、データベースにスキーマを追加します。

## <a name="configure-the-head-database-for-running-distributed-queries"></a>分散クエリを実行するための "ヘッド" データベースを構成する

この演習では、テナント データベース全体でクエリ実行を可能にするアドホック分析データベースにスキーマ (外部データ ソースと外部テーブル定義) を追加します。

1. SQL Server Management Studio を開き、前の手順で作成したアドホック レポート データベースに接続します。 データベースの名前は *adhocreporting* になります。
2. SSMS で、...\Learning Modules\Operational Analytics\Adhoc Reporting\ *Initialize-AdhocReportingDB.sql* を開きます。
3. SQL スクリプトを読み、次の点に注意します。

   エラスティック クエリは、データベース スコープの資格情報を使用して、各テナント データベースにアクセスします。 この資格情報は、すべてのデータベースで使用できる必要があり、通常、これらのアドホック クエリを有効にするために必要な最小限の権限が付与されている必要があります。

    ![資格情報を作成する](media/saas-tenancy-adhoc-analytics/create-credential.png)

   カタログ データベースでテナント シャード マップを使用するように定義されている外部データ ソース。 これを外部データ ソースとして使用すると、クエリが実行されたとき、カタログに登録されているすべてのデータベースにクエリが分散されます。 デプロイごとにサーバー名が異なるため、この初期化スクリプトでは、スクリプトが実行された現在のサーバー (@@servername) を検索することでカタログ サーバーの場所を取得します。

    ![外部データ ソースを作成する](media/saas-tenancy-adhoc-analytics/create-external-data-source.png)

   外部テーブルは、前のセクションで説明したグローバル ビューを参照します。**DISTRIBUTION = SHARDED(VenueId)** で定義されています。 各 *VenueId* は 1 つのデータベースにマッピングされるため、次のセクションで示すように、多くのシナリオでパフォーマンスが改善されます。

    ![外部テーブルを作成する](media/saas-tenancy-adhoc-analytics/external-tables.png)

   作成され、データが設定されているローカル テーブル *VenueTypes*。 この参照データ テーブルは、すべてのテナント データベースで共通です。ここではローカル テーブルとして表すことができます。また、共通データを入力できます。 クエリによっては、テナント データベースと *adhocreporting* データベースの間で移動するデータ量が減ることがあります。

    ![テーブルを作成する](media/saas-tenancy-adhoc-analytics/create-table.png)

   この方法で参照テーブルを追加する場合、テナント データベースを更新するときは必ず、テーブルのスキーマとデータを更新します。

4. **F5** キーを押してスクリプトを実行し、*adhocreporting* データベースを初期化します。 

これで分散クエリを実行し、すべてのテナントを対象に洞察を収集できます。

## <a name="run-ad-hoc-distributed-queries"></a>アドホック分散クエリを実行する

これで *adhocreporting* データベースが設定されたので、分散クエリをいくつか実行します。 クエリ処理の場所をより良く理解できるように実行プランを含めます。 

実行プランを調べるとき、プラン アイコンにカーソルを合わせると詳細が表示されます。 

外部データ ソースの定義時に **DISTRIBUTION = SHARDED(VenueId)** を設定したことで、多くのシナリオでパフォーマンスが改善します。これは重要なことなのでご留意ください。 各 *VenueId* は 1 つのデータベースにマッピングされるため、フィルター処理はリモートで簡単に実行されます。必要なデータのみが返されます。

1. SSMS で ...\\Learning Modules\\Operational Analytics\\Adhoc Reporting\\*Demo-AdhocReportingQueries.sql* を開きます。
2. **adhocreporting** データベースに接続していることを確認します。
3. **[クエリ]** メニューを選択し、**[実際の実行プランを含める]** をクリックします。
4. *[Which venues are currently registered?]* クエリを強調表示し、**F5** を押します。

   クエリは会場一覧全体を返します。すべてのテナントにクエリを実行し、各テナントからデータを返すことがいかに簡単かわかります。

   プランを調べ、コスト全体がリモート クエリであることを確認します。各テナント データベースに進み、会場情報を選択しているためです。

   ![SELECT * FROM dbo.Venues](media/saas-tenancy-adhoc-analytics/query1-plan.png)

5. 次のクエリを選択し、**F5** を押します。

   このクエリは、テナント データベースとローカル *VenueTypes* テーブル (*adhocreporting* データベースのテーブルであり、ローカル) のデータを結合します。

   プランを調べ、コストの大半がリモート クエリであることを確認します。各テナントの会場情報 (dbo.Venues) を問い合わせ、ローカル *VenueTypes* テーブルでクイック ローカル結合を行い、フレンドリ名を表示しているためです。

   ![リモートとローカルのデータで結合](media/saas-tenancy-adhoc-analytics/query2-plan.png)

6. 次に *On which day were the most tickets sold?* クエリを選択し、**F5** を押します。

   このクエリは、少しばかり複雑な結合と集計を行います。 重要な点は、処理の大半がリモートで行われるということです。再度、必要な行だけ返します。各会場で 1 日に売り上げたチケットの数を対象に 1 行だけ返します。

   ![クエリ](media/saas-tenancy-adhoc-analytics/query3-plan.png)


## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]

> * すべてのテナント データベースにわたって分散クエリを実行する
> * アドホック レポート データベースをデプロイし、それにスキーマを追加し、分散クエリを実行します。


それでは[テナント分析チュートリアル](saas-tenancy-tenant-analytics.md)を試してください。個別の分析データベースにデータを抽出することでより複雑な分析を処理できます。

## <a name="additional-resources"></a>その他のリソース

* [Wingtip Tickets SaaS Database Per Tenant アプリケーションに基づく作業のための追加のチュートリアル](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [エラスティック クエリ](sql-database-elastic-query-overview.md)
