---
title: "複数の Azure SQL データベースにわたるアドホック分析クエリの実行 | Microsoft Docs"
description: "Wingtip SaaS マルチテナント アプリで複数の SQL データベースに対してアドホック分析クエリを実行します。"
keywords: "SQL データベース チュートリアル"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: develop apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: bf003a3677ed27bc833de59ef61f7637a6899d37
ms.contentlocale: ja-jp
ms.lasthandoff: 06/01/2017


---
# <a name="run-ad-hoc-analytics-queries-across-all-wingtip-saas-tenants"></a>すべての Wingtip SaaS テナントにわたるアドホック分析クエリの実行

このチュートリアルでは、アドホック分析データベースを作成し、すべてのテナントにわたって複数のクエリを実行します。 これらのクエリでは、Wingtip SaaS アプリの日常業務データに埋もれている洞察を抽出できます。

アドホック分析クエリを (複数のテナントにわたって) 実行するために、Wingtip SaaS アプリは分析データベースと共に[エラスティック クエリ](sql-database-elastic-query-overview.md)を使用します。


このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]

> * テナントの境界を越えたクエリを可能にする、各データベースのグローバル ビューについて
> * アドホック分析データベースをデプロイする方法
> * すべてのテナント データベースにわたって分散クエリを実行する方法



このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。

