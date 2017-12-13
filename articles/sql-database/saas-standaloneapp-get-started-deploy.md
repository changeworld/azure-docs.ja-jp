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
ms.date: 11/30/2017
ms.author: genemi
ms.openlocfilehash: d38cd108821bce05824732bbdbdd322ae8563bde
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2017
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Azure SQL Database を使用するスタンドアロン シングルテナント アプリケーションをデプロイおよび操作する

このチュートリアルでは、Wingtip Tickets SaaS スタンドアロン アプリケーションをデプロイして操作します。 このアプリケーションの目的は、各種 SaaS シナリオの有効化を簡素化する Azure SQL Database の機能を紹介することにあります。

スタンドアロン アプリケーション パターンでは、テナントごとにシングルテナント アプリケーションとシングルテナント データベースを含む Azure リソース グループを展開します。  アプリケーションの複数のインスタンスは、マルチテナント ソリューションを提供するようにプロビジョニングできます。

このチュートリアルでは、複数テナントのリソース グループをユーザーの Azure サブスクリプションにデプロイします。  このパターンを使用すると、リソース グループをテナントの Azure サブスクリプションにデプロイすることができます。 Azure には、こうしたリソース グループを、サービス プロバイダーがテナントの代わりに管理するためのパートナー プログラムがあります。 サービス プロバイダーは、テナントのサブスクリプションの管理者です。

以下のデプロイ セクションには、青い **[Azure へのデプロイ]** ボタンが 3 つあります。 各ボタンによって、異なるアプリケーション インスタンスがデプロイされます。 インスタンスそれぞれが、特定のテナント用にカスタマイズされています。 各ボタンを押すと、対応するアプリケーションが 5 分以内に完全にデプロイされます。  アプリは、Azure サブスクリプションにデプロイされます。  ユーザーには、個々のアプリケーション コンポーネントを確認して操作するフル アクセスがあります。

アプリケーションのソース コードと管理スクリプトは、[WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) GitHub リポジトリから入手できます。


このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Wingtip Tickets SaaS スタンドアロン アプリケーションをデプロイする方法。
> * アプリケーションのソース コード、および管理スクリプトを取得する場所。
> * アプリを構成するサーバーおよびデータベースについて。

追加のチュートリアルがリリースされます。 これにより、このアプリケーション パターンに基づいて管理シナリオの範囲を調べることができます。   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Wingtip Tickets SaaS スタンドアロン アプリケーションをデプロイする

次のように 3 つの指定されたテナント用にアプリをデプロイします。

