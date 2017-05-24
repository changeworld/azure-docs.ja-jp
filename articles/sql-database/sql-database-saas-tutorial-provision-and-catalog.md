---
title: "新しいテナントのプロビジョニングおよびカタログ登録 (Azure SQL Database を使用する SaaS アプリケーションの例) | Microsoft Docs"
description: "新しいテナントのプロビジョニングおよびカタログ登録"
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
ms.openlocfilehash: 4eeada941f8615fa04624bc725efcb44f05d56c7
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---
# <a name="provision-new-tenants-and-register-them-in-the-catalog"></a>新しいテナントをプロビジョニングしてカタログに登録する

このチュートリアルでは、SaaS アプリケーション Wingtip Tickets Platform (WTP) に新しいテナントをプロビジョニングします。 具体的には、テナントとテナント データベースを作成して、カタログにテナントを登録します。 "*カタログ*" とは、SaaS アプリケーションの多数のテナントとそのデータの間のマッピング情報を保持するためのデータベースを指します。 ここではスクリプトを使って、使用するプロビジョニングとカタログのパターンのほか、カタログに新しいテナントを登録する処理の実装を見ていきます。 カタログは、アプリケーションの要求を正しいデータベースに転送するうえで重要な役割を果たします。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]

> * 新しいテナントを 1 件プロビジョニングする方法
> * バッチを使って追加のテナントをプロビジョニングする方法
> * 新しいテナントをプロビジョニングしてカタログに登録するまでの流れを詳しく確認する方法


このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。

