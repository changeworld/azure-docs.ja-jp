---
title: "マルチテナント アプリで Azure SQL Database スキーマを管理する | Microsoft Docs"
description: "Azure SQL Database を使用するマルチテナント アプリケーションで複数のテナントのスキーマを管理します"
keywords: "SQL データベース チュートリアル"
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg
editor: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.reviewers: billgib
ms.author: genemi
ms.openlocfilehash: 135764a7d89dcf711ff7fe9416850f1af9329479
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2018
---
# <a name="manage-schema-for-multiple-tenants-in-a-multi-tenant-application-that-uses-azure-sql-database"></a>Azure SQL Database を使用するマルチテナント アプリケーションで複数のテナントのスキーマを管理します

このチュートリアルでは、クラウドのサービスとしてのソフトウェア (SaaS) アプリケーションで、規模が大きくなる可能性のあるデータベース フリートを保持する際の課題について考察します。 アプリケーションの有効期間中に開発および実装される、スキーマ拡張機能を管理するためのソリューションを示します。

アプリケーションが進化するにつれて、アプリケーションのテーブル列やその他のスキーマ、参照データ、またはパフォーマンス関連項目に対する変更が生じる可能性があります。 SaaS アプリケーションでは、これらの変更を、既存の多数のテナント データベース全体に同等にデプロイする必要があります。 また、これらの変更を、将来アプリケーションに追加されるテナント データベースに含める必要もあります。 したがって、変更を新しいデータベースのプロビジョニング プロセスに組み込む必要があります。

#### <a name="two-scenarios"></a>2 つのシナリオ

このチュートリアルでは、次の 2 つのシナリオを考察します。
- 参照データの更新をすべてのテナントにわたってデプロイする。
- 参照データを含むテーブルのインデックスを再調整する。

これらの操作を複数のテナント データベースにわたって実行するために、Azure SQL Database の[エラスティック ジョブ](sql-database-elastic-jobs-overview.md)機能を使用します。 このジョブは、テナント データベースのゴールデン テンプレートに対しても機能します。 このテンプレートは、新しいデータベースがプロビジョニングされるときに使用されます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * ジョブ アカウントを作成する。
> * 複数のテナントにわたってクエリを実行する。
> * すべてのテナント データベースでデータを更新する。
> * すべてのテナント データベース内のテーブルにインデックスを作成する。

## <a name="prerequisites"></a>前提条件

- Wingtip Tickets アプリがデプロイ済みである必要があります。
    - 手順については、*Wingtip Tickets* SaaS マルチテナント データベース アプリケーションについて説明している最初のチュートリアル、<br />「[Azure SQL Database を使用するシャード化されたマルチテナント アプリケーションのデプロイと操作](saas-multitenantdb-get-started-deploy.md)」をご覧ください。
        - デプロイ プロセスは 5 分未満で実行されます。
    - *シャード化されたマルチテナント* バージョンの Wingtip をインストール済みである必要があります。 *スタンドアロン*および*テナントごとのデータベース* バージョンでは、このチュートリアルはサポートされません。

- 最新バージョンの SQL Server Management Studio (SSMS) がインストールされている必要があります。 「[SSMS のダウンロードとインストール](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)」をご覧ください。

