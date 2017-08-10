---
title: "Azure SQL Database を使用するマルチテナント アプリで新しいテナントをプロビジョニングする | Microsoft Docs"
description: "Wingtip SaaS アプリで新しいテナントをプロビジョニングしてカタログに登録する方法について説明します"
keywords: "SQL データベース チュートリアル"
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: sstein
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: 658c316d8d9d14ce11dbb92188afbf0e68c00493
ms.contentlocale: ja-jp
ms.lasthandoff: 07/28/2017

---
# <a name="provision-new-tenants-and-register-them-in-the-catalog"></a>新しいテナントをプロビジョニングしてカタログに登録する

このチュートリアルでは、Wingtip SaaS アプリケーションで新しいテナントをプロビジョニングします。 テナント データベースをプロビジョニングし、カタログに登録することでテナントを作成します。 "*カタログ*" とは、SaaS アプリケーションのテナントとそのデータの間のマッピング情報を保持するためのデータベースを指します。

ここではスクリプトを使って、使用するプロビジョニングとカタログのパターンのほか、カタログに新しいテナントを登録する処理の実装を見ていきます。 カタログは、アプリケーションの要求を正しいデータベースに転送するうえで重要な役割を果たします。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]

> * 新しいテナントを 1 件プロビジョニングする方法
> * バッチを使って追加のテナントをプロビジョニングする方法
> * テナントをプロビジョニングしてカタログに登録するまでの流れを詳しく確認する方法


このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。

