---
title: "マルチテナント SaaS チュートリアル - Azure SQL Database | Microsoft Docs"
description: "Azure SQL Database を使用して Database per Tenant およびその他の SaaS パターンを示す、Wingtip Tickets SaaS マルチ テナント アプリケーションをデプロイおよび操作します。"
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
ms.date: 11/10/2017
ms.author: sstein
ms.openlocfilehash: f91ddff81e51e7cc3d1561dc799013764530924b
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/28/2017
---
# <a name="deploy-and-explore-a-multi-tenant-saas-application-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Azure SQL Database によるテナント単位データベース パターンを使用するマルチテナント SaaS アプリケーションのデプロイと操作

このチュートリアルでは、Wingtip Tickets SaaS Database per Tenant アプリケーションをデプロイして操作します。 このアプリケーションでは、複数のテナントのデータを保存するために、テナント単位データベース パターンが使用されます。 このアプリケーションの目的は、各種 SaaS シナリオの有効化を簡素化する Azure SQL Database の機能を紹介することにあります。

下にある *[Azure に配置する]* ボタンをクリックすると、5 分後に、SQL Database を使用するマルチテナント SaaS アプリケーションがクラウド上に作成されて稼働します。 アプリケーションは 3 つのサンプル テナントと共にデプロイされます。各テナントには専用のデータベースが設定され、すべてが 1 つの SQL エラスティック プールにデプロイされます。 アプリはお使いの Azure サブスクリプションにデプロイされ、フル アクセス権が付与されるため、個々のアプリケーション コンポーネントを調べながら操作することができます。 アプリケーションのソース コードと管理スクリプトは、WingtipTicketsSaaS-DbPerTenant GitHub リポジトリから入手できます。


このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]

> * Wingtip SaaS アプリケーションをデプロイする方法
> * アプリケーションのソース コード、および管理スクリプトを取得する場所
> * アプリを構成するサーバー、プール、データベースについて
> * *カタログ*によるテナントとデータのマッピング方法
> * 新しいテナントのプロビジョニング方法
> * アプリでテナントのアクティビティを監視する方法

さまざまな SaaS の設計と管理のパターンについて理解を深めることができるように、この初期デプロイをベースにした[一連の関連チュートリアル](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)が用意されています。 これらのチュートリアルでは、用意されたスクリプトを詳しく確認し、さまざまな SaaS パターンの実装方法を学習します。 スクリプトを実行しながら各チュートリアルを進めると、SaaS アプリケーションの開発を簡素化する多様な SQL Database 機能の実装方法について深く理解することができます。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。

