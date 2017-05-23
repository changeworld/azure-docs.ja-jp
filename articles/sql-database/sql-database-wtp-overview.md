---
title: "すぐに利用可能な SaaS (Azure SQL Database を使用する SaaS アプリケーションの例) | Microsoft Docs"
description: "SQL Database を使用して SaaS アプリケーションを構築します"
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
ms.openlocfilehash: bf5745a788cd9ab6bf2ea8d5d97b8c04f083fc5d
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---
# <a name="introduction-to-the-wingtip-tickets-platform-wtp-sample-saas-application"></a>Wingtip Tickets Platform (WTP) サンプル SaaS アプリケーションの概要

Wingtip Tickets Platform (WTP) SaaS アプリケーションは、SQL Database の独自のメリットを示すためのサンプルのマルチテナント アプリです。 このアプリは、SaaS アプリケーション パターンとしてテナント単位のデータベースを使用して、複数のテナントにサービスを提供します。 WTP アプリの目的は、SaaS の設計と管理のパターンを含む各種 SaaS シナリオを実現している Azure SQL Database の機能を紹介することにあります。 このアプリをすぐに使い始めるには、[WTP アプリを 5 分以内にデプロイする方法に関するページ](sql-database-saas-tutorial.md)をご覧ください。

WTP アプリをデプロイしたら、初期のデプロイに基づく作業を行うために役立つ[複数のチュートリアル](#sql-database-saas-tutorials)を参照します。 各チュートリアルでは、SaaS アプリケーションに実装される一般的なタスクについて説明します。 SQL Database の組み込みの機能を活用する SaaS パターンに従ってタスクが実装されます。 このパターンには、新しいテナントのプロビジョニング、テナント データベースの復元、すべてのテナントでの分散クエリの実行、およびすべてのテナント データベースでのスキーマの変更の展開があります。 各チュートリアルには、再利用可能なスクリプトとその詳細な説明が含まれています。これらを利用すると、SaaS 管理パターンの理解、およびお使いのアプリケーションへの同じ SaaS 管理パターンの実装を大幅に簡略化できます。

WTP アプリケーションは機能の充実した魅力的なサンプル アプリケーションですが、データ層に関連する主要な SaaS パターンに注目することが重要です。 つまり、データ層に注目し、アプリ自体を過剰に分析しないでください。 これらの主要な SaaS パターンの実装を理解することが、特定のビジネス要件に必要な変更を考慮しながら、アプリケーションにこれらのパターンを実装するための鍵となります。



## <a name="application-architecture"></a>アプリケーションのアーキテクチャ

WTP アプリでは、テナント単位のデータベース モデルを使用します。また、SQL エラスティック プールを使用して効率を最大化します。
プロビジョニングの管理および接続にはテナント カタログを使用します。
統合アプリケーション、プール、およびデータベースの監視、およびアラート (OMS)。
クロステナント スキーマと参照データ管理 (エラスティック データベース ジョブ)。
クロステナント クエリ、運用分析 (エラスティック クエリ)。
リーチを広げるための地理的分散データの使用。
ビジネス継続性、シングル テナントの回復 (PITR)、大規模な DR (geo リストア、geo レプリケーション、自動 DR)、テナントのセルフ サービス管理 (管理 API を使用)、自分に原因があるエラーから回復するための PITR。

コア Wingtip アプリケーションは、3 つのサンプル テナントと 1 つのカタログ データベースを備えたプールを使用します。

![WTP のアーキテクチャ](media/sql-database-wtp-overview/wtp-architecture.png)


## <a name="sql-database-wtp-saas-tutorials"></a>SQL Database WTP SaaS のチュートリアル

次に示すチュートリアルは、[Wingtip Tickets Platform SaaS サンプル アプリケーション](sql-database-saas-tutorial.md)の初期のデプロイに基づくものです。

| 領域 | Description | スクリプトの場所 |
|:--|:--|:--|
|[テナントのプロビジョニングおよびカタログ登録のチュートリアル](sql-database-saas-tutorial-provision-and-catalog.md)| 新しいテナントをプロビジョニングしてカタログに登録する | [GitHub のスクリプト](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Provision%20and%20Catalog) |
|[パフォーマンスの監視と管理のチュートリアル](sql-database-saas-tutorial-performance-monitoring.md)| データベースとプールのパフォーマンスを監視および管理する | [GitHub のスクリプト](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Performance%20Monitoring%20and%20Management) |
|[シングル テナントの復元のチュートリアル](sql-database-saas-tutorial-restore-single-tenant.md)| テナント データベースを復元する | [GitHub のスクリプト](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Business%20Continuity%20and%20Disaster%20Recovery/RestoreTenant) |
|[テナント スキーマの管理のチュートリアル](sql-database-saas-tutorial-schema-management.md)| すべてのテナントでクエリを実行する  | [GitHub のスクリプト](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Schema%20Management) |
|[アドホック分析の実行のチュートリアル](sql-database-saas-tutorial-adhoc-analytics.md) | アドホック分析データベースを作成し、すべてのテナントでクエリを実行する  | [GitHub のスクリプト](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Operational%20Analytics/Adhoc%20Analytics) |
|[Log Analytics による管理 (OMS) のチュートリアル](sql-database-saas-tutorial-log-analytics.md) | Log Analytics を構成および確認する | [GitHub のスクリプト](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Performance%20Monitoring%20and%20Management/LogAnalytics) |
|[テナント分析の実行のチュートリアル](sql-database-saas-tutorial-tenant-analytics.md) | テナント分析クエリを設定および実行する | [GitHub のスクリプト](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Operational%20Analytics/Tenant%20Analytics) |

## <a name="get-the-wingtip-application-scripts"></a>Wingtip アプリケーションのスクリプトを取得する

Wingtip Tickets のスクリプトとアプリケーションのソース コードは、[WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) GitHub リポジトリから入手できます。 スクリプト ファイルは、[Learning Modules フォルダー](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules)にあります。 **Learning Modules** フォルダーを、構造を保ったままローカル コンピューターにダウンロードしてください。

## <a name="working-with-the-wtp-powershell-scripts"></a>WTP PowerShell スクリプトを操作する

WTP アプリケーションを使用するメリットは、提供されるスクリプトを表示し、各 SaaS パターンの実装方法を確認することでもたらされます。

提供されるスクリプトやモジュールを表示し、それらを簡単にステップスルーして理解を深めるには、[Windows PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise) を使用します。 名前の先頭が *Demo-* になっている大部分のスクリプトには、実行前に変更可能な変数が含まれているため、PowerShell ISE を使用すると、これらのスクリプトを簡単に操作できます。

WTP アプリの各デプロイの **UserConfig.psm1** ファイルには 2 つのパラメーターが格納されています。これらのパラメーターは、デプロイ時に定義したリソース グループとユーザー名の値を設定します。 デプロイが完了したら、_ResourceGroupName_ パラメーターと _Name_ パラメーターを設定する **UserConfig.psm1** モジュールを編集します。 これらの値は、正常に実行するためにその他のスクリプトで使用されるので、デプロイの完了時に設定することをお勧めします。



### <a name="execute-scripts-by-pressing-f5"></a>F5 キーを押してスクリプトを実行する

複数のスクリプトでは *$PSScriptRoot* を使用してフォルダー間の移動を許可します。この変数が評価されるのは、**F5** キーを押してスクリプトが実行される場合のみです。  選択項目を強調表示して実行する (**F8** キー) とエラーが発生するため、WTP スクリプトの実行時には **F5** キーを押してください。

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>スクリプトをステップスルーして実装を確認する

スクリプトを調べる作業の真の価値は、スクリプトをステップスルーしてその処理内容を確認することでもたらされます。 第 1 レベルの _Demo-_ スクリプトを確認すると、各タスクの実行に必要な手順を示す概要のワークフローを簡単に読み取ることができます。 各 SaaS パターンの実装の詳細を確認するには、個々の呼び出しについて詳しく調べます。

[PowerShell スクリプトのデバッグ](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)と操作に関するヒントを次に示します。

* PowerShell ISE で demo- スクリプトを開いて構成します。
* **F5** キーを押して実行または続行します。 *$PSScriptRoot* は選択したスクリプトの実行時に評価されないため、**F8** キーの使用はお勧めできません。
* 行をクリックまたは選択して **F9** キーを押し、ブレークポイントを設定します。
* **F10** キーを使用して、関数またはスクリプトの呼び出しをステップ オーバーします。
* **F11** キーを使用して、関数またはスクリプトの呼び出しにステップ インします。
* **Shift + F11** キーを使用して、現在の関数またはスクリプトの呼び出しからステップ アウトします。




## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>SSMS を使用してデータベース スキーマを調べ、SQL クエリを実行する

WTP のサーバーとデータベースに接続して参照するには、[SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) を使用します。

WTP サンプル アプリには、接続先となる 2 つの SQL Database サーバー (*tenants1* サーバーと *catalog* サーバー) が最初に用意されています。


1. *SSMS* を開き、*tenants1-&lt;ユーザー&gt;.database.windows.net* サーバーに接続します。
2. **[接続]** > **[データベース エンジン...]** をクリックします。

   ![カタログ サーバー](media/sql-database-wtp-overview/connect.png)

1. デモの資格情報は、ログイン = *developer*、パスワード = *P@ssword1* です。

   ![connection](media\sql-database-wtp-overview\tenants1-connect.png)

1. 手順 2 と 3 を繰り返し、*catalog-&lt;ユーザー&gt;.database.windows.net* サーバーに接続します。

接続に成功すると、両方のサーバーが表示されます。 プロビジョニングしたテナントの数に応じて、データベースの数が異なる場合があります。

![オブジェクト エクスプローラー](media/sql-database-wtp-overview/object-explorer.png)



## <a name="next-steps"></a>次のステップ

[Wingtip Tickets SaaS サンプル アプリケーションのデプロイ](sql-database-saas-tutorial.md)