* Wingtip SaaS アプリがデプロイされている。 5 分未満でデプロイするには、[Wingtip SaaS アプリケーションのデプロイと確認](sql-database-saas-tutorial.md)に関するページを参照してください。
* Azure PowerShell がインストールされている。 詳細については、「[Azure PowerShell を使ってみる](https://docs.microsoft.com/powershell/azure/get-started-azureps)」をご覧ください。

## <a name="introduction-to-the-saas-catalog-pattern"></a>SaaS カタログ パターンの概要

データベースを基盤とするマルチテナント SaaS アプリケーションでは、テナントの情報が格納される場所を把握する必要があります。 SaaS カタログ パターンでは、テナントとデータの格納場所との間のマッピングに関する情報を格納するものとしてカタログ データベースを使用します。 Wingtip SaaS アプリで採用されているのは、"データベースごとに 1 つのテナント" アーキテクチャです。もっとも、マルチテナントとシングルテナントのどちらのデータベースを使っている場合でも、テナントとデータベースとの間のマッピング情報をカタログに記録することに関しては、共通の基本パターンが適用されます。

各テナントには、カタログ内で自らのデータを他のテナントと区別するためのキーが割り当てられます。 Wingtip SaaS アプリケーションでは、このキーがテナント名のハッシュ値を使って生成されます。 このパターンでは、アプリケーションの URL のテナント名の部分を使ってキーを作成し、特定のテナントに対する接続を取得できるようになっています。 パターン全体に影響がなければ、これ以外の ID スキームを使用することもできます。

アプリのカタログは、[エラスティック データベース クライアント ライブラリ (EDCL)](sql-database-elastic-database-client-library.md) のシャード管理テクノロジを使って実装しています。 EDCL は、データベースに裏付けられており、かつ "*シャード マップ*" が保持される "*カタログ*" の作成と管理を担当します。 カタログには、キー (テナント) とそのシャード (データベース) の間のマッピングが格納されます。

> [!IMPORTANT]
> このマッピング データは、カタログ データベースからアクセスできるものの、"*編集することはできません*"。 マッピング データの編集には、必ずエラスティック データベース クライアント ライブラリ API を使用してください。 マッピング データを直接操作することは、カタログが破損するおそれがあるため、サポートしていません。



## <a name="get-the-wingtip-application-scripts"></a>Wingtip アプリケーションのスクリプトを取得する

Wingtip SaaS のスクリプトとアプリケーション ソース コードは、[WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) GitHub リポジトリから入手できます。 [Wingtip SaaS のスクリプトをダウンロードする手順](sql-database-wtp-overview.md#download-and-unblock-the-wingtip-saas-scripts)。

## <a name="provision-one-new-tenant"></a>1 つの新しいテナントをプロビジョニングする

[最初の Wingtip SaaS チュートリアル](sql-database-saas-tutorial.md)で既にテナントを作成している場合は、このセクションを飛ばして次の「[テナントのバッチをプロビジョニングする](#provision-a-batch-of-tenants)」に進んでください。

*Demo-ProvisionAndCatalog* スクリプトを実行すると、テナントをすばやく作成してカタログに登録できます。

1. PowerShell ISE で **Demo-ProvisionAndCatalog.ps1** を開いて、以下の値を設定します。
   * **$TenantName** = 新しい会場の名称 (たとえば、*Bushwillow Blues*)。
   * **$VenueType** = 事前に定義しておいた会場の種類のいずれか (ブルース、クラシック、ダンス、ジャズ、柔道、モーターレース、多目的、オペラ、ロック、サッカー)。
   * **$DemoScenario** = 1。*シングル テナントのプロビジョニング*の場合には、この設定は _1_ のままにしておきます。

1. **F5** キーを押して、スクリプトを実行します。

スクリプトが完了すると、新しいテナントがプロビジョニングされ、その "*イベント*" のアプリがブラウザーで開きます。

![新しいテナント](./media/sql-database-saas-tutorial-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>テナントのバッチをプロビジョニングする

この演習では、バッチを使って追加のテナントをプロビジョニングします。 他の Wingtip SaaS チュートリアルを完了する前に、テナントのバッチをプロビジョニングして、多くのデータベースを操作できるようにしておくことをお勧めします。

1. *PowerShell ISE* で ...\\Learning Modules\\Utilities\\*Demo-ProvisionAndCatalog.ps1* を開き、*$DemoScenario* パラメーターを 3 に設定します。
   * **$DemoScenario** = **3**。*バッチを使ってテナントをプロビジョニングする*場合は **3** に変更します。
1. **F5** キーを押して、スクリプトを実行します。

このスクリプトにより、追加のテナントのバッチがデプロイされます。 このスクリプトでは、バッチを制御し、各データベースのプロビジョニングをリンクされているテンプレートに委任する [Azure Resource Manager テンプレート](../azure-resource-manager/resource-manager-template-walkthrough.md)を使用しています。 テンプレートをこのように使用することによって、Azure Resource Manager がスクリプトのプロビジョニングの処理を仲介できるようになっています。 テンプレートでは、データベースを可能な限り並行してプロビジョニングします。また、必要があればプロセス全体を最適化しながら再試行を処理します。 このスクリプトはべき等です。そのため、スクリプトがなんらかの理由で失敗または停止した場合はもう一度実行してください。

### <a name="verify-the-batch-of-tenants-successfully-deployed"></a>テナントのバッチが正常にデプロイされたかどうかの確認

* [Azure Portal](https://portal.azure.com) でサーバーの一覧を参照し、*tenants1* サーバーを開きます。**[SQL データベース]** をクリックし、今回は一覧に 17 の追加データベースのバッチがあることを確認します。

   ![データベースの一覧](media/sql-database-saas-tutorial-provision-and-catalog/database-list.png)


## <a name="provision-and-catalog-details"></a>プロビジョニングとカタログ登録の詳細

Wingtip アプリケーションで新しいテナントのプロビジョニングの実装がどのようになっているかについて理解を深めるため、もう一度 *Demo-ProvisionAndCatalog* スクリプトを実行して別のテナントをプロビジョニングしてみましょう。 今回は、ブレークポイントを設定してワークフローをステップ実行していきます。

1. ...\\Learning Modules\Utilities\_Demo-ProvisionAndCatalog.ps1_ を開いて、次のパラメーターを設定します。
   * **$TenantName** = テナント名は一意である必要があるため、既存のテナントとは異なる名前に設定します (たとえば、*Hackberry Hitters*)。
   * **$VenueType** = 事前に定義しておいた会場の種類のいずれか ("*柔道*" など) を使用します。
   * **$DemoScenario** = **1**。*シングル テナントのプロビジョニング*を行うする場合は **1** に設定します。

1. ブレークポイントを追加します。これには、*New-Tenant `* の行のどこかにカーソルを合わせて、**F9** キーを押します。

   ![ブレーク ポイント](media/sql-database-saas-tutorial-provision-and-catalog/breakpoint.png)

1. **F5** キーを押して、スクリプトを実行します。 ブレークポイントに達したら、**F11** キーを押してステップ インします。 呼び出された関数にステップ オーバーしたりステップ インしたりする **F10** キーや **F11** キーなどのデバッグのメニュー オプションを使用して、スクリプトの実行をトレースします。 PowerShell スクリプトのデバッグの詳細については、「[PowerShell スクリプトの使用とデバッグに関するヒント](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)」を参照してください。

### <a name="examine-the-provision-and-catalog-implementation-in-detail-by-stepping-through-the-script"></a>スクリプトのステップ実行によるプロビジョニングとカタログの実装の詳細確認

今回使用したスクリプトでは、以下のステップに従って新しいテナントをプロビジョニングし、カタログに登録しています。

1. **SubscriptionManagement.psm1 モジュールのインポート**。このモジュールには、Azure にサインインして、使用する Azure サブスクリプションを選択するための関数が含まれています。
1. **CatalogAndDatabaseManagement.psm1 モジュールのインポート**。このモジュールは、カタログとテナントレベルで[シャード管理](sql-database-elastic-scale-shard-map-management.md)関数を抽象化するものです。 このモジュールは、カタログ パターンの大部分をカプセル化するという点で重要であるため、確認する価値があります。
1. **構成情報の取得**。 (**F11** キーを押して) *Get-Configuration* にステップ インして、アプリの構成がどのように指定されているかを確認します。 リソース名などのアプリ固有の値は、ここで定義されています。スクリプトに慣れていないうちは、ここにある値を変更しないでください。
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

カタログ サーバーで "*ゴールデン*" データベース (basetenantdb) をコピーしてテナント データベースを作成するには、Resource Manager テンプレートを使用します。  別の方法としては、空のデータベースを作成してから、bacpac をインポートして初期化するというやり方もあります。

Resource Manager テンプレートは、…\\Learning Modules\\Common\\ フォルダーにある以下のファイルです。*tenantdatabasecopytemplate.json*

テナント データベースが作成されると、会場 (テナント) の名前と会場の種類の情報でさらに初期化されます。 また、他の初期化もここで実行されます。

テナント データベースは、*Add-TenantDatabaseToCatalog* により、テナント キーを使ってカタログに登録されます。 **F11** を使用して詳細にステップ インします。

* シャード マップ (既知のデータベースの一覧) にカタログ データベースが追加されます。
* キー値 (テナント) をシャード (データベース) にリンクするマッピングが作成されます。
* テナントに関する追加のメタ データが追加されます。

プロビジョニングが完了したら、実行が元の *Demo-ProvisionAndCatalog* スクリプトに戻り、ブラウザーで新しいテナントの **[イベント]** ページが開きます。

   ![events](media/sql-database-saas-tutorial-provision-and-catalog/new-tenant2.png)


## <a name="other-provisioning-patterns"></a>プロビジョニングのその他のパターン

このチュートリアルでは取り上げた以外のプロビジョニング パターンとしては、以下のものが存在します。

**データベースの事前プロビジョニング**。 事前プロビジョニング パターンでは、エラスティック プール内のデータベースには追加のコストが発生しないという点が利用されています。 課金の対象となるのはデータベースではなくエラスティック プールであり、アイドル状態のデータベースがリソースを消費することはありません。 プールにデータベースを事前にプロビジョニングしておいて、必要になったら割り当てるという方法を使えば、テナントの準備にかかる時間を大幅に削減できます。 事前にプロビジョニングされるデータベースの数は、予想されるプロビジョニング速度に応じて適切なバッファーを維持できるよう、必要に応じて調整されることがあります。

**自動プロビジョニング**。 自動プロビジョニング パターンでは、サーバー、プール、データベースを必要に応じて自動でプロビジョニングするための専用のサービスを使用します (このサービスでは、必要があればエラスティック プール内のデータベースの事前プロビジョニングも可能です)。 データベースの使用を停止したり、削除したりした場合には、このプロビジョニング サービスによりエラスティック プール間の格差が是正されます。 このようなサービスは単純なこともあれば、多くの地域にまたがるプロビジョニングを処理するなどして複雑になることもあります。また、ディザスター リカバリーのための戦略として geo レプリケーションを使用している場合には、サービスにより geo レプリケーションが自動でセットアップされることもあります。 自動プロビジョニングのパターンではまず、クライアント アプリケーションまたはスクリプトからプロビジョニングの要求がキューに送信され、その要求がプロビジョニング サービスによって処理されます。その後、アプリケーションまたはスクリプトからサービスに対して、処理が完了したかどうかを確認するポーリングが実行されます。 事前プロビジョニングを使用した場合には、バックグラウンドで実行している代替データベースをのプロビジョニングを管理するサービスにより、要求がすばやく処理されます。



## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]

> * 新しいテナントを 1 件プロビジョニングする方法
> * バッチを使って追加のテナントをプロビジョニングする方法
> * テナントをプロビジョニングしてカタログに登録するまでの流れを詳しく確認する方法

[パフォーマンスの監視に関するチュートリアル](sql-database-saas-tutorial-performance-monitoring.md)を試してください。

## <a name="additional-resources"></a>その他のリソース

* [Wingtip SaaS アプリケーションに基づく作業のための追加のチュートリアル](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [エラスティック データベース クライアント ライブラリ](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-client-library)
* [Windows PowerShell ISE でスクリプトをデバッグする方法](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)

