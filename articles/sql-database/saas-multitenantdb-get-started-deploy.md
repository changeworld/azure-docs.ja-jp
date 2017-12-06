---
title: "Azure SQL Database を使用するシャード化されたマルチテナント データベース SaaS アプリのデプロイ | Microsoft Docs"
description: "Azure SQL Database を使用して SaaS パターンを示す、シャード化された Wingtip Tickets SaaS マルチテナント データベース アプリケーションをデプロイおよび操作します。"
keywords: "SQL データベース チュートリアル"
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: billgib;MightyPen
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: sstein
ms.openlocfilehash: cb55bf1f1c7eeb0fc7608aca8d70818b5e3e06c0
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2017
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application-that-uses-azure-sql-database"></a>Azure SQL Database を使用するシャード化されたマルチテナント アプリケーションのデプロイと操作

このチュートリアルでは、Wingtip Tickets という名前のサンプル SaaS マルチテナント データベース アプリケーションをデプロイおよび操作します。 Wingtip アプリの目的は、各種 SaaS シナリオの実装を簡素化する Azure SQL Database の機能を紹介することにあります。

Wingtips のこの実装では、シャード化されたマルチテナント データベース パターンを使用します。 シャーディングはテナント ID ごとに実行されます。 テナントのデータは、テナント ID の値に応じて特定のデータベースに配布されます。 特定のデータベースに含まれているテナント数に関係なく、すべてのデータベースは、テーブル スキーマにテナント ID が含まれているという意味でマルチテナントです。 

このデータベースのパターンを使用すると、1 つ以上のテナントを各シャードまたはデータベースに保存できます。 それぞれのデータベースを複数のテナントでシャード化することによって、最小限のコストに最適化できます。 または、それぞれのデータベースに 1 つのテナントのみを格納させることによって分離性を最適化できます。 最適化の選択は、特定のテナントごとに独立して行うことができます。 テナントを最初に格納するときに選択したり、後で変更したりできます。 どちらの場合でもアプリケーションが正しく動作するように設計されています。

#### <a name="app-deploys-quickly"></a>アプリのすばやいデプロイ

以下のデプロイ セクションには **[Deploy to Azure (Azure にデプロイ)]** ボタンがあります。 このボタンが押されると、Wingtip アプリは 5 分後に完全にデプロイされます。 Wingtip アプリは、Azure クラウドで動作し、Azure SQL Database を使用します。 Wingtip は、Azure サブスクリプションにデプロイされます。 個々のアプリケーション コンポーネントを操作するフル アクセスがあります。

アプリケーションは、3 つのサンプル テナント用のデータと共にデプロイされます。 テナントは、1 つのマルチテナント データベースにまとめて保存されます。

誰でも[当社の Github リポジトリ][link-github-wingtip-multitenantdb-55g]から Wingtip Tickets の C# および PowerShell のソース コードをダウンロードできます。

#### <a name="learn-in-this-tutorial"></a>このチュートリアルの詳細

> [!div class="checklist"]

> - Wingtip SaaS アプリケーションをデプロイする方法。
> - アプリケーションのソース コード、および管理スクリプトを取得する場所。
> - アプリを構成するサーバーおよびデータベースについて。
> - *カタログ*によるテナントとデータのマッピング方法。
> - 新しいテナントのプロビジョニング方法。
> - アプリでテナントのアクティビティを監視する方法。

この初期デプロイをビルドする一連の関連するチュートリアルを利用できます。 このチュートリアルでは、SaaS の広範な設計と管理パターンについて説明します。 チュートリアルに従って操作すると、指定されたスクリプトをステップ実行して、さまざまな SaaS パターンが実装される方法を確認するように求められます。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。

- 最新の Azure PowerShell がインストールされていること。 詳しくは、「[Azure PowerShell を使ってみる][link-azure-get-started-powershell-41q]」をご覧ください。

## <a name="deploy-the-wingtip-tickets-app"></a>Wingtip Tickets アプリのデプロイ

