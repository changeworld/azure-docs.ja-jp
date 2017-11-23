---
title: "マルチテナント SaaS チュートリアル - Azure SQL Database | Microsoft Docs"
description: "Azure SQL Database を使用するスタンドアロン シングルテナント SaaS アプリケーションをデプロイおよび操作します。"
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
ms.author: sstein
ms.openlocfilehash: ce0268f800dcf1900730d6ad9c476fb06320a79e
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Azure SQL Database を使用するスタンドアロン シングルテナント アプリケーションをデプロイおよび操作する

このチュートリアルでは、Wingtip Tickets SaaS スタンドアロン アプリケーションをデプロイして操作します。 このアプリケーションの目的は、各種 SaaS シナリオの有効化を簡素化する Azure SQL Database の機能を紹介することにあります。

スタンドアロン アプリケーション パターンでは、テナントごとにシングルテナント アプリケーションとシングルテナント データベースを含む Azure リソース グループを展開します。  アプリケーションの複数のインスタンスは、マルチテナント ソリューションを提供するようにプロビジョニングできます。

このチュートリアルでは、いくつかのテナントのリソース グループをユーザーの Azure サブスクリプションにデプロイしますが、このパターンを使用して、リソース グループをテナントの Azure サブスクリプションにデプロイすることができます。  Azure には、サービス プロバイダーがテナントの代わりに、テナントのサブスクリプションの管理者としてこれらのリソース グループを管理するためのパートナー プログラムがあります。

以下のデプロイ セクションには、3 つの [Azure へのデプロイ] ボタンがあります。それぞれのボタンは、特定のテナント用にカスタマイズされたアプリケーションの異なるインスタンスをデプロイします。 各ボタンを押すと、対応するアプリケーションは 5 分後に完全にデプロイされます。  アプリは、Azure サブスクリプションにデプロイされます。  ユーザーには、個々のアプリケーション コンポーネントを確認して操作するフル アクセスがあります。

アプリケーションのソース コードと管理スクリプトは、[WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) GitHub リポジトリから入手できます。


このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]

> * Wingtip Tickets SaaS スタンドアロン アプリケーションをデプロイする方法
> * アプリケーションのソース コード、および管理スクリプトを取得する場所
> * アプリを構成するサーバーおよびデータベースについて

このアプリケーション パターンに基づいて管理シナリオの範囲を調べるための追加のチュートリアルは、近いうちにリリースされます。   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Wingtip Tickets SaaS スタンドアロン アプリケーションをデプロイする

次のように 3 つの指定されたテナント用にアプリをデプロイします。

1. **[Azure へのデプロイ]** ボタンを 1 つずつクリックして、Azure ポータルでデプロイ テンプレートを開きます。 各テンプレートには、2 つのパラメーター値が必要です。新しいリソース グループの名前と、アプリの他のデプロイからこのデプロイを区別するユーザー名です。 次の手順では、これらの値を設定するための詳細を提供します。<br><br>
    <a href="http://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>     &nbsp;&nbsp;**Contoso Concert Hall**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp;&nbsp;**Dogwood Dojo**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>    &nbsp;&nbsp;**Fabrikam Jazz Club**

2. 各デプロイに必須のパラメーター値を入力します。

    > [!IMPORTANT]
    > 一部の認証とサーバー ファイアウォールは、デモンストレーションのために、意図的に保護されていません。 アプリケーション デプロイごとに**新しいリソース グループを作成します**。  既存のリソース グループを使用しないでください。 運用環境にはこのアプリケーション、またはこのアプリケーションが作成したリソースを使用しないでください。 関連する課金を停止するために、サンプル アプリケーションの操作が終了したら、すべてのリソース グループを削除してください。

    リソース名にはアルファベットの小文字、数字、およびハイフンのみを使用することをお勧めします。
    * **[リソース グループ]** - [新規作成] を選択して、リソース グループの [名前] を指定します (大文字と小文字が区別されます)。
        * リソース グループ名のすべての文字を小文字にすることをお勧めします。
        * これにダッシュ、イニシャル、一桁の数字を付加することをお勧めします (_wingtip-sa-af1_ など)。
        * ドロップダウン から [場所] を選択します。

    * **ユーザー** - イニシャルに一桁の数字を付加するなど短いユーザー値を選択することをお勧めします (_af1_ など)。