- Azure PowerShell がインストールされている必要があります。 詳細については、「[Azure PowerShell を使ってみる](https://docs.microsoft.com/powershell/azure/get-started-azureps)」をご覧ください。

> [!NOTE]
> このチュートリアルでは、限定プレビューに含まれる Azure SQL Database サービスの機能 ([Elastic Database ジョブ](sql-database-elastic-database-client-library.md)) を使用します。 このチュートリアルを実行する場合、サブスクリプション ID を *SaaSFeedback@microsoft.com* までお送りください (件名: Elastic Jobs Preview)。 サブスクリプションが有効であることを通知するメールが届いたら、[最新のプレリリース ジョブ コマンドレットをダウンロードしてインストール](https://github.com/jaredmoo/azure-powershell/releases)します。 このプレビューは限定的であるため、関連する質問やサポートについては、*SaaSFeedback@microsoft.com* にお問い合わせください。

## <a name="introduction-to-saas-schema-management-patterns"></a>SaaS スキーマ管理パターンの概要

このサンプルで使用されているシャード化されたマルチテナント データベース モデルでは、テナント データベースに 1 つまたは複数のテナントを含めることができます。 このサンプルでは、複数テナントのデータベースと 1 テナント のデータベースを組み合わせて*ハイブリッド* テナント管理モデルを実現する可能性を探ります。 これらのデータベースの管理は複雑です。 [エラスティック ジョブ](sql-database-elastic-jobs-overview.md)を使用すると、SQL データ層の管理が容易になります。 ジョブにより、テナント データベースのグループに対して、Transact-SQL スクリプトをタスクとして安全かつ確実に実行できます。 このタスクはユーザーの操作や入力に依存しません。 この方法を使用して、スキーマや共通の参照データに対する変更を、アプリケーションのすべてのテナントにわたってデプロイできます。 エラスティック ジョブは、データベースのゴールデン テンプレートのコピーを保持するためにも使用できます。 このテンプレートは新しいテナントの作成に使用され、常に最新のスキーマと参照データが使用されるようにします。

![スクリーン](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>エラスティック ジョブの限定プレビュー

Azure SQL Database の統合機能となった新しいバージョンのエラスティック ジョブが入手可能です。 統合機能とは、追加のサービスやコンポーネントが不要であるという意味です。 現在、この新しいバージョンのエラスティック ジョブは限定プレビュー中です。 現在、この限定プレビューでは、ジョブ アカウントを作成するための PowerShell と、ジョブを作成および管理するための T-SQL がサポートされています。

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Wingtip Tickets SaaS マルチテナント データベース アプリケーションのソース コードとスクリプトを入手する

Wingtip Tickets SaaS マルチテナント データベースのスクリプトとアプリケーション ソース コードは、GitHub の [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) リポジトリで入手できます。 Wingtip Tickets SaaS のスクリプトをダウンロードしてブロックを解除する手順については、[一般的なガイダンス](saas-tenancy-wingtip-app-guidance-tips.md)に関する記事をご覧ください。 

## <a name="create-a-job-account-database-and-new-job-account"></a>ジョブ アカウント データベースと新しいジョブ アカウントの作成

このチュートリアルでは、PowerShell を使用してジョブ アカウント データベースとジョブ アカウントを作成する必要があります。 SQL エージェントで使用される MSDB と同様に、エラスティック ジョブは Azure SQL データベースを使用してジョブ定義、ジョブの状態、履歴を格納します。 ジョブ アカウントの作成後は、すぐにジョブを作成および監視することができます。

1. **PowerShell ISE** で、*…\\Learning Modules\\Schema Management\\Demo-SchemaManagement.ps1* を開きます。
2. **F5** キーを押して、スクリプトを実行します。

*Demo-SchemaManagement.ps1* スクリプトは *Deploy-SchemaManagement.ps1* スクリプトを呼び出して、カタログ サーバーに **jobaccount** という名前の *S2* 階層データベースを作成します。 次に、スクリプトは、jobaccount データベースをジョブ アカウント作成呼び出しのパラメーターとして渡して、ジョブ アカウントを作成します。

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>新しい参照データをすべてのテナントにデプロイするジョブの作成

#### <a name="prepare"></a>準備

各テナント データベースの **VenueTypes** テーブルには、一連の会場タイプが含まれています。 会場タイプは、会場で催されるイベントの種類を定義します。 この演習では、すべてのデータベースに更新をデプロイして、*Motorcycle Racing* と *Swimming Club* の 2 つの会場タイプを追加します。 これらの会場タイプは、テナント イベント アプリに表示される背景画像に対応しています。

新しい参照データをデプロイする前に、既に存在する会場タイプの数を確認してください。会場タイプの数は 10 個のはずです。 次の Wingtip web UI へのリンクをクリックし、**[会場タイプ]** ドロップダウン メニューをクリックして確認します。
- http://events.wingtip-mt.<USER>.trafficmanager.net

これで、元の会場タイプの数を調べることができます。 特に、*Motorcycle Racing* と *Swimming Club* がいずれも存在していないことを確認してください。

#### <a name="steps"></a>手順

各テナント データベースの **VenueTypes** テーブルに 2 つの新しい会場タイプを追加してテーブルを更新するジョブを作成します。

新しいジョブを作成するには、*jobaccount* データベースに作成されている一連のジョブ システム ストアド プロシージャを使用します。 これは、ジョブ アカウントの作成時に作成されたプロシージャです。

1. SSMS で、テナント サーバー tenants1-\<user\>.database.windows.net に接続します。

2. *tenants1-mt-\<user\>.database.windows.net* サーバーの *tenants1* データベースを参照します。

3. *VenueTypes* テーブルのクエリを実行して、*Motorcycle Racing* と *Swimming Club* が結果の一覧に含まれていないことを確認します。

4. カタログ サーバー *catalog-mt-\<user\>.database.windows.net* に接続します。

5. カタログ サーバーの *jobaccount* データベースに接続します。

6. SSMS で、ファイル *...\\Learning Modules\\Schema Management\\DeployReferenceData.sql* を開きます。

7. ステートメント set @User = &lt;user&gt; を変更し、Wingtip Tickets SaaS Multi-tenant Database アプリケーションをデプロイしたときに使用した User の値に置き換えます。

8. **F5** キーを押して、スクリプトを実行します。

#### <a name="observe"></a>確認

*DeployReferenceData.sql* スクリプトで、次の項目を確認します。

- **sp\_add\_target\_group** は、ターゲット グループ名 *DemoServerGroup* を作成し、ターゲット メンバーをグループに追加します。

- **sp\_add\_target\_group\_member** は、次の項目を追加します。
    - *server* ターゲット メンバー タイプ。
        - これは、テナント データベースを含む *tenants1-mt-&lt;user&gt;* サーバーです。
        - したがって、ジョブを実行すると、サーバー内のすべてのデータベースがジョブに含まれます。
    - *catalog-mt-&lt;user&gt;* サーバーにある、ゴールデン データベース (*basetenantdb*) の *database* ターゲット メンバー タイプ。
    - 後のチュートリアルで使用する *adhocreporting* データベースを含めるための *database* ターゲット メンバー タイプ。

- **sp\_add\_job** は、*Reference Data Deployment* というジョブを作成します。

- **sp\_add\_jobstep** は、T-SQL コマンド テキストを含むジョブ ステップを作成して、参照テーブル VenueTypes を更新します。

- スクリプトの残りのビューは、オブジェクトの存在を表示し、ジョブの実行を監視します。 これらのクエリを使用して **lifecycle** 列の状態値を調べ、ジョブがいつ正常に終了したかを確認します。 ジョブは、テナント データベースを更新し、参照テーブルを含む 2 つの追加のデータベースを更新します。

SSMS で、*tenants1-mt-&lt;user&gt;* サーバーのテナント データベースを参照します。 *VenueTypes* テーブルのクエリを実行して、*Motorcycle Racing* と *Swimming Club* がテーブルに追加されたことを確認します。 会場タイプの総数が 2 つ増えているはずです。

## <a name="create-a-job-to-manage-the-reference-table-index"></a>参照テーブルのインデックスを管理するジョブの作成

この演習は、前の演習と似ています。 この演習では、参照テーブルの主キーにインデックスを再構築するジョブを作成します。 インデックスの再構築は、管理者がテーブルに大量のデータを読み込んだ後でパフォーマンス向上のために実行することが多いデータベース管理操作です。

1. SSMS で、*catalog-mt-&lt;User&gt;.database.windows.net* サーバーの *jobaccount* データベースに接続します。

2. SSMS で、*...\\Learning Modules\\Schema Management\\OnlineReindex.sql* を開きます。

3. **F5** キーを押して、スクリプトを実行します。

#### <a name="observe"></a>確認

*OnlineReindex.sql* スクリプトで次の項目を確認します。

* **sp\_add\_job** は、*Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885* という新しいジョブを作成します。

* **sp\_add\_jobstep** は、T-SQL コマンド テキストを含むジョブ ステップを作成して、インデックスを更新します。

* スクリプトの残りのビューは、ジョブの実行を監視します。 これらのクエリを使用して **lifecycle** 列の状態値を調べ、すべてのターゲット グループ メンバーでジョブがいつ正常に終了したかを確認します。

## <a name="additional-resources"></a>その他のリソース

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [スケールアウトされたクラウド データベースの管理](sql-database-elastic-jobs-overview.md)
* [スケールアウトしたクラウド データベースの作成と管理](sql-database-elastic-jobs-create-and-manage.md)

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> - 複数のテナントにわたってクエリを実行するジョブ アカウントを作成する。
> - すべてのテナント データベースでデータを更新する。
> - すべてのテナント データベース内のテーブルにインデックスを作成する。

続いて、[アドホック レポートに関するチュートリアル](saas-multitenantdb-adhoc-reporting.md)をお試しください。