1. 次の **[Deploy to Azure (Azure にデプロイ)]** ボタンをクリックします。
    - Wingtip Tickets SaaS デプロイ テンプレートが指定された状態で Azure Portal が開きます。 テンプレートには、2 つのパラメーター値が必要です。新しいリソース グループの名前と、アプリの他のデプロイからこのデプロイを区別する 'ユーザー' 値です。 次の手順では、これらのパラメーター値を設定するための詳細を説明します。
        - 後で構成ファイルで必要になるため、使用する正確な値をメモしてください。

    [![[Deploy to Azure (Azure にデプロイ)] ボタン。][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

2. デプロイに必須のパラメーター値を入力します。

    > [!IMPORTANT]
    > 一部の認証、およびサーバーのファイアウォール設定は、デモを容易にするために意図的に安全ではないものを使用しています。 **[新しいリソース グループの作成]** を選択します。既存のリソース グループ、サーバー、またはプールは使用しません。 運用環境にはこのアプリケーション、またはこのアプリケーションが作成したリソースを使用しないでください。 関連する課金を停止するために、サンプル アプリケーションの操作が終了したら、このリソース グループを削除してください。

    リソース名にはアルファベットの小文字、数字、およびハイフンのみを使用することをお勧めします。

    - **[リソース グループ]** - **[新規作成]** を選択して、リソース グループの **[名前]** を指定します (大文字と小文字が区別されます)。
        - リソース グループ名のすべての文字を小文字にすることをお勧めします。
        - これにダッシュ、イニシャル、一桁の数字を付加することをお勧めします (*wingtip af1* など)。
        - ドロップダウン から **[場所]** を選択します。
    - **ユーザー** - イニシャルに一桁の数字を付加するなど短い**ユーザー**値を選択することをお勧めします (*af1* など)。

3. **アプリケーションをデプロイします**。

    - 使用条件に同意したら、チェック ボックスをオンにします。
    - **[購入]** をクリックします。

4. **[通知]** (検索ボックスの右にあるベル アイコン) をクリックして、デプロイ ステータスを監視します。 Wingtip アプリのデプロイには約 5 分かかります。

   ![デプロイメントに成功しました](media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>管理スクリプトのダウンロードとブロック解除

アプリケーションをデプロイ中に、アプリケーションのソース コードと管理スクリプトをダウンロードします。

> [!IMPORTANT]
> 実行可能なコンテンツ (スクリプト、dll) は、zip ファイルが外部ソースからダウンロードされ、抽出されると、Windows によってブロックされる場合があります。 zip ファイルからスクリプトを抽出するとき、以下の手順を実行して、.zip ファイルをブロック解除してから抽出します。 .zip ファイルをブロック解除することによってスクリプトを実行できるようになります。

1. [WingtipTicketsSaaS-MultiTenantDb github リポジトリ](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)を参照します。
2. **[複製またはダウンロード]** をクリックします。
3. **[Download ZIP]** をクリックし、ファイルを保存します。
4. **WingtipTicketsSaaS-MultiTenantDb-master.zip** ファイルを右クリックし、**[プロパティ]** を選択します。
5. **[全般]** タブで **[ブロックの解除]** を選択し、**[適用]** をクリックします。
6. **[OK]**をクリックします。
7. ファイルを解凍します。

スクリプトは、*..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\* フォルダーにあります。

## <a name="update-the-configuration-file-for-this-deployment"></a>このデプロイの構成ファイルの更新

スクリプトを実行する前に、**UserConfig.psm1** で*リソース グループ*と*ユーザー*の値を設定します。 これらの変数は、デプロイ中に設定した値に設定します。

1. *PowerShell ISE* で ...\\Learning Modules\\*UserConfig.psm1* を開きます。
2. *ResourceGroupName*と*Name*をデプロイの固有の値で更新します (10 行目および 11 行目のみ)。
3. 変更を保存します。

このファイルで設定された値はすべてのスクリプトで使用されるため、正確であることが重要です。 アプリを再デプロイする場合は、別のリソース グループとユーザー値を選択する必要があります。 次に新しい値で UserConfig を更新します。

## <a name="run-the-application"></a>アプリケーションの実行

WTP アプリは、コンサート ホール、ジャズ クラブ、スポーツ クラブなど、イベントを開催する会場の窓口の機能を提供するアプリです。 各会場は Wingtip プラットフォームの顧客 (テナント) として登録され、イベントの一覧表示やチケットの販売を簡単に行うことができます。 会場ごとに、イベントの管理と表示やチケットの販売を行うための Web アプリが作成され、相互に独立して配置されます。 内部では、テナントの各データが既定でシャード化されたマルチテナント データベースに保存されます。

固有デプロイのテナントへのリンク一覧が、**Events Hub** という中央のページに表示されます。

1. Web ブラウザーで *Events Hub* を開きます。
    - http://events.wingtip-mt.&lt;ユーザー&gt;.trafficmanager.net &nbsp; *(<ユーザー> は実際のデプロイのユーザー値に置き換えてください)。*

    ![Events Hub](media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. *Events Hub* で **[Fabrikam Jazz Club]** をクリックします。

   ![イベント](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

アプリは、着信要求の分散を制御するために [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) を使用します。 テナントに固有であるイベント ページには、URL にテナント名が含まれています。 この URL には、固有のユーザー値も含まれています。その形式は次のとおりです。

- http://events.wingtip-mt.&lt;ユーザー&gt;.trafficmanager.net/*fabrikamjazzclub*
 
イベント アプリは URL からテナント名を解析し、その名前をハッシュして、[シャード マップ管理](sql-database-elastic-scale-shard-map-management.md)を使用するカタログにアクセスするためのキーを作成します。 このカタログによってキーがテナントのデータベースの場所にマップされます。 **Events Hub** はカタログに登録されているすべてのテナントを一覧表示します。 **Events Hub** は、カタログ内の拡張メタデータを使用して、各マッピングに関連付けられたテナントの名前を取得し、URL を構築します。

運用環境では、通常、[会社のインターネット ドメインで Traffic Manager プロファイルが参照されるように](../traffic-manager/traffic-manager-point-internet-domain.md) CNAME DNS レコードを作成します。

## <a name="start-generating-load-on-the-tenant-databases"></a>テナント データベースに対する負荷の生成

これで、アプリがデプロイされました。作業を開始しましょう。 *Demo-LoadGenerator* PowerShell スクリプトは、各テナントに対して実行されるワークロードを開始します。 多くの SaaS アプリの実際の負荷は通常、散発的で予測不能です。 この種の負荷をシミュレートするために、ジェネレーターはすべてのテナントに分散される負荷を生成します。 負荷には、各テナントでランダムな間隔で発生するランダム化されたバーストが含まれています。 負荷パターンの生成には数分かかります。よって、少なくとも 3 ～ 4 分間ジェネレーターを実行してから負荷を監視することをお勧めします。

1. *PowerShell ISE* で、...\\Learning Modules\\Utilities\\*Demo-LoadGenerator.ps1* スクリプトを開きます。
2. **F5** を押すと、スクリプトが実行され、ロード ジェネレーターが起動します (既定のパラメーター値はそのままにしておきます)。

> [!IMPORTANT]
> *Demo-LoadGenerator.ps1* スクリプトはロード ジェネレーターが実行されている他の PowerShell セッションを開きます。 ロード ジェネレーターは、テナントごとに 1 つバックグラウンドの負荷生成ジョブを起動するフォアグラウンド タスクとしてこのセッションで実行されています。

フォアグラウンド タスクは、開始した後にはジョブ呼び出し状態のままになります。 このタスクは、その後にプロビジョニングされる新しいテナントに対して追加のバックグラウンド ジョブを開始します。

PowerShell セッションを閉じると、すべてのジョブが停止します。

ロード ジェネレーター セッションを再起動して、異なるパラメーター値を使用する必要が生じる場合があります。 その場合、PowerShell 生成セッションを閉じてから、*Demo-LoadGenerator.ps1* を再度実行します。

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>シャード化されたデータベースへの新しいテナントのプロビジョニング

最初のデプロイの *Tenants1* データベースには 3 つのサンプル テナントが含まれています。 別のテナントを作成して、デプロイ済みのアプリケーションにどのように影響するか確認してみましょう。 この手順では、新しいテナントを短時間で作成します。

1. *PowerShell ISE* で、...\\Learning Modules\ProvisionTenants\\*Demo-ProvisionTenants.ps1* を開きます。
2. **F5** を押してスクリプトを実行します (この時点では、既定値のままにしておきます)。

   > [!NOTE]
   > 多くの Wingtip Tickets SaaS スクリプトは *$PSScriptRoot* を使用して、フォルダーのナビゲーション、別のスクリプトの呼び出し、モジュールのインポートを可能にします。 この変数は **F5** キーを押してスクリプト全体が実行されたときにのみ評価されます。  選択項目を強調表示して実行する (**F8** キー) とエラーが発生するため、スクリプトの実行時には **F5** キーを押してください。

新しい Red Maple Racing テナントが *Tenants1* データベースに追加され、カタログに登録されます。 ブラウザーに新しいテナントのチケット販売 *Events* サイトが開きます。

![新しいテナント](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

*Events Hub* を更新し、新しいテナントが一覧に表示されます。

## <a name="provision-a-new-tenant-in-its-own-database"></a>独自のデータベース内の新しいテナントをプロビジョニングする

シャード化されたマルチテナント モデルでは、別のテナントを含むデータベース内の新しいテナントをプロビジョニングするか、または独自のデータベース内のテナントをプロビジョニングするかを選択できます。 独自のテナントは、独自のデータを別のテナントのデータから分離する際に役立ちます。 分離によって、そのテナントのパフォーマンスを別のテナントから独立して管理できます。 また、分離されたテナントのデータを簡単に素早く復元できます。 無料試用版または通常の顧客をマルチテナント データベースに格納し、プレミアム顧客を個別のデータベースに格納することを選択できます。 それぞれが 1 つのテナントのみを格納している多数のデータベースを作成した場合、それらすべてをエラスティック プールで一括して管理し、リソース コストを最適化できます。  

ここで別のテナントをプロビジョニングします。今回は独自のデータベースにプロビジョニングします。

1. ...\\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* で、*$TenantName* を **Salix Salsa** に、*$VenueType* を **dance** に、*$Scenario* を **2** に、それぞれ変更します。

2. **F5** キーを押して、スクリプトを再実行します。
    - F5 キーを押すと、別のデータベースに新しいテナントがプロビジョニングされます。 データベースとテナントはカタログに登録されます。 ブラウザーからテナントの [Events] ページを開きます。

   ![Salix Salsa イベント ページ](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - ページの一番下までスクロールします。 バナーの中にデータベース名が表示され、ここにテナントのデータが保存されます。

3. Events Hub を更新し、2 つの新しいテナントが一覧に表示されます。



## <a name="explore-the-servers-and-tenant-databases"></a>サーバーおよびテナント データベースを操作する

ここでデプロイされたリソースの一部が表示されます。

1. [Azure Portal](http://portal.azure.com) で、リソース グループの一覧を参照します。 アプリケーションをデプロイしたときに作成したリソース グループを開きます。

   ![resource group](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. **catalog-mt&lt;USER&gt;** サーバーをクリックします。 カタログ サーバーには、*tenantcatalog* と *basetenantdb* という名前の 2 つのデータベースが含まれています。 *basetenantdb* データベースは空のテンプレート データベースです。 これをコピーして新しいテナント データベースを作成します。多くのテナント用または 1 つのテナントのみに使用します。

   ![カタログ サーバー](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. リソース グループに戻り、 テナント データベースを保持している *tenants1 mt* サーバーを選択します。
    - tenants1 データベースは、マルチテナント データベースです。ここには、元の 3 つのテナントおよび追加した最初のテナントが保存されています。 これは 50 DTU Standard データベースとして構成されています。
    - **salixsalsa** データベースは、その唯一のテナントとして Salix Salsa ダンス会場 を保持しています。 これは、既定で 50 DTU の Standard Edition データベースとして構成されています。

   ![テナント サーバー](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)


## <a name="monitor-the-performance-of-the-database"></a>データベースのパフォーマンスの監視

ロード ジェネレーターを数分間実行したら、十分なテレメトリが生成されています。このテレメトリを使って、ポータルに組み込まれているデータベース監視機能の一部を確認してみましょう。

1. **tenants1-mt&lt;USER&gt;** サーバーを参照し、**[tenants1]** をクリックして 4 つのテナントを持つデータベースのリソース使用率を表示します。 各テナントは、ロード ジェネレーターから散発的な高負荷がかけられます。

   ![tenants1 の監視](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   DTU 使用率のグラフは、多くのテナント間の予測不能な負荷をマルチテナント データベースがサポートする方法を適切に示しています。 ここでは、ロード ジェネレーターは、各テナントに約 30 DTU の散発的な負荷をかけています。 この負荷は、50 DTU データベースの 60% の使用率に相当します。 60% を超えるピークは、同時に複数のテナントに負荷がかけられた結果です。

2. **tenants1-mt&lt;USER&gt;** サーバーを参照し、**salixsalsa** データベースをクリックして 1 つのテナントのみを含むこのデータベースのリソース使用率を表示します。

   ![salixsalsa データベース](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

ロード ジェネレーターは、各テナントがどのデータベースにあるかに関係なく、各テナントに同様の負荷をかけます。 **salixsalsa** データベースにはテナントが 1 つしかないため、このデータベースは、複数のテナントを含むデータベースよりもはるかに高い負荷に耐えることができます。 

> [!NOTE]
> ロード ジェネレーターによって作られた負荷は例示のみがその目的です。  マルチテナント データベースとシングルテナント データベースに割り当てられるリソース、およびマルチテナント データベースにホストできるテナント数は、アプリケーションの実際のワークロード パターンによって決まります。


## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]

> - Wingtip Tickets SaaS マルチテナント データベース アプリケーションのデプロイ方法
> - アプリを構成するサーバーおよびデータベースについて
> - *カタログ*によるテナントとデータのマッピング
> - マルチテナント データベースとシングルテナント データベースに新しいテナントをプロビジョニングする方法。
> - プール使用率を表示してテナント アクティビティを監視する方法
> - サンプル リソースを削除して、関連する課金を停止する方法

続いて、[テナントのプロビジョニングに関するチュートリアル](sql-database-saas-tutorial-provision-and-catalog.md)をお試しください。



## <a name="additional-resources"></a>その他のリソース

- マルチテナント SaaS アプリケーションの詳細については、[*マルチテナント SaaS アプリケーションの設計パターン*](https://docs.microsoft.com/azure/sql-database/sql-database-design-patterns-multi-tenancy-saas-applications)に関するページをご覧ください。








<!--  Link references.

A [series of related tutorials] is available that build upon this initial deployment.
[link-wtp-overivew-jumpto-saas-tutorials-97j]: saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials

-->

[link-aka-ms-deploywtp-mtapp-52k]: http://aka.ms/deploywtp-mtapp


[link-azure-get-started-powershell-41q]: https://docs.microsoft.com/powershell/azure/get-started-azureps

[link-github-wingtip-multitenantdb-55g]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/





<!--  Image references.

[image-deploy-to-azure-blue-48d]: http://aka.ms/deploywtp-mtapp "Button for Deploy to Azure."

-->

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png