* Azure PowerShell がインストールされている。 詳しくは、「[Azure PowerShell を使ってみる](https://docs.microsoft.com/powershell/azure/get-started-azureps)」をご覧ください。

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Wingtip Tickets SaaS アプリケーションのデプロイ

次の手順でアプリケーションをデプロイします。

1. **[Azure にデプロイ]** ボタンをクリックすると、Azure ポータルで、Wingtip Tickets SaaS Database per Tenant デプロイ テンプレートが開きます。 テンプレートには、2 つのパラメーター値が必要です。1 つは、新しいリソース グループの名前です。もう 1 つは、このデプロイを Wingtip Tickets SaaS Database per Tenant アプリの他のデプロイと区別するためのユーザー名です。 次の手順では、これらの値を設定するための詳細を提供します。

   後で構成ファイルに入力する必要があるため、使用する正確な値をメモしてください。

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. デプロイに必須のパラメーター値を入力します。

    > [!IMPORTANT]
    > 一部の認証とサーバー ファイアウォールは、デモンストレーションのために、意図的に保護されていません。 **新しいリソース グループを作成してください**。 既存のリソース グループ、サーバー、またはプールを使用しないでください。 運用環境には、このアプリケーション、スクリプト、またはデプロイされたリソースを使用しないでください。 関連する課金を停止するために、サンプル アプリケーションの操作が終了したら、このリソース グループを削除してください。

    * **[リソース グループ]** - **[新規作成]** を選択して、リソース グループの **[名前]** を指定します。 
    * **[場所]** - ドロップダウン から **[場所]** を選択します。
    * **[ユーザー]** - グローバルに一意の名前が必要なリソースもあります。 一意性を確保するには、アプリケーションをデプロイするたびに、自分が作成するリソースと、Wingtip アプリケーションの別のデプロイで作成したリソースを区別する値を指定します。 自分のイニシャル + 数字 (例: *af1*) のような短い**ユーザー**名を作成し、それをリソース グループ名にも使用する (例: *wingtip-af1*) ことをお勧めします。 **[ユーザー]** パラメーターには、文字、数字、ハイフンのみを含めることができます (スペースは使用できません)。 先頭と末尾の文字は、英文字または数字にしてください (すべて小文字にすることをお勧めします)。


1. **アプリケーションをデプロイします**。

    * 使用条件に同意したら、チェック ボックスをオンにします。
    * **[購入]** をクリックします。

1. **[通知]** (検索ボックスの右にあるベル アイコン) をクリックして、デプロイ ステータスを監視します。 Wingtip Tickets SaaS アプリのデプロイには約 5 分かかります。

   ![デプロイメントに成功しました](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Wingtip Tickets 管理スクリプトのダウンロードとブロック解除

アプリケーションをデプロイ中に、ソース コードと管理スクリプトをダウンロードします。

> [!IMPORTANT]
> 実行可能なコンテンツ (スクリプト、dll) は、zip ファイルが外部ソースからダウンロードされ、抽出されると、Windows によってブロックされる場合があります。 zip ファイルからスクリプトを抽出するとき、以下の手順に従って .zip ファイルをブロック解除してから抽出します。 これにより、スクリプトの実行が許可されます。

1. [WingtipTicketsSaaS-DbPerTenant GitHub リポジトリ](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)を参照します。
1. **[複製またはダウンロード]** をクリックします。
1. **[Download ZIP]** をクリックし、ファイルを保存します。
1. **WingtipTicketsSaaS-DbPerTenant-master.zip** ファイルを右クリックし、**[プロパティ]** を選択します。
1. **[全般]** タブで **[ブロックの解除]** を選択し、**[適用]** をクリックします。
1. **[OK]**をクリックします。
1. ファイルを解凍します。

スクリプトは、*..\\WingtipTicketsSaaS-DbPerTenant-master\\Learning Modules* フォルダーにあります。

## <a name="update-the-user-configuration-file-for-this-deployment"></a>このデプロイのユーザー構成ファイルの更新

スクリプトを実行する前に、**UserConfig.psm1** で*リソース グループ*と*ユーザー*の値を更新します。 これらの変数は、デプロイ中に使用した値に設定します。

1. *PowerShell ISE* で、...\\Learning Modules\\*UserConfig.psm1* を開きます 
1. *ResourceGroupName*と*Name*をデプロイの固有の値で更新します (10 行目および 11 行目のみ)。
1. 変更を保存します。

これらの値は、ほぼすべてスクリプトで参照されます。

## <a name="run-the-application"></a>アプリケーションの実行

このアプリは、コンサート ホール、ジャズ クラブ、スポーツ クラブなど、イベントを開催する会場を対象にしたアプリです。 各会場は Wingtip チケットの顧客 (テナント) として登録され、イベントの一覧表示やチケットの販売を簡単に行うことができます。 会場ごとに、イベントの管理と表示やチケットの販売を行うための Web サイトが作成され、相互に独立して配置されます。 内部では、テナントごとに SQL Database が 1 つずつ作成され、単一の SQL エラスティック プールにデプロイされます。

中央の **Events Hub** ページには、デプロイのテナントへのリンク一覧が表示されます。

1. Web ブラウザーで http://events.wingtip-dpt.&lt;ユーザー&gt;.trafficmanager.net と指定し、_Events Hub_ を開きます (&lt;ユーザー&gt; は実際のデプロイ ユーザーの値に置き換えてください)。

    ![Events Hub](media/saas-dbpertenant-get-started-deploy/events-hub.png)

1. *Events Hub* で **[Fabrikam Jazz Club]** をクリックします。

   ![イベント](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)


アプリは、着信要求の分散を制御するために [*Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md) を使用します。 テナントに固有であるイベント ページは、URL にテナント名が含まれている必要があります。 すべてのテナント URL は、*[ユーザー]* で指定した値が含まれた、http://events.wingtipp-dpt.&lt;ユーザー&gt;.trafficmanager.net/*fabrikamjazzclub* という形式になっています。 イベント アプリは URL からテナント名を解析し、その名前を使って、"[*シャード マップ管理*](sql-database-elastic-scale-shard-map-management.md)" を使用して実装されたカタログにアクセスするためのキーを作成します。 このカタログによってキーがテナントのデータベースの場所にマップされます。 **Events Hub** は、カタログ内の拡張メタデータを使用して、各データベースに関連付けられたテナントの名前を取得し、URL の一覧を提供します。

運用環境では、通常、[*会社のインターネット ドメインで Traffic Manager プロファイルが参照されるように*](../traffic-manager/traffic-manager-point-internet-domain.md) CNAME DNS レコードを作成します。

## <a name="start-generating-load-on-the-tenant-databases"></a>テナント データベースに対する負荷の生成

これで、アプリがデプロイされました。作業を開始しましょう。 *Demo-LoadGenerator* PowerShell スクリプトは、すべてのテナント データベースに対して実行されるワークロードを開始します。 多くの SaaS アプリの実際の負荷は通常、散発的で予測不能です。 この種の負荷をシミュレートするために、ジェネレーターは、すべてのテナントに分散される負荷を生成し、これを各テナントにランダムに課し、ランダム間隔で発生させます。 このため、ロード パターンの生成には数分かかります。よって、少なくとも 3 ~ 4 分間ジェネレーターを実行してから負荷を監視することをお勧めします。

1. *PowerShell ISE* で、...\\Learning Modules\\Utilities\\*Demo-LoadGenerator.ps1* スクリプトを開きます。
1. **F5** を押すと、スクリプトが実行され、ロード ジェネレーターが起動します (既定のパラメーター値はそのままにしておきます)。

> [!IMPORTANT]
> 別のスクリプトを実行するには、新しい PowerShell ISE window を開きます。 ロード ジェネレーターは、ローカル PowerShell セッションで一連のジョブとして実行されています。 *Demo-LoadGenerator.ps1* スクリプトは、実際の負荷ジェネレーター スクリプトを起動します。これは、フォアグラウンド タスクに加えて一連のバックグラウンド負荷生成ジョブとして実行されます。 ロード ジェネレーターのジョブは、カタログに登録されているデータベースごとに呼び出されます。 ジョブはローカルの PowerShell セッションで実行しているため、PowerShell セッションを閉じるとすべてのジョブが停止します。 コンピューターを一時停止すると、負荷の生成は一時停止し、コンピューターを再開すると再開されます。

各テナントについての負荷生成ジョブをロード ジェネレーターが呼び出すと、フォアグラウンド タスクはジョブ呼び出し状態のままになり、後でプロビジョニングされる新しいテナント用の追加のバックグラウンド ジョブが開始されます。 *Ctrl-C* を使用するまたは *[停止]* ボタンを押すことによって、フォアグラウンド タスクを停止できますが、既存のバックグラウンド ジョブは各データベースの負荷の生成を続けます。 バックグラウンド ジョブを監視し、制御する必要がある場合、*Get-Job*、*Receive-Job* および *Stop-Job* を使用します。 フォア グラウンド タスクの実行中には、同じ PowerShell セッションを使用して別のスクリプトを実行することはできません。 別のスクリプトを実行するには、新しい PowerShell ISE window を開きます。

たとえば別のパラメーターを使用してロード ジェネレーター セッションを再開する場合、フォア グラウンド タスクを停止してから *Demo-LoadGenerator.ps1* スクリプトを再実行できます。 *Demo-LoadGenerator.ps1* を再実行すると、まず現在実行中のジョブが停止され、ジェネレーターが再開されます。このとき、現在のパラメーターを使用して一連の新しいジョブが起動されます。

ここでは、ジョブを呼び出す状態でロード ジェネレーターを実行したままにします。


## <a name="provision-a-new-tenant"></a>新しいテナントのプロビジョニング

最初のデプロイによって 3 つのサンプル テナントが作成されますが、テナントをもう 1 つ作成し、デプロイしたアプリケーションへの影響を確認しましょう。 Wingtip Tickets SaaS のテナントのプロビジョニング ワークフローの詳細は、「[プロビジョニングおよびカタログのチュートリアル](saas-dbpertenant-provision-and-catalog.md)」を参照してください。 この手順では、新しいテナントを短時間で作成します。

1. *PowerShell ISE* で、...\\Learning Modules\Provision and Catalog\\*Demo-ProvisionAndCatalog.ps1* を開きます。
1. **F5** を押してスクリプトを実行します (この時点では、既定値のままにしておきます)。

   > [!NOTE]
   > 多くの Wingtip SaaS スクリプトは、*$PSScriptRoot* を使用してフォルダーを移動し、他のスクリプト内の関数を呼び出します。 この変数は **F5** キーを押してスクリプト全体が実行されたときにのみ評価されます。  選択項目を強調表示して実行する (**F8** キー) とエラーが発生するため、スクリプトの実行時には **F5** キーを押してください。

新しいテナント データベースは SQL エラスティック プールに作成され、初期化され、カタログに登録されます。 プロビジョニングが正常に完了すると、新しいテナントの *Events* サイトがブラウザーに表示されます。

![新しいテナント](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

*Events Hub* を更新し、新しいテナントが一覧に表示されます。


## <a name="explore-the-servers-pools-and-tenant-databases"></a>サーバー、プール、テナント データベースを操作する

ここまでで、テナントのコレクションに対する負荷の実行を開始できました。続いては、デプロイされたリソースをいくつか見てみましょう。

1. [Azure Portal](http://portal.azure.com) で、SQL サーバーの一覧を参照して、**catalog-dpt-&lt;ユーザー&gt;** サーバーを開きます。 カタログ サーバーには 2 つのデータベースが含まれます。**tenantcatalog** と **basetenantdb** (新しいテナントを作成するためにコピーされたテンプレート データベース) です。

   ![データベース](./media/saas-dbpertenant-get-started-deploy/databases.png)

1. SQL サーバーの一覧に戻り、テナント データベースを保持している **tenants1-dpt-&lt;ユーザー&gt;** サーバーを開きます。 各テナント データベースは_エラスティック標準_データベースで、eDTU が 50 の Standard プールにあります。 以前にプロビジョニングしたテナント データベースである _Red Maple Racing_ データベースも表示されています。

   ![server](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>プールの監視

ロード ジェネレーターを数分間実行したら、十分な量のデータが生成されています。このデータを使って、プールとデータベースに組み込まれている監視機能の一部を確認してみましょう。

サーバー **tenants1-dpt-&lt;USER&gt;** を参照し、**[Pool1]** をクリックしてプールのリソース使用率を表示します (次のグラフではロード ジェネレーターは 1 時間実行しました)。

   ![プールの監視](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

上のグラフはプールの eDTU 使用率を示し、下のグラフは、プール内の上位 5 つのデータベースの eDTU 使用率を示しています。  この 2 つのグラフは、エラスティック プールと SQL Database が SaaS アプリケーション ワークロードにどのように対処しているかをわかりやすく示しています。 それぞれが 40 eDTU までバーストする 4 つのデータベースが、50 eDTU プールで問題なくサポートされています。 スタンドアロン データベースとしてプロビジョニングされた場合、バーストをサポートするためにそれぞれが S2 (50 DTU) 必要になります。 4 つのスタンドアロン S2 データベースのコストは、プールの価格の 3 倍近くになり、プールには、さらに多くのデータベースを収容できる余地がまだ十分にあります。 実際の環境では、現時点で、SQL Database のお客様用に最大 500 のデータベースが 200 eDTU のプールで実行されています。 詳細については、[パフォーマンスの監視](saas-dbpertenant-performance-monitoring.md)に関するチュートリアルをご覧ください。


## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]

> * Wingtip Tickets SaaS アプリケーションのデプロイ方法
> * アプリを構成するサーバー、プール、データベースについて
> * *カタログ*によるテナントとデータのマッピング
> * 新しいテナントのプロビジョニング方法
> * プール使用率を表示してテナント アクティビティを監視する方法
> * サンプル リソースを削除して、関連する課金を停止する方法

続いては、[プロビジョニングとカタログに関するチュートリアル](saas-dbpertenant-provision-and-catalog.md)をお試しください。



## <a name="additional-resources"></a>その他のリソース

* [Wingtip Tickets SaaS Database Per Tenant アプリケーションに基づく作業のための追加のチュートリアル](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* エラスティック プールの詳細については、[*Azure SQL エラスティック プール*](sql-database-elastic-pool.md)に関するページをご覧ください。
* エラスティック ジョブの詳細については、「[*スケールアウトされたクラウド データベースの管理*](sql-database-elastic-jobs-overview.md)」をご覧ください。
* マルチテナント SaaS アプリケーションの詳細については、[*マルチテナント SaaS アプリケーションの設計パターン*](saas-tenancy-app-design-patterns.md)に関するページをご覧ください。
