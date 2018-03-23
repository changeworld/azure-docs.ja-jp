---
title: Azure SQL Database を使用するマルチテナント アプリで新しいテナントをプロビジョニングする | Microsoft Docs
description: Azure SQL Database のマルチテナント SaaS アプリで新しいテナントをプロビジョニングしてカタログ化する方法について説明します。
keywords: SQL データベース チュートリアル
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 08/11/2017
ms.author: sstein
ms.openlocfilehash: 21f0bca3a16164ead4e0990842a968fd9b95c33f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>新しいテナントをプロビジョニングしてカタログに登録する方法の詳細

このチュートリアルでは、SaaS パターンのプロビジョニングおよびカタログと、Wingtip Tickets SaaS Database per Tenant アプリケーションで実装する方法について説明します。 新規のテナント データベースを作成して初期化し、アプリケーションのテナント カタログに登録します。 カタログ とは、SaaS アプリケーションの多数のテナントとそのデータの間のマッピング情報を保持するためのデータベースを指します。 カタログは、アプリケーションと管理の要求を正しいデータベースに転送するうえで重要な役割を果たします。  

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]

> * 新しいテナントを 1 件プロビジョニングする方法
> * バッチを使って追加のテナントをプロビジョニングする方法


このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。

* Wingtip Tickets SaaS Database per Tenant アプリをデプロイします。 5 分未満でデプロイするには、「[Deploy and explore the Wingtip Tickets SaaS Database per Tenant application (Wingtip Tickets SaaS Database Per Tenant アプリケーションのデプロイと探索)](saas-dbpertenant-get-started-deploy.md)」を参照してください。
* Azure PowerShell がインストールされている。 詳しくは、「[Azure PowerShell を使ってみる](https://docs.microsoft.com/powershell/azure/get-started-azureps)」をご覧ください。

## <a name="introduction-to-the-saas-catalog-pattern"></a>SaaS カタログ パターンの概要

データベースを基盤とするマルチテナント SaaS アプリケーションでは、テナントの情報が格納される場所を把握する必要があります。 SaaS カタログ パターンでは、各テナントとデータが格納されるデータベースとの間のマッピングに関する情報を保持するものとしてカタログ データベースを使用します。 このパターンは、テナント データが複数のデータベース間で分散されるたびに適用されます。

各テナントはカタログ内のキーによって識別され、各自のデータベースの場所にマップされます。 Wingtip Tickets アプリでは、このキーがテナント名のハッシュ値を使って生成されます。 これにより、アプリは、アプリケーションの URL に含まれているテナント名からキーを作成できます。 他のテナント キー スキームを使用できます。  

カタログによって、アプリケーションに対する影響を最小限に抑えて、データベースの名前と場所を変更できます。  マルチ テナント データベース モデルでは、カタログはデータベース間のテナントの移動も可能にします。  また、テナントやデータベースがメンテナンスやその他の措置のためにオフラインになっているかどうかを示すためにも、カタログを使用できます。 詳しくは、[シングル テナントの復元に関するチュートリアル](saas-dbpertenant-restore-single-tenant.md)をご覧ください。

カタログには、スキーマのバージョン、サービス プラン、テナントに提供される SLA などのテナントまたはデータベースのメタデータに加え、アプリケーションの管理、顧客のサポート、開発を可能にするその他の情報も格納できます。  

SaaS アプリケーション以上に、カタログはデータベース ツールを有効にできます。  Wingtip Tickets SaaS Database per Tenant サンプルでは、カタログはテナント間クエリを有効にするために使用されます。[アドホック レポートのチュートリアル](saas-tenancy-cross-tenant-reporting.md)に関する記事ご覧ください。 データベース間のジョブ管理については、[スキーマ管理](saas-tenancy-schema-management.md)および[テナント分析](saas-tenancy-tenant-analytics.md)のチュートリアルをご覧ください。 

Wingtip Tickets SaaS サンプルでは、[Elastic Database クライアント ライブラリ (EDCL)](sql-database-elastic-database-client-library.md) のシャード管理機能を使って、カタログが実装されています。 EDCL は Java と .Net フレームワークで利用できます。 EDCL により、アプリケーションは、データベースに支援されたシャード マップを作成、管理、使用できます。 シャード マップにはシャード (データベース) の一覧と、キー (テナント) とシャード間のマッピングが含まれます。  EDCL 関数は、テナント プロビジョニング時に、シャード マップのエントリを作成するために使用され、その後、実行時に、適切なデータベースに接続するためにアプリケーションによって使用されます。 EDCL は接続情報をキャッシュして、カタログ データベースへのトラフィックを最小限に抑え、アプリケーションを高速化します。  

> [!IMPORTANT]
> このマッピング データは、カタログ データベースからアクセスできるものの、"*編集することはできません*"。 マッピング データの編集には、必ずエラスティック データベース クライアント ライブラリ API を使用してください。 マッピング データを直接操作することは、カタログが破損するおそれがあるため、サポートしていません。


## <a name="introduction-to-the-saas-provisioning-pattern"></a>SaaS プロビジョニング パターンの概要

シングル テナント データベースを使用する SaaS アプリケーションに新しいテナントをオンボードする場合、新しいテナント データベースがプロビジョニングされる必要があります。  データベースは、適切な場所とサービス層に作成され、適切なスキーマと参照データを使って初期化され、適切なテナント キー下のカタログに登録される必要があります。  

データベース プロビジョニングには、SQL スクリプトの実行、bacpac のデプロイ、テンプレート データベースのコピーなど、別の方法も使用できます。  

データベースのプロビジョニングは、スキーマ管理戦略の一部である必要があり、新しいデータベースは最新のスキーマでプロビジョニングされことを保証する必要があります。 この要件については、[スキーマ管理のチュートリアル](saas-tenancy-schema-management.md)をご覧ください。  

Wingtip Tickets Database per Tenant アプリは、カタログ サーバーにデプロイされている、_basetenantdb_ という名前のテンプレート データベースをコピーすることで、新しいテナントをプロビジョニングします。  プロビジョニングは、サインアップ エクスペリエンスや、スクリプトを使用してサポートされるオフラインの一部として、アプリケーションに統合できます。 このチュートリアルでは、PowerShell を使ったプロビジョニングについて説明します。 プロビジョニング スクリプトは、_basetenantdb_ データベースをコピーしてエラスティック プールに新しいテナント データベースを作成し、テナント固有情報を使ってデータベースを初期化して、カタログ シャード マップに登録します。  テナント データベースには、テナント名に基づいて名前が付けられますが、カタログでは任意の名前付け規則を使用してテナント キーがデータベース名にマップされるため、この名前付けスキームはパターンの重要な部分ではありません。 


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Wingtip Tickets SaaS Database Per Tenant アプリケーション スクリプトを入手する

Wingtip Tickets SaaS のスクリプトとアプリケーション ソース コードは、[WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub リポジトリから入手できます。 Wingtip Tickets SaaS スクリプトをダウンロードし、ブロックを解除する手順については、[一般的なガイダンス](saas-tenancy-wingtip-app-guidance-tips.md)に関する記事をご覧ください。


## <a name="provision-and-catalog-detailed-walkthrough"></a>プロビジョニングとカタログ登録の詳細手順

Wingtip Tickets アプリケーションで新しいテナント プロビジョニングを実装する方法を理解するには、ブレークポイントを追加して、テナントをプロビジョニングしているときのワークフローを確認します。

1. _PowerShell ISE_ で、...\\Learning Modules\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ を開いて、次のパラメーターを設定します。
   * **$TenantName** = 新しい会場の名称 (たとえば、*Bushwillow Blues*)。
   * **$VenueType** = 事前に定義しておいた会場の種類のいずれか (_ブルース_、クラシック、ダンス、ジャズ、柔道、モーターレース、多目的、オペラ、ロック、サッカー)。
   * **$DemoScenario** = **1** (*シングル テナントのプロビジョニング*)。

1. ブレークポイントを追加します。追加するには、*New-Tenant `* と記述されている行のどこかにカーソルを置いて、**F9** キーを押します。

   ![ブレーク ポイント](media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

1. **F5** キーを押して、スクリプトを実行します。

1. ブレークポイントでスクリプトの実行が停止したら、**F11** キーを押してコードにステップ インします。

   ![デバッグ](media/saas-dbpertenant-provision-and-catalog/debug.png)



呼び出された関数にステップ オーバーしたりステップ インしたりする **F10** キーや **F11** キーなどの**デバッグ**のメニュー オプションを使用して、スクリプトの実行をトレースします。 PowerShell スクリプトのデバッグの詳細については、「[PowerShell スクリプトの使用とデバッグに関するヒント](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)」を参照してください。


次の記述は、明示的に従う手順ではなく、スクリプトのデバッグ中にたどるワークフローの説明です。

1. **CatalogAndDatabaseManagement.psm1 モジュールのインポート**。このモジュールは、カタログとテナントレベルで[シャード管理](sql-database-elastic-scale-shard-map-management.md)関数を抽象化するものです。 このモジュールは、カタログ パターンの大部分をカプセル化するため、確認する価値があります。
1. **SubscriptionManagement.psm1 モジュールのインポート**。このモジュールには、Azure にサインインして、使用する Azure サブスクリプションを選択するための関数が含まれています。
1. **構成情報の取得**。 (F11 キーを押して) Get-Configuration にステップ インして、アプリの構成がどのように指定されているかを確認します。 リソース名などのアプリ固有の値は、ここで定義されています。スクリプトに慣れていないうちは、これらの値を変更しないでください。
1. **カタログ オブジェクトの取得**。 より高いレベルのスクリプトで使用されるカタログ オブジェクトを構成して返す Get-Catalog にステップ インします。  この関数では、**AzureShardManagement.psm1**からインポートされたシャード管理関数を使用します。 カタログ オブジェクトは、以下の要素で構成されています。
   * $catalogServerFullyQualifiedName。これは、標準的なステム部分とユーザー名を組み合わせた構成になっています (例: _catalog-\<ユーザー\>.database.windows.net_)。
   * $catalogDatabaseName。これは、構成 *tenantcatalog* から取得されます。
   * $shardMapManager オブジェクト。これは、カタログ データベースから初期化されます。
   * $shardMap オブジェクト。これは、カタログ データベースの _tenantcatalog_ シャード マップから初期化されます。
   高度なスクリプトでは、カタログ オブジェクトが作成されて返され、使用されます。
1. **新しいテナント キーの計算**。 ハッシュ関数を使用して、テナント名からテナント キーが作成されます。
1. **テナント キーが既に存在するかどうかの確認**。 カタログにキーがあることを確認します。
1. **New-TenantDatabase によるテナント データベースのプロビジョニング。** **F11** キーを使ってステップ インして、[Azure Resource Manager テンプレート](../azure-resource-manager/resource-manager-template-walkthrough.md)によってデータベースがどのようにプロビジョニングされているかを確認します。

    データベース名は、どのシャードがどのテナントに属しているかが明確になるよう、テナント名を基に作成されますが、他のデータベースの名前付け規則も使用できます。
    Resource Manager テンプレートは、カタログ サーバーでテンプレート データベース (_baseTenantDB_) をコピーすることでテナント データベースを作成します。 別の方法として、空のデータベースを作成し、bacpac をインポートして初期化するか、既知の場所から初期化スクリプトを実行できます。

    Resource Manager テンプレートは、…\Learning Modules\Common\ フォルダーにある *tenantdatabasecopytemplate.json* ファイルです。

1. テナント データベースが作成されると、**会場 (テナント) の名前と会場の種類の情報でさらに初期化されます**。 また、他の初期化もここで実行されます。

1. **テナント データベースは、***Add-TenantDatabaseToCatalog* により、テナント キーを使ってカタログに登録されます。 **F11** を使用して詳細にステップ インします。

    * シャード マップ (既知のデータベースの一覧) にカタログ データベースが追加されます。
    * キー値をシャードにリンクさせるマッピングが作成されます。
    * テナント (会場の名前) に関する追加のメタ データが、カタログの "*テナント*" テーブルに追加されます。  テナント テーブルは、ShardManagement スキーマの一部ではなく、EDCL ではインストールされません。  このテーブルは、アプリケーション固有の追加データをサポートするようにカタログ データベースを拡張できる仕組みを例示しています。   


プロビジョニングが完了したら、実行が元の *Demo-ProvisionAndCatalog* スクリプトに戻り、ブラウザーで新しいテナントの **[イベント]** ページが開きます。

   ![events](media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>テナントのバッチをプロビジョニングする

この演習では、17 のテナントのバッチをプロビジョニングします。 他の Wingtip Tickets SaaS Database per Tenant チュートリアルを開始する前に、このテナント バッチをプロビジョニングして、多くのデータベースを操作できるようにしておくことをお勧めします。

1. *PowerShell ISE* で ...\\Learning Modules\\ProvisionAndCatalog\\*Demo-ProvisionAndCatalog.ps1* を開き、*$DemoScenario* パラメーターを 3 に変更します。
   * **$DemoScenario** = **3** (*テナント バッチのプロビジョニング*)。
1. **F5** キーを押して、スクリプトを実行します。

このスクリプトにより、追加のテナントのバッチがデプロイされます。 このスクリプトでは、バッチを制御し、各データベースのプロビジョニングをリンクされているテンプレートに委任する [Azure Resource Manager テンプレート](../azure-resource-manager/resource-manager-template-walkthrough.md)を使用しています。 テンプレートをこのように使用することによって、Azure Resource Manager がスクリプトのプロビジョニングの処理を仲介できるようになっています。 テンプレートは、データベースを並列にプロビジョニングし、必要であれば再試行を処理します。 このスクリプトはべき等です。そのため、スクリプトがなんらかの理由で失敗または停止した場合はもう一度実行してください。

### <a name="verify-the-batch-of-tenants-successfully-deployed"></a>テナントのバッチが正常にデプロイされたかどうかの確認

* [Azure Portal](https://portal.azure.com) で、サーバーの一覧を参照して、*tenants1* サーバーを開きます。 **SQL データベース**をクリックして 17 個の追加データベースのバッチが一覧に追加されたことを確認します。

   ![データベースの一覧](media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>プロビジョニングのその他のパターン

このチュートリアルでは取り上げた以外のプロビジョニング パターンとしては、以下のものが存在します。

**データベースの事前プロビジョニング**。 事前プロビジョニング パターンでは、エラスティック プール内のデータベースには追加のコストが発生しないという点が利用されています。 課金の対象となるのはデータベースではなくエラスティック プールであり、アイドル状態のデータベースがリソースを消費することはありません。 プールにデータベースを事前にプロビジョニングしておいて、必要になったら割り当てるという方法を使えば、テナントの準備にかかる時間を大幅に削減できます。 事前にプロビジョニングされるデータベースの数は、予想されるプロビジョニング速度に応じて適切なバッファーを維持できるよう、必要に応じて調整できます。

**自動プロビジョニング**。 自動プロビジョニング パターンでは、プロビジョニング サービスによって、サーバー、プール、データベースが必要に応じて自動でプロビジョニングされます (このサービスでは、必要があればエラスティック プール内のデータベースの事前プロビジョニングも可能です)。 データベースの使用を停止したり、削除したりした場合には、このプロビジョニング サービスによりエラスティック プール間の格差が是正されます。 このようなサービスは単純なこともあれば、多くの地域にまたがるプロビジョニングを処理するなどして複雑になることもあります。また、ディザスター リカバリーのための geo レプリケーションをセットアップできます。 自動プロビジョニング パターンではまず、クライアント アプリケーションまたはスクリプトからプロビジョニングの要求がキューに送信され、その要求がプロビジョニング サービスによって処理されます。その後、アプリケーションまたはスクリプトからサービスに対して、処理が完了したかどうかを確認するポーリングが実行されます。 事前プロビジョニングを使用した場合には、バックグラウンドで実行している代替データベースをプロビジョニングするサービスにより、要求がすばやく処理されます。


## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]

> * 新しいテナントを 1 件プロビジョニングする方法
> * バッチを使って追加のテナントをプロビジョニングする方法
> * テナントをプロビジョニングしてカタログに登録するまでの流れを詳しく確認する方法

[パフォーマンスの監視に関するチュートリアル](saas-dbpertenant-performance-monitoring.md)を試してください。

## <a name="additional-resources"></a>その他のリソース

* [Wingtip Tickets SaaS Database Per Tenant アプリケーションに基づく作業のための追加のチュートリアル](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [エラスティック データベース クライアント ライブラリ](sql-database-elastic-database-client-library.md)
* [Windows PowerShell ISE でスクリプトをデバッグする方法](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
