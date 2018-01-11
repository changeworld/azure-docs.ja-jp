---
title: "テナント単位データベース SaaS チュートリアル - Azure SQL Database | Microsoft Docs"
description: "Azure SQL Database を使用して Database per Tenant およびその他の SaaS パターンを示す、Wingtip Tickets SaaS マルチ テナント アプリケーションをデプロイおよび操作します。"
keywords: "SQL データベース チュートリアル"
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: genemi
ms.openlocfilehash: 5342b5290fab9826a2b38cd7ada63a6736c77601
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2017
---
# <a name="deploy-and-explore-a-multi-tenant-saas-application-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Azure SQL Database によるテナント単位データベース パターンを使用するマルチテナント SaaS アプリケーションのデプロイと操作

このチュートリアルでは、Wingtip Tickets SaaS "*テナント単位データベース*" アプリケーション (Wingtip) をデプロイして操作します。 このアプリでは、複数のテナントのデータを保存するために、テナント単位データベース パターンが使用されます。 このアプリは、各種 SaaS シナリオの実現を簡素化する Azure SQL Database の機能を紹介することを目的としています。

**[Azure にデプロイ]** という青いボタンをクリックすると、5 分後にマルチテナント SaaS アプリケーションが作成されます。 アプリには、Microsoft Azure クラウドで実行されている Azure SQL Database が含まれています。 アプリは、それぞれ独自のデータベースを使用する 3 つのサンプル テナントと共にデプロイされます。 すべてのデータベースが 1 つの SQL "*エラスティック プール*" にデプロイされます。 アプリは Azure サブスクリプションにデプロイされます。 ユーザーには、アプリの個々のコンポーネントを確認して操作するフル アクセス権があります。 アプリケーションの C# ソース コードと管理スクリプトは、[WingtipTicketsSaaS-DbPerTenant GitHub リポジトリ][github-wingtip-dpt]で入手できます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> - Wingtip SaaS アプリケーションをデプロイする方法。
> - アプリケーションのソース コード、および管理スクリプトを取得する場所。
> - アプリを構成するサーバー、プール、データベースについて。
> - *カタログ*によるテナントとデータのマッピング方法。
> - 新しいテナントのプロビジョニング方法。
> - アプリでテナントのアクティビティを監視する方法。

SaaS の設計と管理のさまざまなパターンを確認するために、[一連の関連チュートリアル](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)が用意されています。 各チュートリアルは、この初期デプロイを基に作成されています。
これらのチュートリアルを使用すると、用意されているスクリプトを調べて、さまざまな SaaS パターンの実装方法を確認できます。
各スクリプトは、SQL Database の機能によって、SaaS アプリケーションの開発がどのように簡素化されるのかを示しています。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。

