---
title: "マルチテナント アプリで Azure SQL Database スキーマを管理する | Microsoft Docs"
description: "Azure SQL Database を使用するマルチテナント アプリケーションで複数のテナントのスキーマを管理します"
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
ms.date: 11/14/2017
ms.author: billgib
ms.openlocfilehash: 346177be29ec196464f4f441858222ac5d5eb8c3
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="manage-schema-for-multiple-tenants-in-a-multi-tenant-application-that-uses-azure-sql-database"></a>Azure SQL Database を使用するマルチテナント アプリケーションで複数のテナントのスキーマを管理します

[最初の Wingtip Tickets SaaS Multi-tenant Database チュートリアル](saas-multitenantdb-get-started-deploy.md)では、アプリがシャード マルチテナント データベースをどのようにプロビジョニングし、カタログに登録するかを示します。 他のアプリケーション同様、Wingtip Tickets SaaS アプリは時間の経過に従って進化し、データベースへの変更が必要になる場合があります。 変更には、新しいまたは変更されたスキーマ、新しいまたは変更された参照データ、およびアプリの最適なパフォーマンスを得るための定期的なデータベース メンテナンス タスクなどがあります。 SaaS アプリケーションにより、これらの変更は、大容量になる可能性のあるテナント データベース全体に体系的な方法でデプロイされる必要があります。 これらの変更が将来のテナント データベースに含まれるようにするには、プロビジョニング プロセスに変更を組み込む必要があります。

このチュートリアルでは、2 つのシナリオを扱います。すべてのテナントに対する参照データのデプロイと、参照データを含むテーブルでのインデックスの再調整です。 [エラスティック ジョブ](sql-database-elastic-jobs-overview.md)機能を使用して、これらの操作をテナント データベースにわたって実行したり、新しいデータベースのテンプレートとして使用される "*ゴールデン*" テナント データベースに対して実行したりします。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]

> * ジョブ アカウントを作成する
> * 複数のテナントにわたってクエリを実行する
> * すべてのテナント データベースでデータを更新する
> * すべてのテナント データベース内のテーブルにインデックスを作成する


このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。

