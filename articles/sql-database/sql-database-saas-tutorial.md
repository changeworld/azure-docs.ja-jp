---
title: "Wingtip Tickets Platform (WTP) アプリケーション (Azure SQL Database を使用する Saas サンプル アプリケーション) のデプロイと操作 |Microsoft Docs"
description: "Azure SQL Database を使用する SaaS サンプル アプリケーションをデプロイして操作します"
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
ms.openlocfilehash: ff4dc19bb6e24ea9ceeed9721cfb3a85b4d10965
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---
# <a name="deploy-and-explore-a-multi-tenant-saas-application-that-uses-azure-sql-database"></a>Azure SQL Database を使用するマルチテナント SaaS アプリケーションのデプロイと操作

このチュートリアルでは、Wingtip Tickets Platform (WTP) SaaS アプリケーションをデプロイして操作します。 このアプリケーションは、SaaS アプリケーション パターンとしてテナント単位のデータベース パターンを使用し、複数のテナントにサービスを提供します。 このアプリケーションの目的は、各種 SaaS シナリオや SaaS の設計と管理のパターンを実現している Azure SQL Database の機能を紹介することにあります。

下にある *[Azure に配置する]* ボタンをクリックすると、5 分後に、SQL Database を使用するマルチテナント SaaS アプリケーションがクラウド上に作成されて稼働します。 アプリケーションは 3 つのサンプル テナントと共にデプロイされます。各テナントには専用のデータベースが設定され、すべてが 1 つの SQL エラスティック プールにデプロイされます。 アプリはお使いの Azure サブスクリプションにデプロイされ、フル アクセス権が付与されるため、個々のアプリケーション コンポーネントを調べながら操作することができます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]

> * WTP アプリケーションをデプロイする方法
> * アプリを構成するサーバー、プール、データベースについて
> * *カタログ*によるテナントとデータのマッピング
> * 新しいテナントのプロビジョニング方法
> * プール使用率を表示してテナント アクティビティを監視する方法
> * サンプル リソースを削除して、関連する課金を停止する方法

さまざまな SaaS の設計と管理のパターンについて理解を深めることができるように、この初期デプロイをベースにした[一連の関連チュートリアル](sql-database-wtp-overview.md)が用意されています。 これらのチュートリアルでは、用意されたスクリプトを詳しく確認し、さまざまな SaaS パターンの実装方法を学習します。 スクリプトを実行しながら各チュートリアルを進めると、SaaS アプリケーションの開発を簡素化する多様な SQL Database 機能の実装方法について深く理解することができます。

このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。