* Wingtip SaaS アプリがデプロイされている。 5 分未満でデプロイするには、[Wingtip SaaS アプリケーションのデプロイと確認](sql-database-saas-tutorial.md)に関するページを参照してください。
* Azure PowerShell がインストールされている。 詳しくは、「[Azure PowerShell を使ってみる](https://docs.microsoft.com/powershell/azure/get-started-azureps)」をご覧ください。


## <a name="ad-hoc-analytics-pattern"></a>アドホック分析パターン

SaaS アプリケーションで得られる優れた機会の 1 つは、クラウドで一元的に保存されている膨大なテナント データを使用することです。 このデータを使用することで、アプリケーションの操作と使用状況、テナント、ユーザー、設定、動作などに関する洞察が得られます。このような洞察は、機能の開発、操作性の改良、アプリやサービスへのその他の投資に役立ちます。

単一のマルチテナント データベース内にあるこのデータにアクセスすることは簡単ですが、数千ものデータベースにわたる規模で分散されている場合は簡単ではありません。 1 つのアプローチはエラスティック クエリを使用するものであり、この方法では、分散した一連のデータベースにわたって共通スキーマでアドホックなクエリを行えます。 エラスティック クエリは、分散 (テナント) データベース内のテーブルまたはビューをミラー化する外部テーブルが定義されている単一の "*ヘッド*" データベースを使用します。 このヘッド データベースに送信されたクエリはコンパイルされ、分散クエリ プランが生成されます。このクエリの一部は、必要に応じてテナント データベースにプッシュダウンされます。 エラスティック クエリでは、カタログ データベースのシャード マップを使用して、テナント データベースの場所を指定します。 セットアップとクエリは、標準的な [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference) を使用して簡単に行うことができ、Power BI や Excel などのツールからのアドホック クエリをサポートしています。

## <a name="get-the-wingtip-application-scripts"></a>Wingtip アプリケーションのスクリプトを取得する

Wingtip SaaS のスクリプトとアプリケーション ソース コードは、[WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) GitHub リポジトリから入手できます。 [Wingtip SaaS のスクリプトをダウンロードする手順](sql-database-wtp-overview.md#download-the-wingtip-saas-scripts)。


## <a name="explore-the-global-views"></a>グローバル ビューを詳しく知る

Wingtip SaaS アプリケーションは、データベースあたりのテナント モデルを使用して構築されるため、テナント データベース スキーマはシングル テナントの観点から定義されます。 テナント固有の情報は、*Venue* という 1 つのテーブルに存在します。このテーブルは常に 1 行で構成されており、さらに主キーなしでヒープとして設計されています。  スキーマ内の他のテーブルは、*Venue* テーブルに関連付ける必要はありません。通常の用途では、データがどのテナントに属しているかは明白です。  ただし、すべてのデータベースに対してクエリを実行する際は、データベース内のテーブルのデータを特定のテナントに関連付けることが重要になります。 これを簡単にするために、各テナントの "グローバル" ビューを提供する一連のビューがテナント データベースに追加されます。 これらのグローバル ビューは、グローバルにクエリが実行される各テーブルにテナント ID を投影します。 これにより、各テナントのデータを簡単に識別できます。 便宜上、これらのビューはすべてのテナント データベースに事前に作成されています (さらに、新しいテナントがプロビジョニングされたときにこれらのグローバル ビューが使用できるように、ゴールデン データベースにも事前に作成されています)。

1. SSMS を開き、[tenants1-&lt;USER&gt; サーバーに接続します](sql-database-wtp-overview.md#explore-database-schema-and-execute-sql-queries-using-ssms)。
1. **[データベース]** を展開し、**[contosoconcerthall]** を右クリックして、**[新しいクエリ]** を選択します。
1. 次のクエリを実行して、シングル テナント テーブルとグローバル ビューの違いを調べます。

   ```T-SQL
   -- This is the base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   SELECT * FROM Venues

   -- The base Events table which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

サンプル データベースでは、Venue 名のハッシュから整数 ID を計算しましたが、一意の値はどのような方法で求めてもかまいません。 これは、カタログにテナント キーを作成する方法と似ていますが、*adhocanalytics* データベース内のテナント ID とカタログ キーとが同じでなければならないという要件はありません。

"*ビュー*" を調べて、それがどのように作成されたかを確認するには:

1. **オブジェクト エクスプローラー**で、**[contosoconcethall]** > **[ビュー]** の順に展開します。

   ![ビュー](media/sql-database-saas-tutorial-adhoc-analytics/views.png)

1. **[dbo.Venues]** を右クリックします。
1. **[ビューをスクリプト化]** > **[新規作成]** > **[新しいクエリ エディター ウィンドウ]** の順に選択します。

任意の "*ビュー*" にこれを実行して、それがどのように作成されたかを調べます。

## <a name="deploy-the-database-used-for-ad-hoc-analytics-queries"></a>アドホック分析クエリに使用されるデータベースをデプロイする

この演習では、*adhocanalytics* データベースをデプロイします。 このデータベースには、すべてのテナント データベースに対してクエリを実行する場合に使用されるスキーマが含まれています。 データベースは、既存のカタログ サーバーにデプロイされます。これは、管理関連のデータベースすべてが格納されているサーバーです。

1. ...\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Deploy-AdhocAnalyticsDB.ps1* を開きます。
1. 下へスクロールして、SQL スクリプトに `$commandText` を代入しているセクションに移動します。 そのスクリプトを確認し、次の点に注意します。

   1. エラスティック クエリは、データベース スコープの資格情報を使用して、各テナント データベースにアクセスします。 この資格情報は、すべてのデータベースで使用できる必要があり、通常、これらのアドホック クエリを有効にするために必要な最小限の権限が付与されている必要があります。
   1. カタログ データベースでテナント シャード マップを使用するように定義されている外部データ ソース。  これを外部データ ソースとして使用すると、クエリが実行された時点でカタログに登録されているすべてのデータベースにクエリが分散されます。
   1. 前のセクションで説明したグローバル ビューを参照する外部テーブル。
   1. 作成され、データが設定されているローカル テーブル *VenueTypes*。  この参照データ テーブルは、すべてのテナント データベースで共通するため、ここではローカル テーブルとして表すことができます。このテーブルでは、クエリによっては、テナント データベースと *adhocanalytics* データベースの間で移動されるデータ量が削減される場合があります。


1. 今度は、*PowerShell ISE* で ...\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalytics.ps1* を開き、次の値を設定します。
   * **$DemoScenario** = 2、**アドホック分析データベースをデプロイする**。

1. **F5** キーを押してスクリプトを実行し、*adhocanalytics* データベースを作成します。

   ここでは、"*RPC サーバーを利用できません*" という警告は無視してかまいません。

これで、分散クエリを実行するために使用できる *adhocanalytics* データベースが用意され、すべてのテナントにわたって洞察を集めることができるようになりました。

![adhocanalytics データベース](media/sql-database-saas-tutorial-adhoc-analytics/adhocanalytics.png)

## <a name="run-ad-hoc-analytics-queries"></a>アドホック分析クエリの実行

*adhocanalytics* データベースの設定が済んだら、いくつかのアドホック クエリを実行します。

1. SSMS で ....\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalyticsQueries.sql* を開きます。
1. **adhocanalytics** データベースに接続していることを確認します。
1. 実行する個々のクエリを選択し、**F5** キーを押します。

    ![クエリ](media/sql-database-saas-tutorial-adhoc-analytics/query.png)




## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]

> * アドホック分析データベースをデプロイする
> * すべてのテナント データベースにわたって分散クエリを実行する

それでは、[テナント分析のチュートリアル](sql-database-saas-tutorial-tenant-analytics.md)を試してみましょう。

## <a name="additional-resources"></a>その他のリソース

* [Wingtip SaaS アプリケーションに基づく作業のための追加のチュートリアル](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [エラスティック クエリ](sql-database-elastic-query-overview.md)

