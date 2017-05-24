---
title: "すべてのテナントにわたるアドホック分析クエリの実行 (Azure SQL Database を使用した SaaS アプリケーションの例) | Microsoft Docs"
description: "すべてのテナントにわたるアドホック分析クエリの実行"
keywords: "SQL データベース チュートリアル"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c00c25116002c46fc94c5ea59acc101a2055b689
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---
# <a name="run-ad-hoc-analytics-queries-across-all-wtp-tenants"></a>すべての WTP テナントにわたるアドホック分析クエリの実行

このチュートリアルでは、アドホック分析データベースを作成し、すべてのテナントにわたって複数のクエリを実行します。 これらのクエリでは、WTP アプリの日常業務データに埋もれている洞察を抽出できます。

アドホック分析クエリを (複数のテナントにわたって) 実行するために、WTP アプリは分析データベースとともに[エラスティック クエリ](sql-database-elastic-query-overview.md)を使用します。


このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]

> * アドホック分析データベースをデプロイする
> * すべてのテナント データベースにわたって分散クエリを実行する



このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。

* WTP アプリがデプロイされている。 5 分以内にデプロイを完了する方法については、[WTP SaaS アプリケーションのデプロイと確認に関するページ](sql-database-saas-tutorial.md)をご覧ください。
* Azure PowerShell がインストールされている。 詳しくは、「[Azure PowerShell を使ってみる](https://docs.microsoft.com/powershell/azure/get-started-azureps)」をご覧ください。


## <a name="ad-hoc-analytics-pattern"></a>アドホック分析パターン

SaaS アプリケーションで得られる優れた機会の 1 つは、クラウドで一元的に保存している膨大なテナント データを使用することです。 このデータを使用することで、アプリケーションの操作および使用状況、テナント、そのユーザー、およびその設定と動作に関する洞察が得られます。 得られた洞察は、機能の開発、操作性の改良、およびアプリとサービスへのその他の投資に役立ちます。

単一のマルチテナント データベース内にあるこのデータにアクセスすることは簡単ですが、数千ものデータベースにわたる規模で分散されている場合は簡単ではありません。 1 つのアプローチはエラスティック クエリを使用するものであり、この方法では、分散した一連のデータベースにわたって共通スキーマでアドホックなクエリを行えます。 エラスティック クエリは、分散 (テナント) データベース内のテーブルをミラー化する外部テーブルが定義されている単一の*ヘッド* データベースを使用します。 このヘッド データベースに送信されたクエリはコンパイルされ、分散クエリ プランが生成されます。このクエリの一部は、必要に応じてテナント データベースにプッシュダウンされます。 エラスティック クエリでは、カタログ データベースのシャード マップを使用して、テナント データベースの場所を指定します。 セットアップとクエリは、通常の T-SQL を使用して簡単に行え、Power BI や Excel などのツールからのアドホック クエリをサポートしています。

## <a name="get-the-wingtip-application-scripts"></a>Wingtip アプリケーションのスクリプトを取得する

Wingtip Tickets のスクリプトとアプリケーションのソース コードは、[WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) Github リポジトリから入手できます。 スクリプト ファイルは、[Learning Modules フォルダー](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules)にあります。 **Learning Modules** フォルダーを、構造を保ったままローカル コンピューターにダウンロードします。

## <a name="deploy-the-database-used-for-ad-hoc-analytics-queries"></a>アドホック分析クエリに使用されるデータベースをデプロイする

この演習では、すべてのテナント データベースにわたるアドホック クエリの発行に使用されるスキーマを含んだアドホック分析データベースをデプロイします。

1. *PowerShell ISE* で ...\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalytics.ps1* を開き、次の値を設定します。
   * **$DemoScenario** = 2、**アドホック分析データベースをデプロイする**。

1. **F5** キーを押してスクリプトを実行し、アドホック分析用の新しい SQL データベースを作成して、それを WTP カタログに追加します。 TicketPurchases、Tickets、および Venues のテーブルがクエリできる外部テーブルとして追加されます。
   ここで「*RPC サーバーを利用できません*」という警告が発生しても問題ありません。


これで、分散クエリを実行するために使用できる *adhocanalytics* データベースが用意され、すべてのテナントにわたって洞察を集めることができるようになりました。

## <a name="run-ad-hoc-analytics-queries"></a>アドホック分析クエリの実行

この演習では、アドホック分析クエリを実行して、WTP アプリケーションからのテナントの洞察を明らかにします。

1. SSMS で ....\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalyticsQueries.sql* を開きます。
1. **adhocanalytics** データベースに接続していることを確認します。
1. 実行する個々のクエリを選択し、**F5** キーを押します。

    ![クエリ](media/sql-database-saas-tutorial-adhoc-analytics/query.png)




## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]

> * アドホック分析データベースをデプロイする
> * すべてのテナント データベースにわたって分散クエリを実行する

[Log Analytics (OMS) のチュートリアル](sql-database-saas-tutorial-log-analytics.md)

## <a name="additional-resources"></a>その他のリソース

* [Wingtip Tickets Platform (WTP) アプリケーションの初期のデプロイに基づく作業のための追加のチュートリアル](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [エラスティック クエリ](sql-database-elastic-query-overview.md)