* Azure PowerShell がインストールされている。 詳細については、「[Azure PowerShell を使ってみる](https://docs.microsoft.com/powershell/azure/get-started-azureps)」をご覧ください。

## <a name="deploy-the-wingtip-tickets-wtp-saas-application"></a>Wingtip Tickets Platform (WTP) SaaS アプリケーションのデプロイ

以下の手順に従うと Wingtip Tickets Platform を 5 分未満でデプロイできます。

1. 下のボタンをクリックして、デプロイを開始します。

   <a href="http://aka.ms/deploywtpapp" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. デプロイに必須のパラメーター値を入力します。

    > [!IMPORTANT]
    > 一部の認証とサーバー ファイアウォールは、デモンストレーションのために、意図的に保護されていません。 **リソース グループは新しく作成してください**。既存のリソース グループ、サーバー、プールは使用しないでください。また、このアプリケーションや作成されたリソースを運用環境で使用しないでください。 関連する課金を停止するために、サンプル アプリケーションの操作が終了したら、このリソース グループを削除してください。

    * **[リソース グループ]** - **[新規作成]** を選択して、**名前**と**場所**を指定します。
    * **[ユーザー]** - 一部のリソースでは、すべての Azure サブスクリプションで一意の名前が必要です。 一意性を確保するには、自分が作成するリソースと、Wingtip アプリケーションをデプロイする他のユーザーが作成したリソースを区別する値を指定します。 自分のイニシャル + 数字 (例: *bg1*) のような短い**ユーザー**名を作成し、それをリソース グループ名にも使用する (例: *wingtip-bg1*) ことをお勧めします。 **[ユーザー]** パラメーターには、文字、数字、ハイフンのみを含めることができます。 先頭と末尾の文字は、英文字または数字にしてください (すべて小文字にすることをお勧めします)。

     ![template](./media/sql-database-saas-tutorial/template.png)

1. **アプリケーションをデプロイします**。

    * 使用条件に同意したら、チェック ボックスをオンにします。
    * **[ダッシュボードにピン留めする]** をオンにします。
    * **[購入]** をクリックします。

1. **[通知]** (検索ボックスの右にあるベル アイコン) をクリックして、デプロイ ステータスを監視します。 WTP アプリのデプロイには約 4 分かかります。

   ![デプロイメントに成功しました](media/sql-database-saas-tutorial/succeeded.png)

## <a name="explore-the-application"></a>アプリケーションの探索

WTP アプリは、コンサート ホール、ジャズ クラブ、スポーツ クラブなど、イベントを開催する会場の窓口の機能を提供するアプリです。 各会場は Wingtip Tickets Platform (WTP) の顧客 (テナント) として登録され、イベントの一覧表示やチケットの販売を簡単に行うことができます。 会場ごとに、イベントの管理と表示やチケットの販売を行うための Web アプリが作成され、相互に独立して配置されます。 内部では、テナントごとに SQL Database が 1 つずつ作成され、単一の SQL エラスティック プールにデプロイされます。

デプロイに固有のテナント URL の一覧が、**Events Hub** という中央のページに表示されます。 すべてのテナント URL は、*[ユーザー]* で指定した値が含まれた、http://events.wtp.&lt;ユーザー&gt;.trafficmanager.net/*fabrikamjazzclub* という形式になっています。 

1. http://events.wtp.&lt;ユーザー&gt;.trafficmanager.net で _Events Hub_ を開きます (<ユーザー> は実際のユーザー名に置き換えてください)。

    ![Events Hub](media/sql-database-saas-tutorial/events-hub.png)

1. *Events Hub* で **[Fabrikam Jazz Club]** をクリックします。

   ![イベント](./media/sql-database-saas-tutorial/fabrikam.png)

1. **[チケット]** をクリックし、イベントのチケット購入の操作をしてみてください。

WTP アプリケーションは、[*Azure Traffic Manager*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) を使用して着信トラフィックの分散を制御します。 テナントに固有であるイベント ページは、URL にテナント名が含まれている必要があります。 イベント アプリは URL からテナント名を解析し、その名前を使って、"[*シャード マップ管理*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-scale-shard-map-management)" を使用するカタログにアクセスするためのキーを作成します。 このカタログによってキーがテナントのデータベースの場所にマップされます。 **Events Hub** は、カタログ内の拡張メタデータを使用して、各データベースに関連付けられたテナントの名前を取得します。

運用環境では、通常、[*会社のインターネット ドメインで Traffic Manager プロファイルが参照されるように*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-point-internet-domain) CNAME DNS レコードを作成します。

## <a name="get-the-wingtip-application-scripts"></a>Wingtip アプリケーションのスクリプトの取得

Wingtip Tickets のスクリプトとアプリケーションのソース コードは、[WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) Github リポジトリから入手できます。 スクリプト ファイルは、[Learning Modules フォルダー](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules)にあります。 **Learning Modules** フォルダーを、構造を保ったままローカル コンピューターにダウンロードしてください。

## <a name="provision-a-new-tenant"></a>新しいテナントのプロビジョニング

初期デプロイでは 3 つのサンプル テナントが作成されますが、チュートリアルの効果を最大限に高めるために、テナントを増やす必要があります。 この手順では、新しいテナントを短時間で作成します。 この手順が完了したら、[プロビジョニングとカタログに関するチュートリアル](sql-database-saas-tutorial-provision-and-catalog.md)で新しいテナントのプロビジョニングに関する詳細を確認してください。このチュートリアルでは、アプリケーションに登録コンポーネントを実装し、顧客のサインアップ時にテナントを自動的にプロビジョニングする簡単な方法が説明されています。

### <a name="initialize-the-user-config-file-for-your-deployment"></a>デプロイ用のユーザー構成ファイルを初期化する

これは WTP アプリケーションの初期デプロイ後に初めて実行するスクリプトであるため、ユーザー構成ファイルを更新する必要があります。 変数はデプロイに固有の値に置き換えてください。

   1. *PowerShell ISE* で ...\\Learning Modules\\*UserConfig.psm1* を開きます。
   1. _$userConfig.ResourceGroupName_ を、アプリのデプロイ時に _[リソース グループ]_ に設定した値に変更します。
   1. _$userConfig.Name_ を、アプリのデプロイ時に _[ユーザー]_ に設定した名前に変更します。

### <a name="provision-the-new-tenant"></a>新しいテナントをプロビジョニングする

1. *PowerShell ISE* で ...\\Learning Modules\Provision and Catalog\\*Demo-ProvisionAndCatalog.ps1* を開きます。
1. **F5** を押してスクリプトを実行します (この時点では、スクリプトの既定値はそのままにしておきます)。

新しいテナントがカタログに登録されます。 テナントのためのデータベースが作成され、SQL エラスティック プールに追加されます。 プロビジョニングが正常に完了すると、新しいテナントのチケット販売サイトがブラウザーに表示されます。

![新しいテナント](./media/sql-database-saas-tutorial/red-maple-racing.png)

Events Hub を更新し、新しいテナントが一覧に含まれていることを確認します。

## <a name="start-generating-load-on-the-tenant-databases"></a>テナント データベースに対する負荷の生成

テナント データベースを複数作成できたところで、実際に動作させてみましょう。 すべてのテナント データベースに対して実行されるワークロードをシミュレートする PowerShell スクリプトが用意されています。 負荷は既定で 60 分間実行されます。 Wingtip Tickets は SaaS アプリです。SaaS アプリに対する実際の負荷は、通常、散発的で予測できません。 これをシミュレートするために、ロード ジェネレーターはすべてのテナントに分散されるランダムな負荷を生成します。 パターンが出現するまで数分かかるため、ロード ジェネレーターを 5 ～ 10 分程度実行してから、負荷を監視する次のセクションに進んでください。

1. **PowerShell ISE** で ...\\Learning Modules\\Utilities\\*Demo-LoadGenerator.psm1* を開きます。
1. **F5** を押すと、スクリプトが実行され、ロード ジェネレーターが起動します (既定のパラメーター値はそのままにしておきます)。

> [!IMPORTANT]
> ロード ジェネレーターは、ローカル PowerShell セッションで一連のジョブとして実行されるため、PowerShell セッションは開いたままになります。 ジョブは停止するには、PowerShell を閉じるかロード ジェネレーターを開始ししたタブを閉じる、またはマシンを中断します。


## <a name="explore-the-servers-pools-and-tenant-databases"></a>サーバー、プール、テナント データベースを操作する

ここまでで、アプリケーションを実際に操作し、新しいテナントをプロビジョニングして、テナントのコレクションに対する負荷の実行を開始できました。続いては、デプロイされたリソースをいくつか見てみましょう。

1. [Azure Portal](http://portal.azure.com) で**catalog-&lt;ユーザー&gt;** サーバーを開きます。 このカタログ サーバーには 2 つのデータベースがあります。 **tenantcatalog** と **basetenantdb** (新しいテナントを作成するためにコピーされた空の *golden* db) です

   ![データベース](./media/sql-database-saas-tutorial/databases.png)

1. テナント データベースを保持している **tenants1-&lt;ユーザー&gt;** サーバーを開きます。 各テナント データベースは_エラスティック標準_データベースで、eDTU が 50 の Standard プールにあることがわかります。 以前にプロビジョニングしたテナント _Red Maple Racing_ データベースも表示されています。

   ![server](./media/sql-database-saas-tutorial/server.png)

## <a name="monitor-the-pool"></a>プールの監視

ロード ジェネレーターを数分間実行したら、十分な量のデータが生成されています。このデータを使って、プールとデータベースに組み込まれている監視機能の一部を確認してみましょう。

1. *tenants1-&lt;ユーザー&gt;* サーバーを参照し、**Pool1** をクリックすると、このプールのリソース使用率が表示されます。

   ![プールの監視](./media/sql-database-saas-tutorial/monitor-pool.png)

この 2 つのグラフは、エラスティック プールと SQL Database が SaaS アプリケーション ワークロードにどのように対処しているかをわかりやすく示しています。 それぞれが 40 eDTU までバーストする 4 つのデータベースが、50 eDTU プールで問題なくサポートされています。 各データベースがスタンドアロン データベースとしてプロビジョニングされている場合は、バーストをサポートするためにそれぞれが S2 (50 DTU) である必要がありますが、スタンドアロン S2 データベース 4 つのコストはプールの料金の約 3 倍になります。 プールには、さらに多くのデータベースを収容できる余地がまだ十分にあります。 実際の環境では、現時点で、最大 500 のデータベースが 200 eDTU のプールで実行されています。 詳細については、[パフォーマンスの監視](sql-database-saas-tutorial-performance-monitoring.md)に関するチュートリアルをご覧ください。


## <a name="deleting-the-resources-created-with-this-tutorial"></a>このチュートリアルで作成したリソースの削除

WTP アプリの操作と確認が終わったら、このデプロイに関連するすべての課金を停止するために、ポータルでアプリケーションのリソース グループを参照して削除します。 付随するチュートリアルのいずれかを使用した場合は、作成したすべてのリソースもアプリケーションと共に削除されます。


## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]

> * WTP アプリケーションをデプロイする方法
> * アプリを構成するサーバー、プール、データベースについて
> * *カタログ*によるテナントとデータのマッピング
> * 新しいテナントのプロビジョニング方法
> * プール使用率を表示してテナント アクティビティを監視する方法
> * サンプル リソースを削除して、関連する課金を停止する方法

続いては、[プロビジョニングとカタログに関するチュートリアル](sql-database-saas-tutorial-provision-and-catalog.md)をお試しください。



## <a name="additional-resources"></a>その他のリソース

* [Wingtip Tickets Platform (WTP) アプリケーションの初期のデプロイに基づく作業のための追加のチュートリアル](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* エラスティック プールの詳細については、[*Azure SQL エラスティック プール*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)に関するページをご覧ください。
* エラスティック ジョブの詳細については、「[*スケールアウトされたクラウド データベースの管理*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview)」をご覧ください。
* マルチテナント SaaS アプリケーションの詳細については、[*マルチテナント SaaS アプリケーションの設計パターン*](https://docs.microsoft.com/azure/sql-database/sql-database-design-patterns-multi-tenancy-saas-applications)に関するページをご覧ください。