1. **アプリケーションをデプロイします**。

    * 使用条件に同意したら、チェック ボックスをオンにします。
    * **[購入]** をクリックします。

1. **[通知]** (検索ボックスの右にあるベル アイコン) をクリックして、3 つすべてのデプロイのデプロイ ステータスを監視します。 アプリのデプロイには約 5 分かかります。


## <a name="run-the-application"></a>アプリケーションの実行

WTP アプリは、コンサート ホール、ジャズ クラブ、スポーツ クラブなど、イベントを開催する会場の窓口の機能を提供するアプリです。 各会場は Wingtip チケットの顧客 (テナント) として登録され、イベントの一覧表示やチケットの販売を簡単に行うことができます。 会場ごとに、イベントの管理と表示やチケットの販売を行うための Web サイトが作成され、相互に独立して配置されます。 各テナントは、見えないところで個別のアプリケーション インスタンスとスタンドアロン SQL データベースを取得します。

1. 個別のブラウザー タブで、次のように 3 つのテナントごとにイベント ページを開きます。

    http://events.contosoconcerthall.&lt;USER&gt;.trafficmanager.net <br>
    http://events.dogwooddojo.&lt;USER&gt;.trafficmanager.net<br>
    http://events.fabrikamjazzclub.&lt;USER&gt;.trafficmanager.net

    (&lt;USER&gt; をユーザーのデプロイの [ユーザー] 値に置換してください)。

   ![イベント](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)


アプリは、着信要求の分散を制御するために [*Azure Traffic Manager*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) を使用します。 各テナント固有のアプリには、URL 内のドメイン名の一部としてテナント名が含まれています。 すべてのテナント URL は、*[ユーザー]* で指定した値が含まれた、http://events.&lt;venuename&gt;.&lt;USER&gt;.trafficmanager.net という形式になっています。 各テナントのデータベースの場所は、対応するデプロイされたアプリのアプリ設定に含まれています。

運用環境では、通常、[*会社のインターネット ドメインで Traffic Manager プロファイルの URL が参照されるように*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-point-internet-domain) CNAME DNS レコードを作成します。


## <a name="explore-the-servers-and-tenant-databases"></a>サーバーおよびテナント データベースを操作する

デプロイされたリソースの一部を見てみましょう。

1. [Azure Portal](http://portal.azure.com) で、リソース グループの一覧を参照します。  **catalog-sa-&lt;USER&gt;** サーバーが **tenantcatalog** データベースでデプロイされている **wingtip-sa-catalog-&lt;USER&gt;** リソース グループが表示されるはずです。 3 つのテナントのリソース グループも表示されるはずです。

1. **wingtip-sa-fabrikam-&lt;USER&gt;** リソース グループを開きます。これには、Fabrikam Jazz Club デプロイのリソースが含まれます。  **fabrikamjazzclub-&lt;USER&gt;** サーバーには、**fabrikamjazzclub** データベースが含まれます。


各テナント データベースは 50 DTU _スタンドアロン_ データベースです。

## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]

> * Wingtip Tickets SaaS スタンドアロン アプリケーションをデプロイする方法
> * アプリを構成するサーバーおよびデータベースについて
> * サンプル リソースを削除して、関連する課金を停止する方法


## <a name="additional-resources"></a>その他のリソース

<!--* Additional [tutorials that build on the Wingtip SaaS application](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview) -->
* マルチテナント SaaS アプリケーションの詳細については、[*マルチテナント SaaS アプリケーションの設計パターン*](saas-tenancy-app-design-patterns.md)に関するページをご覧ください。