1. 青い **[Azure へのデプロイ]** ボタンを 1 つずつクリックして、[Azure Portal](https://portal.azure.com) でデプロイ テンプレートを開きます。 各テンプレートには、2 つのパラメーター値が必要です。新しいリソース グループの名前と、アプリの他のデプロイからこのデプロイを区別するユーザー名です。 次の手順では、これらの値を設定するための詳細を提供します。<br><br>
    <a href="http://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Contoso Concert Hall**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Dogwood Dojo**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Fabrikam Jazz Club**

2. 各デプロイに必須のパラメーター値を入力します。

    > [!IMPORTANT]
    > 一部の認証とサーバー ファイアウォールは、デモンストレーションのために、意図的に保護されていません。 アプリケーション デプロイごとに**新しいリソース グループを作成します**。  既存のリソース グループを使用しないでください。 運用環境にはこのアプリケーション、またはこのアプリケーションが作成したリソースを使用しないでください。 関連する課金を停止するために、サンプル アプリケーションの操作が終了したら、すべてのリソース グループを削除してください。

    リソース名にはアルファベットの小文字、数字、およびハイフンのみを使用することをお勧めします。
    * **リソース グループ** - **[新規作成]** を選択して、リソース グループの**名前**を小文字で指定します。
        * これにダッシュ、イニシャル、一桁の数字を付加することをお勧めします (*wingtip-sa-af1* など)。
        * ドロップダウン から **[場所]** を選択します。

    * **ユーザー** - イニシャルに一桁の数字を付加するなど、短いユーザー値を選択することをお勧めします (*af1* など)。


3. **アプリケーションをデプロイします**。

    * 使用条件に同意したら、チェック ボックスをオンにします。
    * **[購入]** をクリックします。

4. **[通知]** (検索ボックスの右にあるベル アイコン) をクリックして、3 つのデプロイすべてのデプロイ状態を監視します。 アプリのデプロイには 5 分かかります。


## <a name="run-the-application"></a>アプリケーションの実行

アプリは、イベントを開催する会場の窓口の機能を提供します。 会場の種類には、コンサート ホール、ジャズ クラブ、スポーツ クラブなどがあります。 会場は Wingtip Tickets アプリの顧客です。 Wingtip Tickets アプリでは、会場が "*テナント*" として登録されます。 テナントになることで、会場が顧客に対して、イベントの一覧表示やチケットの販売を簡単に行うことができます。 各会場が、イベントの表示やチケット販売を行うためのパーソナライズされた Web サイトを利用できます。 テナントそれぞれが、他のテナントから切り離され、相互に独立しています。 各テナントは、見えないところで、個別のアプリケーション インスタンスと、そのインスタンス独自のスタンドアロン SQL データベースを取得します。

1. 個別のブラウザー タブで、次のように 3 つのテナントごとにイベント ページを開きます。

    - http://events.contosoconcerthall.&lt;USER&gt;.trafficmanager.net
    - http://events.dogwooddojo.&lt;USER&gt;.trafficmanager.net
    - http://events.fabrikamjazzclub.&lt;USER&gt;.trafficmanager.net

    (各 URL で、&lt;USER&gt; を、デプロイのユーザー値に置き換えてください)。

   ![イベント](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

アプリは、着信要求の分散を制御するために [*Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md) を使用します。 各テナント固有のアプリ インスタンスには、URL 内のドメイン名の一部としてテナント名が含まれています。 すべてのテナント URL に、特定の**ユーザー**値が含まれます。 URL の形式を次に示します。
- http://events.&lt;venuename&gt;.&lt;USER&gt;.trafficmanager.net

各テナントのデータベースの**場所**は、対応するデプロイされたアプリのアプリ設定に含まれています。

運用環境では、通常、[*会社のインターネット ドメインで Traffic Manager プロファイルの URL が参照*](../traffic-manager/traffic-manager-point-internet-domain.md)されるようにCNAME DNS レコードを作成します。


## <a name="explore-the-servers-and-tenant-databases"></a>サーバーおよびテナント データベースを操作する

デプロイされたリソースの一部を見てみましょう。

1. [Azure Portal](http://portal.azure.com) で、リソース グループの一覧を参照します。
2. **wingtip-sa-catalog-&lt;USER&gt;** リソース グループを確認します。
    - このリソース グループに、**catalog-sa-&lt;USER&gt;** がデプロイされます。 サーバーには、**tenantcatalog** データベースが含まれます。
    - 3 つのテナントのリソース グループも表示されるはずです。
3. **wingtip-sa-fabrikam-&lt;USER&gt;** リソース グループを開きます。これには、Fabrikam Jazz Club デプロイのリソースが含まれます。  **fabrikamjazzclub-&lt;USER&gt;** サーバーには、**fabrikamjazzclub** データベースが含まれます。

各テナント データベースは 50 DTU *スタンドアロン* データベースです。

## <a name="additional-resources"></a>その他のリソース

<!--
* Additional [tutorials that build on the Wingtip SaaS application](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview)
-->

- マルチテナント SaaS アプリケーションの詳細については、[マルチテナント SaaS アプリケーションの設計パターン](saas-tenancy-app-design-patterns.md)に関するページをご覧ください。


## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Wingtip Tickets SaaS スタンドアロン アプリケーションをデプロイする方法。
> * アプリを構成するサーバーおよびデータベースについて。
> * サンプル リソースを削除して、関連する課金を停止する方法。