* Azure PowerShell がインストールされている。 詳細については、「[Azure PowerShell を使ってみる](https://docs.microsoft.com/powershell/azure/get-started-azureps)」をご覧ください。

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Wingtip Tickets SaaS アプリケーションのデプロイ

次の手順でアプリケーションをデプロイします。

1. 次のパラメーターに必要な値を選択し、それらの値を覚えておきます。

    - **[ユーザー]**: 自分のイニシャル + 数字のような短い値を選択します  (例: *af1*)。 [ユーザー] パラメーターには、文字、数字、ハイフンのみを含めることができます (スペースは使用できません)。 先頭と末尾の文字は、文字または数字にする必要があります。 すべて小文字にすることをお勧めします。
    - **[リソース グループ]**: Wingtip アプリケーションをデプロイするたびに、新しいリソース グループに別の一意の名前を選択する必要があります。 リソース グループのベース名にユーザー名を付加することをお勧めします。 たとえば、*wingtip-af1* というリソース グループ名にします。 この場合も、すべて小文字にすることをお勧めします。

2. 青い **[Azure にデプロイ]** ボタンをクリックして、Azure Portal で Wingtip Tickets SaaS Database per Tenant デプロイ テンプレートを開きます。

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

3. テンプレートに必要なパラメーターの値を入力します。

    > [!IMPORTANT]
    > 一部の認証とサーバー ファイアウォールは、デモンストレーションのために、意図的に保護されていません。 "*新しいリソース グループを作成する*" ことをお勧めします。 既存のリソース グループ、サーバー、またはプールを使用しないでください。 運用環境には、このアプリケーション、スクリプト、またはデプロイされたリソースを使用しないでください。 関連する課金を停止するために、サンプル アプリケーションの操作が終了したら、このリソース グループを削除してください。

    - **[リソース グループ]** - **[新規作成]** を選択し、先ほど選択したリソース グループの一意の**名前**を指定します。 
    - **[場所]** - ドロップダウン から **[場所]** を選択します。
    - **[ユーザー]** - 先ほど選択したユーザー名の値を使用します。

4. アプリケーションをデプロイします。

    - 使用条件に同意したら、チェック ボックスをオンにします。
    - **[購入]** をクリックします。

5. **[通知]** (検索ボックスの右にあるベル アイコン) をクリックして、デプロイの状態を監視します。 Wingtip Tickets SaaS アプリのデプロイには約 5 分かかります。

   ![デプロイメントに成功しました](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Wingtip Tickets 管理スクリプトのダウンロードとブロック解除

アプリケーションをデプロイ中に、ソース コードと管理スクリプトをダウンロードします。

> [!IMPORTANT]
> .zip ファイルを外部ソースからダウンロードして抽出すると、実行可能なコンテンツ (スクリプト、DLL) が Windows によってブロックされる場合があります。 zip ファイルからスクリプトを抽出するとき、以下の手順を実行して、.zip ファイルをブロック解除してから抽出します。 ブロック解除により、スクリプトの実行が許可されます。

1. [WingtipTicketsSaaS-DbPerTenant GitHub リポジトリ][github-wingtip-dpt]を参照します。
2. **[複製またはダウンロード]** をクリックします。
3. **[Download ZIP]** をクリックし、ファイルを保存します。
4. **WingtipTicketsSaaS-DbPerTenant-master.zip** ファイルを右クリックし、**[プロパティ]** を選択します。
5. **[全般]** タブで **[ブロックの解除]** を選択し、**[適用]** をクリックします。
6. **[OK]**をクリックします。
7. ファイルを解凍します。

スクリプトは、*..\\WingtipTicketsSaaS-DbPerTenant-master\\Learning Modules* フォルダーにあります。

## <a name="update-the-user-configuration-file-for-this-deployment"></a>このデプロイのユーザー構成ファイルの更新

スクリプトを実行する前に、**UserConfig.psm1** で*リソース グループ*と*ユーザー*の値を更新します。 これらの変数は、デプロイ中に使用した値に設定します。

1. *PowerShell ISE* で、...\\Learning Modules\\*UserConfig.psm1* を開きます 
2. *ResourceGroupName*と*Name*をデプロイの固有の値で更新します (10 行目および 11 行目のみ)。
3. 変更を保存します。

これらの値は、ほぼすべてスクリプトで参照されます。

## <a name="run-the-application"></a>アプリケーションの実行

アプリは、イベントを開催する会場の窓口の機能を提供します。 会場の種類には、コンサート ホール、ジャズ クラブ、スポーツ クラブなどがあります。 Wingtip Tickets アプリでは、会場がテナントとして登録されます。 テナントになることで、会場が顧客に対して、イベントの一覧表示やチケットの販売を簡単に行うことができます。 各会場が、イベントの表示やチケット販売を行うためのパーソナライズされた Web サイトを利用できます。

アプリの内部では、テナントごとに SQL Database が作成され、1 つの SQL エラスティック プールにデプロイされます。

中央の **Events Hub** ページには、デプロイのテナントへのリンク一覧が表示されます。

1. Web ブラウザーで http://events.wingtip-dpt.&lt;ユーザー&gt;.trafficmanager.net と指定し、*Events Hub* を開きます (&lt;ユーザー&gt; は実際のデプロイ ユーザーの値に置き換えてください)。

    ![Events Hub](media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. *Events Hub* で **[Fabrikam Jazz Club]** をクリックします。

    ![イベント](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

#### <a name="azure-traffic-manager"></a>Azure の Traffic Manager

Wingtip アプリケーションでは、[*Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md) を使用して受信要求の分散を制御します。 1 つのテナントのイベント ハブにアクセスするための URL は、次の形式に従う必要があります。

- http://events.wingtip-dpt.&lt;ユーザー&gt;.trafficmanager.net/fabrikamjazzclub

上記の形式の各部分の説明を次の表に示します。

| URL の部分 | 説明 |
| :------- | :---------- |
| http://events.wingtip-dpt | Wingtip アプリのイベントの部分。<br /><br />***-dpt*** 部分によって、Wingtip の*テナント単位データベース*実装が、若干異なる他の実装と区別されます。 たとえば、他のドキュメント記事では、"*スタンドアロン*" (*-sa*) または "*マルチテナント DB*" の Wingtip について説明しています。 |
| .*&lt;ユーザー&gt;* | この例では *af1*。 |
| .trafficmanager.net/ | Azure Traffic Manager、ベース URL。 |
| fabrikamjazzclub | *Fabrikam Jazz Club* という名前のテナント。 |
| &nbsp; | &nbsp; |

1. イベント アプリによって、URL からテナント名が解析されます。
2. テナント名を使用してキーが作成されます。
3. テナントのデータベースの場所を取得するために、このキーを使用してカタログにアクセスします。
    - カタログは、"*シャード マップ管理*" を使用して実装されています。
4. *Events Hub* は、カタログ内の拡張メタデータを使用してイベント URL の一覧を取得します。

運用環境では、通常、[*会社のインターネット ドメインが Traffic Manager プロファイルを参照*](../traffic-manager/traffic-manager-point-internet-domain.md)するように CNAME DNS レコードを作成します。

## <a name="start-generating-load-on-the-tenant-databases"></a>テナント データベースに対する負荷の生成

これで、アプリがデプロイされました。作業を開始しましょう。
*Demo-LoadGenerator* PowerShell スクリプトは、すべてのテナント データベースに対して実行されるワークロードを開始します。
多くの SaaS アプリの実際の負荷は散発的で予測不能です。
この種の負荷をシミュレートするために、ジェネレーターは各テナントでのアクティビティのランダムなスパイクまたはバーストによる負荷を生成します。
バーストはランダムな間隔で発生します。
負荷パターンの生成には数分かかります。 そのため、少なくとも 3 ～ 4 分間ジェネレーターを実行してから負荷を監視することをお勧めします。

1. *PowerShell ISE* で、...\\Learning Modules\\Utilities\\*Demo-LoadGenerator.ps1* スクリプトを開きます。
2. **F5** キーを押してスクリプトを実行し、ロード ジェネレーターを起動します  (現時点では、既定のパラメーター値をそのまま使用します)。

 *Demo-LoadGenerator.ps1* の再実行以外のことに、同じ PowerShell ISE インスタンスを再利用しないでください。 他の PowerShell スクリプトを実行する必要がある場合は、別の PowerShell ISE を起動します。

#### <a name="rerun-with-different-parameters"></a>別のパラメーターを使用して再実行する

別のパラメーターを使用してワークロード テストを再実行する場合は、次の手順に従います。

1. *LoadGenerator.ps1* を停止します。
    - **Ctrl + C** キーを使用するか、**[停止]** ボタンをクリックします。
    - この停止により、現在実行中の未完了のバックグラウンド ジョブが停止されたり、影響を受けたりすることはありません。

2. *Demo-LoadGenerator.ps1* を再実行します。
    - この再実行により、*sp_CpuLoadGenerator* を引き続き実行しているすべてのバックグラウンド ジョブがまず停止されます。

PowerShell ISE インスタンスを終了することもできます。これにより、すべてのバックグラウンド ジョブが停止します。 その後、PowerShell ISE の新しいインスタンスを起動し、*Demo-LoadGenerator.ps1* を再実行します。

#### <a name="monitor-the-background-jobs"></a>バックグラウンド ジョブを監視する

バックグラウンド ジョブを制御および監視する場合は、次のコマンドレットを使用します。

- Get-Job
- Receive-Job
- Stop-Job

#### <a name="demo-loadgeneratorps1-actions"></a>Demo-LoadGenerator.ps1 のアクション

*Demo-LoadGenerator.ps1* は、顧客トランザクションのアクティブなワークロードを模倣します。 次の手順は、*Demo-LoadGenerator.ps1* で開始される一連のアクションを示しています。

1. *Demo-LoadGenerator.ps1* は、フォアグラウンドで *LoadGenerator.ps1* を起動します。
    - これらの.ps1 ファイルはいずれも、*Learning Modules\\Utilities\\* のフォルダーに格納されています。

2. *LoadGenerator.ps1* は、カタログに登録されているすべてのテナント データベースをループ処理します。

3. テナント データベースごとに、*LoadGenerator.ps1* が *sp_CpuLoadGenerator* という名前の Transact-SQL ストアド プロシージャの実行を開始します。
    - この実行は、*Invoke-SqlAzureWithRetry* コマンドレットを呼び出して、バックグラウンドで開始されます。
    - *sp_CpuLoadGenerator* は、既定の期間である 60 秒間、SQL SELECT ステートメントをループ処理します。 SELECT の発行間隔は、パラメーター値によって異なります。
    - この .sql ファイルは、*WingtipTenantDB\\dbo\\StoredProcedures\\* に格納されています。

4. テナント データベースごとに、*LoadGenerator.ps1* が *Start-Job* コマンドレットも開始します。
    - *Start-Job* は、チケット販売のワークロードを模倣します。

5. *LoadGenerator.ps1* が引き続き実行され、プロビジョニングされた新しいテナントが監視されます。

&nbsp;

次のセクションに進む前に、ロード ジェネレーターをジョブ呼び出し状態で実行したままにしておきます。

## <a name="provision-a-new-tenant"></a>新しいテナントのプロビジョニング

初期デプロイでは 3 つのサンプル テナントが作成されます。 ここでは、別のテナントを作成して、デプロイ済みのアプリケーションへの影響を確認します。 Wingtip アプリで新しいテナントをプロビジョニングするワークフローについては、[プロビジョニングとカタログに関するチュートリアル](saas-dbpertenant-provision-and-catalog.md)をご覧ください。 このフェーズでは、1 分足らずで新しいテナントを作成します。

1. *PowerShell ISE* で、...\\Learning Modules\Provision and Catalog\\*Demo-ProvisionAndCatalog.ps1* を開きます。
2. **F5** キーを押して、スクリプトを実行します。 (現時点では、既定値をそのまま使用します。)

   > [!NOTE]
   > 多くの Wingtip SaaS スクリプトは、*$PSScriptRoot* を使用してフォルダーを移動し、他のスクリプト内の関数を呼び出します。 この変数は **F5** キーを押してスクリプト全体が実行されたときにのみ評価されます。  **F8** キーで選択項目を強調表示して実行すると、エラーが発生する可能性があります。 そのため、**F5** キーを押してスクリプトを実行してください。

新しいテナント データベースは次のように処理されます。

- SQL エラスティック プールに作成される。
- 初期化される。
- カタログに登録される。

プロビジョニングが正常に完了すると、新しいテナントの *Events* サイトがブラウザーに表示されます。

![新しいテナント](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

*Events Hub* を更新すると、新しいテナントが一覧に表示されます。

## <a name="explore-the-servers-pools-and-tenant-databases"></a>サーバー、プール、テナント データベースを操作する

ここまでで、テナントのコレクションに対する負荷の実行を開始できました。続いては、デプロイされたリソースをいくつか見てみましょう。

1. [Azure Portal](http://portal.azure.com) で、SQL サーバーの一覧を参照し、**catalog-dpt-&lt;ユーザー&gt;** サーバーを開きます。
    - カタログ サーバーには 2 つのデータベースが含まれます。**tenantcatalog** と **basetenantdb** (新しいテナントを作成するためにコピーされたテンプレート データベース) です。

   ![データベース](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. SQL サーバーの一覧に戻ります。

3. テナント データベースを保持している **tenants1-dpt-&lt;ユーザー&gt;** サーバーを開きます。

4. 次の項目を確認します。
    - 各テナント データベースは*エラスティック標準*データベースで、eDTU が 50 の Standard プールにあります。
    - *Red Maple Racing* データベース。これは、以前にプロビジョニングしたテナント データベースです。

   ![server](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>プールの監視

*LoadGenerator.ps1* を数分間実行したら、十分な量のデータが生成されています。このデータを使って、一部の監視機能を見てみましょう。 これらの機能は、プールとデータベースに組み込まれています。

**tenants1-dpt-&lt;ユーザー&gt;** サーバーを参照し、**Pool1** をクリックして、プールのリソース使用率を表示します。 次のグラフでは、ロード ジェネレーターを 1 時間実行しました。

   ![プールの監視](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- **Resource utilization** というラベルが付いた最初のグラフは、プールの eDTU 使用率を示しています。
- 2 番目のグラフは、プール内の上位 5 つのデータベースの eDTU 使用率を示しています。

この 2 つのグラフは、エラスティック プールと SQL Database が SaaS アプリケーション ワークロードに適していることを示しています。
これらのグラフは、4 つのデータベースがそれぞれ 40 eDTU までバーストしても、50 eDTU プールで問題なくサポートされていることを示しています。 50 eDTU プールは、さらに重いワークロードをサポートできます。
スタンドアロン データベースとしてプロビジョニングされた場合、バーストをサポートするためにそれぞれが S2 (50 DTU) 必要になります。
4 つのスタンドアロン S2 データベースのコストは、プールの価格の約 3 倍になります。
実際の環境では、現時点で、SQL Database のお客様用に最大 500 のデータベースが 200 eDTU のプールで実行されています。
詳細については、[パフォーマンスの監視](saas-dbpertenant-performance-monitoring.md)に関するチュートリアルをご覧ください。

## <a name="additional-resources"></a>その他のリソース

- [Wingtip Tickets SaaS Database Per Tenant アプリケーションに基づく作業のための追加のチュートリアル](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
- エラスティック プールの詳細については、[*Azure SQL エラスティック プール*](sql-database-elastic-pool.md)に関するページをご覧ください。
- エラスティック ジョブの詳細については、「[*スケールアウトされたクラウド データベースの管理*](sql-database-elastic-jobs-overview.md)」をご覧ください。
- マルチテナント SaaS アプリケーションの詳細については、[*マルチテナント SaaS アプリケーションの設計パターン*](saas-tenancy-app-design-patterns.md)に関するページをご覧ください。


## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> - Wingtip Tickets SaaS アプリケーションのデプロイ方法
> - アプリを構成するサーバー、プール、データベースについて
> - *カタログ*によるテナントとデータのマッピング
> - 新しいテナントのプロビジョニング方法
> - プール使用率を表示してテナント アクティビティを監視する方法
> - サンプル リソースを削除して、関連する課金を停止する方法

次に、[プロビジョニングとカタログに関するチュートリアル](saas-dbpertenant-provision-and-catalog.md)をお試しください。



<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant 