* Wingtip Tickets SaaS マルチテナント データベース アプリケーションがデプロイされます。 5 分未満でデプロイするには、「[Deploy and explore the Wingtip Tickets SaaS Multi-tenant Database application (Wingtip Tickets SaaS マルチテナント データベース アプリケーションのデプロイと探索)](saas-multitenantdb-get-started-deploy.md)」を参照してください。
* Azure PowerShell がインストールされている。 詳しくは、「[Azure PowerShell を使ってみる](https://docs.microsoft.com/powershell/azure/get-started-azureps)」をご覧ください。
* 最新バージョンの SQL Server Management Studio (SSMS) のインストール。 [SSMS のダウンロードとインストール](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> "*このチュートリアルでは、限定プレビューに含まれる SQL Database サービスの機能を使用します (Elastic Database ジョブ)。このチュートリアルを実行する場合、サブスクリプション ID を SaaSFeedback@microsoft.com までお送りください (件名: Elastic Jobs Preview)。サブスクリプションが有効であることを通知するメールが届いたら、[最新のプレリリース ジョブ コマンドレットをダウンロードしてインストール](https://github.com/jaredmoo/azure-powershell/releases)します。このプレビューは限定的であるため、関連する質問やサポートについては、SaaSFeedback@microsoft.com にお問い合わせください。*


## <a name="introduction-to-saas-schema-management-patterns"></a>SaaS スキーマ管理パターンの概要

このサンプルで使用されているシャード マルチテナント データベース モデルを使用して、テナント データベースに任意の数のテナントを含めることができます。 このサンプルでは、マルチテナントとシングルテナント データベースを組み合わせて、「ハイブリッド」テナント管理モデルを有効にする可能性を探ります。 これらのデータベースの保守および管理は複雑です。 [エラスティック ジョブ](sql-database-elastic-jobs-overview.md)を使用すると、SQL データ層の管理が容易になります。 ジョブでは、ユーザーの対話操作または入力とは関係なく、データベースのグループに対して、タスク (T-SQL スクリプト) を安全かつ確実に実行できます。 このメソッドを使用して、スキーマおよび一般的な参照データの変更を、アプリケーションのすべてのテナントにわたってデプロイできます。 また、エラスティック ジョブを使用すると、新しいテナントの作成に使用されるデータベースの "*ゴールデン*" コピーを維持できます。これにより、スキーマおよび参照データを常に最新の状態に保つことができます。

![スクリーン](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>エラスティック ジョブの限定プレビュー

Azure SQL Database の統合機能となったエラスティック ジョブの新しいバージョンが入手可能です (追加のサービスまたはコンポーネントを必要としない)。 現在、この新しいバージョンのエラスティック ジョブは限定プレビュー中です。 現在、この限定プレビューでは、ジョブ アカウントを作成するための PowerShell と、ジョブを作成および管理するための T-SQL がサポートされています。

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-scripts"></a>Wingtip Tickets SaaS マルチテナント データベース アプリケーション スクリプトを入手する

Wingtip Tickets SaaS マルチテナント データベースのスクリプトとアプリケーション ソース コードは、[WingtipTicketsSaaS-MultiTenantDB](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub リポジトリで入手できます。 <!-- [Steps to download the Wingtip Tickets SaaS Multi-tenant Database scripts](saas-multitenantdb-wingtip-app-guidance-tips.md#download-and-unblock-the-wingtip-saas-scripts)-->

## <a name="create-a-job-account-database-and-new-job-account"></a>ジョブ アカウント データベースと新しいジョブ アカウントの作成

このチュートリアルでは、PowerShell を使用して、ジョブ アカウント データベースとジョブ アカウントを作成する必要があります。 MSDB や SQL エージェントと同様、エラスティック ジョブは Azure SQL データベースを使用して、ジョブ定義、ジョブの状態、履歴を格納します。 ジョブ アカウントが作成されたら、すぐにジョブを作成および監視することができます。

1. **PowerShell ISE** で、*…\\Learning Modules\\Schema Management\\Demo-SchemaManagement.ps1* を開きます。
1. **F5** キーを押して、スクリプトを実行します。

*Demo-SchemaManagement.ps1* スクリプトは *Deploy-SchemaManagement.ps1* スクリプトを呼び出して、カタログ サーバーに **jobaccount** という名前の *S2* データベースを作成します。 次に、スクリプトはジョブ アカウントを作成します。これにより jobaccount データベースがパラメーターとしてジョブ アカウント作成呼び出しに渡されます。

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>新しい参照データをすべてのテナントにデプロイするジョブの作成

各テナント データベースには、会場でホストされているイベントの種類を定義するテーブル **VenueTypes** の会場タイプ セットが含まれています。 この演習では、すべてのデータベースに更新プログラムをデプロイして、次の 2 つの会場タイプを追加します: *Motorcycle Racing*、*Swimming Club*。 これらの会場タイプは、テナント イベント アプリに表示される背景画像に対応しています。

[会場のタイプ] ドロップダウン メニューをクリックし、10 個の会場タイプ オプションのみが利用可能であること、また Motorcycle Racing と Swimming Club が一覧に含まれていないことを確認します。

それでは、すべてのテナント データベースの **VenueTypes** テーブルを更新するジョブを作成し、新しい会場タイプを追加してみましょう。

新しいジョブを作成するには、ジョブ アカウントが作成されたときに jobaccount データベースに作成された一連のジョブ システム ストアド プロシージャを使用します。

1. SSMS で、テナント サーバー tenants1-\<user\>.database.windows.net に接続します。
2. *tenants1-mt-\<user\>.database.windows.net* サーバーで *tenants1* データベースを参照し、*VenueTypes* テーブルにクエリを実行して *Motorcycle Racing* および *Swimming Club* が結果の一覧に**含まれていない**ことを確認します。
3. カタログ サーバー catalog-mt-\<user\>.database.windows.net に接続します。
4. カタログ サーバーの jobaccount データベースに接続します。
5. SSMS で、ファイル …\\Learning Modules\\Schema Management\\DeployReferenceData.sql を開きます。
6. ステートメント set @User = &lt;user&gt; を変更し、Wingtip Tickets SaaS Multi-tenant Database アプリケーションをデプロイしたときに使用した User の値に置き換えます。
7. **F5** キーを押して、スクリプトを実行します。

    * **sp\_add\_target\_group** は、ターゲット グループ名 DemoServerGroup を作成します。次に、ターゲット メンバーをグループに追加します。
    * **sp\_add\_target\_group\_member** は、*server* ターゲット メンバー タイプを追加します。これがジョブに含まれると、ジョブの実行時に、そのサーバー (テナント データベースを含む tenants1-mt-&lt;user&gt; サーバー) 内のすべてのデータベースがジョブに含まれます。次に、"ゴールデン" データベース (basetenantdb) の *database* ターゲット メンバー タイプを追加します。これは、catalog-mt-&lt;user&gt; サーバーにあります。最後に、*database* ターゲット グループ メンバー タイプを追加して、後のチュートリアルで使用される adhocreporting データベースを含めます。
    * **sp\_add\_job** は、"Reference Data Deployment" というジョブを作成します。
    * **sp\_add\_jobstep** は、T-SQL コマンド テキストを含むジョブ ステップを作成して、参照テーブル VenueTypes を更新します。
    * スクリプトの残りのビューは、オブジェクトの存在を表示し、ジョブの実行を監視します。 これらのクエリを使用して **lifecycle** 列の状態値を調べ、テナント データベースおよび参照テーブルを含むその他 2 つのデータベースで、ジョブがいつ正常に終了したかを確認します。

1. SSMS で、*tenants1-mt-&lt;user&gt;* サーバーでテナント データベースを参照し、*VenueTypes* テーブルにクエリを実行して *Motorcycle Racing* および *Swimming Club* がテーブルに**追加されている*ことを確認します。


## <a name="create-a-job-to-manage-the-reference-table-index"></a>参照テーブルのインデックスを管理するジョブの作成

前の演習と同様に、この演習では参照テーブルの主キーにインデックスを再構築するジョブを作成します。これは、テーブルへの大量データ ロードの後に管理者が実行する可能性のある一般的なデータベース管理操作です。


1. SSMS で、catalog-mt-&lt;User&gt;.database.windows.net サーバーの jobaccount データベースに接続します。
2. SSMS で、…\\Learning Modules\\Schema Management\\OnlineReindex.sql を開きます。
3. **F5** キーを押して、スクリプトを実行します。

    * **sp\_add\_job** は、"Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885" という新しいジョブを作成します。
    * **sp\_add\_jobstep** は、T-SQL コマンド テキストを含むジョブ ステップを作成して、インデックスを更新します。
    * スクリプトの残りのビューは、ジョブの実行を監視します。 これらのクエリを使用して **lifecycle** 列の状態値を調べ、すべてのターゲット グループ メンバーで、ジョブがいつ正常に終了したかを確認します。

## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]

> * 複数のテナントにわたってクエリを実行するジョブ アカウントを作成する
> * すべてのテナント データベースでデータを更新する
> * すべてのテナント データベース内のテーブルにインデックスを作成する

[アドホック分析の実行のチュートリアル](saas-multitenantdb-adhoc-reporting.md)


## <a name="additional-resources"></a>その他のリソース

<!--* Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [スケールアウトされたクラウド データベースの管理](sql-database-elastic-jobs-overview.md)
* [スケールアウトしたクラウド データベースの作成と管理](sql-database-elastic-jobs-create-and-manage.md)