* WTP アプリがデプロイされている。 5 分未満でデプロイする方法については、[WTP SaaS アプリケーションのデプロイと確認](sql-database-saas-tutorial.md)に関するページを参照してください。
* Azure PowerShell がインストールされている。 詳細については、「[Azure PowerShell を使ってみる](https://docs.microsoft.com/powershell/azure/get-started-azureps)」をご覧ください。

## <a name="introduction-to-the-saas-catalog-pattern"></a>SaaS カタログ パターンの概要

データベースを基盤とするマルチテナント SaaS アプリケーションでは、テナントの情報が格納される場所を把握する必要があります。 SaaS カタログ パターンでは、テナントとデータの格納場所との間のマッピングに関する情報を格納するものとしてカタログ データベースを使用します。 WTP アプリで採用されているのは、シングルテナント データベース アーキテクチャです。もっとも、マルチテナントとシングルテナントのどちらのデータベースを使っている場合でも、テナントとデータベースとの間のマッピング情報をカタログに記録することに関しては、共通の基本パターンが適用されます。

各テナントには、カタログ内で自らのデータを他のテナントと区別するためのキーが割り当てられます。 WTP アプリケーションでは、このキーがテナント名のハッシュ値を使って生成されます。 このパターンでは、アプリケーションの URL のテナント名の部分を使ってキーを作成し、特定のテナントに対する接続を取得できるようになっています。 パターン全体に影響がなければ、これ以外の ID スキームを使用することもできます。

WTP アプリのカタログは、[エラスティック データベース クライアント ライブラリ (EDCL)](sql-database-elastic-database-client-library.md) のシャード管理テクノロジを使って実装しています。 EDCL は、データベースに裏付けられており、かつ "_シャード マップ_" が保持される "_カタログ_" の作成と管理を担当します。 カタログには、キー (テナント) とそのデータベース (シャード) の間のマッピングに関するデータが格納されます。

> [!IMPORTANT]
> このマッピング データは、カタログ データベースからアクセスできるものの、"*編集することはできません*"。 マッピング データの編集には、必ずエラスティック データベース クライアント ライブラリ API を使用してください。 マッピング データを直接操作することは、カタログが破損するおそれがあるため、サポートしていません。

Wingtip SaaS アプリは、"*ゴールデン*" データベースをコピーすることによって、新しいテナントをプロビジョニングします。

## <a name="get-the-wingtip-application-scripts"></a>Wingtip アプリケーションのスクリプトの取得

Wingtip Tickets のスクリプトとアプリケーションのソース コードは、[WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) Github リポジトリから入手できます。 スクリプト ファイルは、[Learning Modules フォルダー](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules)にあります。 **Learning Modules** フォルダーを、構造を保ったままローカル コンピューターにダウンロードしてください。

## <a name="provision-a-new-tenant"></a>新しいテナントのプロビジョニング

最初の WTP チュートリアルで既にテナントを作成している場合には、このセクションは飛ばして「[バッチを使ったテナントのプロビジョニング](#provision-a-batch-of-tenants)」に進んでください。

*Demo-ProvisionAndCatalog* スクリプトを実行すると、テナントをすばやく作成してカタログに登録できます。

1. PowerShell ISE で **Demo-ProvisionAndCatalog.ps1** を開いて、以下の値を設定します。
   * **$TenantName** = 新しい会場の名称 (たとえば、*Bushwillow Blues*)。 
   * **$VenueType** = 事前に定義しておいた会場の種類のいずれか (ブルース、クラシック、ダンス、ジャズ、柔道、モーターレース、多目的、オペラ、ロック、サッカー)。
   * **$DemoScenario** = 1。**シングル テナントのプロビジョニング**の場合には、この設定は _1_ のままにしておきます。

1. **F5** キーを押して、スクリプトを実行します。

スクリプトが完了すると、新しいテナントがプロビジョニングされ、その "*イベント*" のアプリがブラウザーで開きます。

![新しいテナント](./media/sql-database-saas-tutorial-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>バッチを使ったテナントのプロビジョニング

この演習では、バッチを使って追加のテナントをプロビジョニングします。 この作業は、他の WTP チュートリアルを完了する前に済ませておくことをお勧めします。

1. *PowerShell ISE* で ...\\Learning Modules\\Utilities\\*Demo-ProvisionAndCatalog.ps1* を開いて、以下の値を設定します。
   * **$DemoScenario** = **3**。**バッチを使ってテナントをプロビジョニングする**場合には、**3** に設定します。
1. **F5** キーを押して、スクリプトを実行します。

このスクリプトにより、追加のテナントのバッチがデプロイされます。 このスクリプトでは、バッチを制御し、各データベースのプロビジョニングをリンクされているテンプレートに委任する [Azure Resource Manager テンプレート](../azure-resource-manager/resource-manager-template-walkthrough.md)を使用しています。 テンプレートをこのように使用することによって、Azure Resource Manager がスクリプトのプロビジョニングの処理を仲介できるようになっています。 テンプレートでは、データベースを可能な限り並行してプロビジョニングします。また、必要があればプロセス全体を最適化しながら再試行を処理します。 このスクリプトはべき等性が保証されているため、中断されても再度実行されます。

### <a name="verify-the-batch-of-tenants-successfully-deployed"></a>テナントのバッチが正常にデプロイされたかどうかの確認

* [Azure Portal](https://portal.azure.com) で *tenants1* サーバーを開いて、**[SQL データベース]** をクリックします。

   ![データベースの一覧](media/sql-database-saas-tutorial-provision-and-catalog/database-list.png)


## <a name="provision-and-catalog-detailed-walkthrough"></a>プロビジョニングとカタログ登録の詳細手順

Wingtip アプリケーションで新しいテナントのプロビジョニングの実装がどのようになっているかについて理解を深めるため、もう一度 *Demo-ProvisionAndCatalog* スクリプトを実行して別のテナントをプロビジョニングしてみましょう。 今回は、ブレークポイントを設定してワークフローをステップ実行していきます。

1. ...\\Learning Modules\Utilities\_Demo-ProvisionAndCatalog.ps1_ を開いて、現在のカタログに存在しない新しいテナントの値として、以下を設定します。
   * **$TenantName** = 新しい名前 (*Hackberry Hitters* など) を設定します。
   * **$VenueType** = 事前に定義しておいた会場の種類のいずれか ("*柔道*" など) を使用します。
   * **$DemoScenario** = 1。**シングル テナントのプロビジョニング**であるため、**1** に設定します。

1. ブレークポイントを追加します。これには、*New-Tenant `* の行のどこかにカーソルを合わせて、**F9** キーを押します。

   ![ブレーク ポイント](media/sql-database-saas-tutorial-provision-and-catalog/breakpoint.png)

1. **F5** キーを押して、スクリプトを実行します。 ブレークポイントに達したら、**F11** キーを押してステップ インします。 **F10** キーと **F11** キーを使用し、呼び出された関数にステップ オーバーやステップ インをしながら、スクリプトの実行をトレースします。 [PowerShell スクリプトの使用とデバッグに関するヒント](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)

### <a name="examine-the-provision-and-catalog-implementation-in-detail-by-stepping-through-the-script"></a>スクリプトのステップ実行によるプロビジョニングとカタログの実装の詳細確認

今回使用したスクリプトでは、以下のステップに従って新しいテナントをプロビジョニングし、カタログに登録しています。

1. **SubscriptionManagement.psm1 モジュールのインポート**。このモジュールには、Azure にサインインして、使用する Azure サブスクリプションを選択するための関数が含まれています。
1. **CatalogAndDatabaseManagement.psm1 モジュールのインポート**。このモジュールは、カタログとテナントレベルで[シャード管理](sql-database-elastic-scale-shard-map-management.md)関数を抽象化するものです。 このモジュールは、カタログ パターンの大部分をカプセル化するという点で重要であるため、確認する価値があります。
1. **構成情報の取得**。 (**F11** キーを押して) _Get-Configuration_ にステップ インして、アプリの構成がどのように指定されているかを確認します。 リソース名などのアプリ固有の値は、ここで定義されています。スクリプトに慣れていないうちは、ここにある値を変更しないでください。
1. **カタログ オブジェクトの取得**。 *Get-Catalog* にステップ インして、**AzureShardManagement.psm1** からインポートされたシャード管理関数によってカタログがどのように初期化されるかを確認します。 カタログは、以下のオブジェクトで構成されています。
   * $catalogServerFullyQualifiedName。これは、標準的なステム部分とユーザー名を組み合わせた構成になっています (例: _catalog-\<ユーザー\>.database.windows.net_)。
   * $catalogDatabaseName。これは、構成 *tenantcatalog* から取得されます。
   * $shardMapManager オブジェクト。これは、カタログ データベースから初期化されます。
   * $shardMap オブジェクト。これは、カタログ データベースの *tenantcatalog* シャード マップから初期化されます。
   高度なスクリプトでは、カタログ オブジェクトが作成されて返され、使用されます。
1. **新しいテナント キーの計算**。 ハッシュ関数を使用して、テナント名からテナント キーが作成されます。
1. **テナント キーが既に存在するかどうかの確認**。 カタログにキーがあることを確認します。
1. **New-TenantDatabase によるテナント データベースのプロビジョニング。** **F11** キーを使ってステップ インして、Resource Manager テンプレートによってデータベースがどのようにプロビジョニングされているかを確認します。
    
データベース名は、どのシャードがどのテナントに属しているかが明確になるよう、テナント名を基に作成されます  (もっとも、データベースに名前を付ける戦略としては、これ以外のものも簡単に使用できます)。

カタログ サーバーで **"*ゴールデン*" データベース (baseTenantDB) をコピーしてテナント データベースを作成する**ときには、Resource Manager テンプレートを使用します。  別の方法としては、空のデータベースを作成してから、bacpac をインポートして初期化するというやり方もあります。

Resource Manager テンプレートは、…\\Learning Modules\\Common\\ フォルダーにある以下のファイルです。*tenantdatabasecopytemplate.json*

テナント データベースが作成されると、会場 (テナント) の名前と会場の種類の情報でさらに初期化されます。 また、他の初期化もここで実行されます。

テナント データベースは、*Add-TenantDatabaseToCatalog* により、テナント キーを使ってカタログに登録されます。 **F11** を使用して詳細にステップ インします。

* シャード マップ (既知のデータベースの一覧) にカタログ データベースが追加されます。
* キー値をシャードにリンクさせるマッピングが作成されます。
* テナントに関する追加のメタ データ (会場の名前) が追加されます。

プロビジョニングが完了したら、実行が元の *Demo-ProvisionAndCatalog* スクリプトに戻り、ブラウザーで新しいテナントの**イベント** ページが開きます。

   ![events](media/sql-database-saas-tutorial-provision-and-catalog/new-tenant2.png)


## <a name="other-provisioning-patterns"></a>プロビジョニングのその他のパターン

このチュートリアルでは取り上げた以外のプロビジョニング パターンとしては、以下のものが存在します。

**データベースの事前プロビジョニング**。 このパターンでは、(データベースではなく、エラスティック プール単位で課金が発生する関係上) エラスティック プール内のデータベースには追加のコストがかからないという点と、アイドル状態のデータベースがリソースを消費しないという点を利用したものとなっています。 プールにデータベースを事前にプロビジョニングしておいて、必要になったら割り当てるという方法を使えば、テナントの準備にかかる時間を大幅に削減できます。 事前にプロビジョニングされるデータベースの数は、予想されるプロビジョニング速度に応じて適切なバッファーを維持できるよう、必要に応じて調整されることがあります。

**自動プロビジョニング**。 このパターンでは、サーバー、プール、データベースを必要に応じて自動でプロビジョニングするための専用のサービスを使用します (このサービスでは、必要があればエラスティック プール内のデータベースの事前プロビジョニングも可能です)。 データベースの使用を停止したり、削除したりした場合には、このプロビジョニング サービスによりエラスティック プール間の格差が是正されます。 このようなサービスは単純なこともあれば、多くの地域にまたがるプロビジョニングを処理するなどして複雑になることもあります。また、DR のための戦略として geo レプリケーションを使用している場合には、サービスにより geo レプリケーションが自動でセットアップされることもあります。 自動プロビジョニングのパターンではまず、クライアント アプリケーションまたはスクリプトからプロビジョニングの要求がキューに送信され、その要求がプロビジョニング サービスによって処理されます。その後、アプリケーションまたはスクリプトからサービスに対して、処理が完了したかどうかを確認するポーリングが実行されます。 事前プロビジョニングを使用した場合には、バックグラウンドで実行している代替データベースをのプロビジョニングを管理するサービスにより、要求がすばやく処理されます。


## <a name="stopping-wingtip-saas-application-related-billing"></a>Wingtip SaaS アプリケーション関連の課金の停止

ほかのチュートリアルに進む予定がなければ、課金を停止するためにリソースをすべて削除することをお勧めします。 WTP アプリケーションをデプロイしたリソース グループを削除すると、そのリソースがすべて削除されます。

* ポータルでアプリケーションのリソース グループを参照して削除すると、この WTP のデプロイに関連する課金がすべて停止します。

## <a name="tips"></a>ヒント

* EDCL にはほかにも、クライアント アプリケーションがカタログにアクセスし、カタログを操作できるようにするための重要な機能が用意されています。 また、アプリケーションが正しいデータベースに接続できるように、特定のキー値について EDCL を使用して ADO.NET 接続を取得することもできます。 クライアントはこの接続情報をキャッシュして、カタログ データベースへのトラフィックを最小限に抑え、アプリケーションを高速化します。



## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]

> * 新しいテナントを 1 件プロビジョニングする方法
> * バッチを使って追加のテナントをプロビジョニングする方法
> * 新しいテナントをプロビジョニングしてカタログに登録するまでの流れを詳しく確認する方法

[パフォーマンスの監視に関するチュートリアル](sql-database-saas-tutorial-performance-monitoring.md)

## <a name="additional-resources"></a>その他のリソース

* [Wingtip Tickets Platform (WTP) アプリケーションの初期のデプロイに基づく作業のための追加のチュートリアル](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [エラスティック データベース クライアント ライブラリ](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-client-library)
* [Windows PowerShell ISE でスクリプトをデバッグする方法](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)

