---
title: "Azure SQL Database のマルチテナント SQL データベースを使用した SaaS アプリケーションでの新しいテナントのプロビジョニングおよびカタログ登録 | Microsoft Docs"
description: "Azure SQL Database のマルチテナント SaaS アプリで新しいテナントをプロビジョニングしてカタログ化する方法について説明します。"
keywords: "SQL データベース チュートリアル"
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: saas apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: billgib
ms.openlocfilehash: ec753027c8ce8040cbc574279a44eb24590fcb05
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2017
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-sql-database"></a>シャード マルチテナント SQL データベースを使用した SaaS アプリケーションでの新しいテナントのプロビジョニングおよびカタログ登録 | Microsoft Docs

このチュートリアルでは、シャード マルチテナント SQL データベース モデルを使用する場合の、テナントのプロビジョニングおよびカタログ登録のパターンを説明します。 

テナント データを保持するテーブルの主キーにテナント ID を含むマルチテナント スキーマでは、1 つのデータベースに複数のテナントを格納できます。 多くのテナントをサポートするために、テナント データは複数のシャードまたはデータベースに分散されます。 1 つのテナントのデータは、常に 1 つのデータベースに完全に含まれています。  カタログは、テナントのデータベースへのマッピングを保持するために使用されます。   

1 つのテナントで複数のデータベースを使用することもできます。 複数のテナントを保持するデータベースは、テナントの分離よりも、テナントあたりのコストをより低く抑えることを優先します。  テナントを 1 つだけ保持するデータベースは、低コストよりも分離を優先します。  複数のテナントを保持するデータベースと 1 つのテナントを保持するデータベースは、同じ SaaS アプリケーション内で混在させることができ、各テナントでコストまたは分離を最適化できます。 テナントをプロビジョニングする際、テナントに独自のデータベースを設定できます。あるいは、後でテナントを独自のデータベースに移動することができます。

   ![テナント カタログを使用したシャード マルチテナント データベース アプリ](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)


## <a name="tenant-catalog-pattern"></a>テナント カタログ パターン

テナント データを複数のデータベースに分散する場合、各テナントのデータが格納される場所を把握しておくことが重要になります。 カタログ データベースは、テナントとそのデータを格納するデータベースとの間のマッピングを保持しています。

各テナントは、カタログ内のキーによって識別されます。 Wingtip Tickets SaaS アプリでは、このテナント キーがテナント名のハッシュ値を使って生成されます。 これにより、アプリケーションは Web ページ URL からテナント名を抽出し、そのテナント名を使用して適切なデータベースに接続できます。 他のテナント キー スキームを使用することもできます。

カタログを使用すると、アプリケーションを中断することなく、プロビジョニング後でもテナント データベースの名前や場所を変更できます。  マルチ テナント データベース モデルでは、カタログによってデータベース間のテナントの移動が可能になります。  また、アプリケーションに対してテナントがメンテナンスやその他の措置のためにオフラインになっているかどうかを示すためにも、カタログを使用できます。

カタログを拡張し、追加のテナントまたはデータベース メタデータ (データベースの層やエディション、データベース スキーマのバージョン、テナント名とサービス プランまたは SLA などの情報) を格納することで、アプリケーションの管理、カスタマー サポート、DevOps プロセスが可能になります。  

また、カタログを使用して、テナント間レポートの作成、スキーマ管理、分析目的のデータ抽出も可能です。 

### <a name="elastic-database-client-library"></a>Elastic Database クライアント ライブラリ 
Wingtip Tickets SaaS アプリでは、[Elastic Database クライアント ライブラリ (EDCL)](sql-database-elastic-database-client-library.md) のシャード管理機能を使用して、カタログが *tenantcatalog* データベースに実装されています。 このライブラリにより、アプリケーションはデータベースに支援された 'シャード マップ' を作成、管理、使用できます。 シャード マップにはシャード (データベース) の一覧、およびキー (テナント ID) とシャードとの間のマッピングが含まれます。  EDCL 関数は、テナント プロビジョニング時にアプリケーションまたは PowerShell スクリプトから使用して、シャード マップのエントリを作成したり、後で適切なデータベースに接続したりできます。 このライブラリは接続情報をキャッシュして、カタログ データベースへのトラフィックを最小限に抑え、接続を高速化します。 

> [!IMPORTANT]
> このマッピング データは、カタログ データベースからアクセスできるものの、"*編集することはできません*"。 マッピング データの編集には、必ずエラスティック データベース クライアント ライブラリ API を使用してください。 マッピング データを直接操作することは、カタログが破損するおそれがあるため、サポートしていません。


## <a name="tenant-provisioning-pattern"></a>テナントのプロビジョニング パターン

シャード マルチテナント データベース モデル内で新しいテナントをプロビジョニングする場合、テナントを共有データベースと指定の独自のデータベースのどちらでプロビジョニングするかを最初に決める必要があります。 共有データベースを使用する場合、既存のデータベース内に空きがあるかどうか、または新しいデータベースが必要かどうかを判断する必要があります。 新しいデータベースが必要な場合、テナント データベースは、適切な場所とサービス レベルでプロビジョニングされ、適切なスキーマと参照データを使って初期化されてからカタログに登録される必要があります。 最後に、適切なシャードを参照するためにテナント マッピングを追加できます。

SQL スクリプトの実行、bacpac のデプロイ、またはテンプレート データベースのコピーによって、データベースをプロビジョニングします。 Wingtip Tickets SaaS アプリケーションは、テンプレート データベースをコピーして新しいテナント データベースを作成します。

データベースのプロビジョニング方法は、全体のスキーマ管理戦略に組み込まれる必要があります。この戦略では、新しいデータベースが確実に最新のスキーマでプロビジョニングされる必要があります。  詳細については、[スキーマの管理に関するチュートリアル](saas-tenancy-schema-management.md)をご覧ください。  

このチュートリアルで取り扱うテナント プロビジョニング スクリプトには、他のテナントと共有している既存のデータベースにテナントをプロビジョニングする方法と、独自のデータベースにテナントをプロビジョニングする方法の両方が含まれています。 次に、テナント データは初期化され、カタログ シャード マップに登録されます。 このサンプル アプリでは、複数のテナントを含むデータベースには *tenants1*、*tenants2* などの一般名がつけられ、1 つのテナントを含むデータベースにはそのテナントの名前がつけられています。 カタログを使用すればデータベースに任意の名前を割り当てることができるため、このサンプルで使用されている名前付け規則は、このパターンの重要事項ではありません。  

## <a name="provision-and-catalog-tutorial"></a>プロビジョニングおよびカタログ登録のチュートリアル

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]

> * マルチテナント データベースにテナントをプロビジョニングする
> * シングルテナント データベースにテナントをプロビジョニングする
> * マルチテナント データベースとシングルテナント データベースにテナントをまとめてプロビジョニングする
> * カタログにデータベースとテナント マッピングを登録する

このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。

* Wingtip Tickets SaaS マルチテナント データベース アプリがデプロイされます。 5 分未満でデプロイするには、「[Deploy and explore the Wingtip Tickets SaaS Multi-tenant Database application (Wingtip Tickets SaaS マルチテナント データベース アプリケーションのデプロイと探索)](saas-multitenantdb-get-started-deploy.md)」を参照してください。
* Azure PowerShell がインストールされている。 詳しくは、「[Azure PowerShell を使ってみる](https://docs.microsoft.com/powershell/azure/get-started-azureps)」をご覧ください。

## <a name="get-the-wingtip-tickets-management-scripts"></a>Wingtip Tickets 管理スクリプトの入手

管理スクリプトとアプリケーションのソース コードは、[WingtipTicketsSaaS-MultiTenantDB](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub リポジトリから入手できます。 <!--See [Steps to download the Wingtip SaaS scripts](saas-tenancy-wingtip-app-guidance-tips.md#download-and-unblock-the-wingtip-saas-scripts).-->


## <a name="provision-a-tenant-in-a-shared-database-with-other-tenants"></a>他のテナントと共有しているデータベースにテナントをプロビジョニングする

Wingtip Tickets アプリケーションを使用して共有データベースに新しいテナントのプロビジョニングを実装する方法を理解するには、ブレークポイントを追加して、次のワークフローに従います。

1. _PowerShell ISE_ で、...\\Learning Modules\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ を開き、次のパラメーターを設定します。
   * **$TenantName** = **Bushwillow Blues** (新しい会場の名前)。
   * **$VenueType** = **blues** (事前に定義しておいた会場の種類のいずれか: *blues*、classicalmusic、dance、jazz、judo、motorracing、multipurpose、opera、rockmusic、soccer (小文字、スペースなし))。
   * **$Scenario** = **1** (*他のテナントと共有しているデータベースにテナントをプロビジョニング*します)。

1. ブレークポイントを追加します。追加するためには、38 行目 (*New-Tenant `* と記述されている行) のどこかにカーソルを置いて、**F9** キーを押します。

   ![ブレーク ポイント](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

1. **F5** キーを押して、スクリプトを実行します。

1. ブレークポイントでスクリプトの実行が停止したら、**F11** キーを押してコードにステップ インします。

   ![debug](media/saas-multitenantdb-provision-and-catalog/debug.png)

呼び出された関数にステップ オーバーしたりステップ インしたりする **F10** キーや **F11** キーなどの**デバッグ**のメニュー オプションを使用して、スクリプトの実行をトレースします。 PowerShell スクリプトのデバッグの詳細については、「[PowerShell スクリプトの使用とデバッグに関するヒント](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)」を参照してください。


これから行うプロビジョニング ワークフローの主な要素を次に示します。

* **新しいテナント キーの計算**。 ハッシュ関数を使用して、テナント名からテナント キーが作成されます。
* **テナント キーが既に存在するかどうかの確認**。 キーがまだ登録されていないことを確認するため、カタログがチェックされます。
* **既定のテナント データベース内でのテナントの初期化**。 新しいテナント情報を追加するため、テナント データベースが更新されます。  
* **カタログへのテナントの登録**。新しいテナント キーと既存の tenants1 データベースとの間のマッピングがカタログに追加されます。 
* **カタログの拡張テーブルへのテナント名の追加**。 カタログ内の Tenants テーブルに会場名が追加されます。  これは、アプリケーション固有の追加データをサポートするようにカタログ データベースを拡張できる仕組みを示しています。
* **新しいテナントのイベント ページの表示**。 次のように、ブラウザーで *Bushwillow Blues* イベント ページが開きます。

   ![events](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)


## <a name="provision-a-tenant-in-its-own-database"></a>独自のデータベース内にテナントをプロビジョニングする

次に、独自のデータベースにテナントを作成するプロセスを説明します。

1. 引き続き ...\\Learning Modules\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ 内で、次のパラメーターを設定します。
   * **$TenantName** = **Sequoia Soccer** (新しい会場の名前)。
   * **$VenueType** = **soccer** (事前に定義しておいた会場の種類のいずれか: blues、classicalmusic、dance、jazz、judo、motorracing、multipurpose、opera、rockmusic、*soccer* (小文字、スペースなし))。
   * **$Scenario** = **2** (*他のテナントと共有しているデータベースにテナントをプロビジョニング*します)。

1. 新しいブレークポイントを追加します。これには、57 行目 (*&&nbsp;$PSScriptRoot\New-TenantAndDatabase `* と記述されている行) のどこかにカーソルを置いて、**F9 キー**を押します。

   ![ブレーク ポイント](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

1. **F5** キーを押して、スクリプトを実行します。

1. ブレークポイントでスクリプトの実行が停止したら、**F11** キーを押してコードにステップ インします。  **F10** キーや **F11** キーを使用し、関数にステップ オーバーおよびステップ インして実行をトレースします。

スクリプトをトレースする間に行うプロビジョニング ワークフローの主な要素を次に示します。

* **新しいテナント キーの計算**。 ハッシュ関数を使用して、テナント名からテナント キーが作成されます。
* **テナント キーが既に存在するかどうかの確認**。 キーがまだ登録されていないことを確認するため、カタログがチェックされます。
* **新しいテナント データベースの作成**。 Resource Manager テンプレートを使用して *basetenantdb* データベースをコピーすることで、データベースが作成されます。  新しいデータベースの名前はテナント名に基づきます。
* **カタログへのデータベースの追加**。 新しいテナント データベースが、カタログ内にシャードとして登録されます。
* **既定のテナント データベース内でのテナントの初期化**。 新しいテナント情報を追加するため、テナント データベースが更新されます。  
* **カタログへのテナントの登録**。新しいテナント キーと *sequoiasoccer* データベースとの間のマッピングがカタログに追加されます。
* **カタログへのテナント名の追加**。 カタログ内のテナント拡張テーブルに会場名が追加されます。
* **新しいテナントのイベント ページの表示**。 次のように、ブラウザーで *Sequoia Soccer* イベント ページが開きます。

   ![events](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)


## <a name="provision-a-batch-of-tenants"></a>テナントのバッチをプロビジョニングする

この演習では、17 のテナントのバッチをプロビジョニングします。 他の Wingtip Tickets チュートリアルを開始する前に、このテナントをまとめてプロビジョニングして、多くのデータベースを操作できるようにしておくことをお勧めします。

1. *PowerShell ISE* で ...\\Learning Modules\\ProvisionAndCatalog\\*Demo-ProvisionAndCatalog.ps1* を開き、*$Scenario* パラメーターを 3 に変更します。
   * **$Scenario** = **3** (*テナントをまとめて共有データベースにプロビジョニングする*します)。
1. **F5** キーを押して、スクリプトを実行します。


### <a name="verify-the-deployed-set-of-tenants"></a>テナントのデプロイ済みセットを確認する 
現在、共有データベースにデプロイされたテナントと、独自のデータベースにデプロイされたテナントが混在しています。 Azure Portal を使用して、作成したデータベースを検査できます。  

* [Azure Portal](https://portal.azure.com) で、SQL サーバーの一覧を表示して **tenants1-mt-\<USER\>** サーバーを開きます。  **SQL データベース**の一覧には、**tenants1** 共有データベースと、独自データベース内のテナントのデータベースが含まれているはずです。

   ![データベースの一覧](media/saas-multitenantdb-provision-and-catalog/Databases.png)

Azure Portal でテナント データベースを表示しても、共有データベースの*内部*のテナントは表示されません。 テナントの全リストは、次のように Wingtip Tickets Events ハブ ページでカタログを閲覧すると表示できます。   

1. ブラウザーで Events Hub ページを開きます (http:events.wingtip-mt.\<USER\>.trafficmanager.net)  

   テナントの全リストとそれに対応するデータベースがカタログで確認できます。 tenantcatalog データベース内では SQL ビューを利用できます。SQL ビューでは Tenants テーブルに格納されているテナント名と、Shard Management テーブルのデータベース名が結び付けられます。 このビューは、カタログに格納されているメタデータの値を拡張するのが有用であることを示す好例です。

2. *SQL Server Management Studio (SSMS)* 内で、次の資格情報を使って **tenants1-mt.\<USER\>.database.windows.net** にあるテナント サーバーに接続します。[ログイン]: 「**developer**」、[パスワード]: 「**P@ssword1**」

    ![SSMS 接続ダイアログ](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. *オブジェクト エクスプローラー*で、*tenantcatalog* データベース内のビューを表示します。
2. *TenantsExtended* ビューを右クリックして、**[Select Top 1000 Rows]\(上位 1000 行の選択\)** を選択します。 別のテナントのテナント名とデータベースとの間のマッピングに注意してください。

    ![SSMS 内の ExtendedTenants ビュー](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)
      
## <a name="other-provisioning-patterns"></a>プロビジョニングのその他のパターン

その他の興味深いプロビジョニング パターンとしては、次のようなものがあります。

**エラスティック プールでのデータベースの事前プロビジョニング** 事前プロビジョニング パターンは、エラスティック プールを使用する際にデータベースではなくプールが課金対象となることを利用します。 そのため、追加コストを発生させることなく、データベースが必要になる前にデータベースをエラスティック プールに追加できます。 プールにデータベースを事前にプロビジョニングしておいて、必要になったら割り当てるという方法を使えば、テナントをデータベースにプロビジョニングする時間を大幅に削減できます。 事前にプロビジョニングされるデータベースの数は、予想されるプロビジョニング速度に応じて適切なバッファーを維持できるよう、必要に応じて調整できます。

**自動プロビジョニング**。 自動プロビジョニング パターンでは、サーバー、プール、データベースを必要に応じて自動でプロビジョニングするための専用のプロビジョニング サービスを使用します (このサービスでは、エラスティック プール内のデータベースの事前プロビジョニングも可能です)。 データベースの使用を停止したり、データベースを削除したりした場合には、必要に応じてこのプロビジョニング サービスによりエラスティック プール間で生じる格差が是正されます。 このようなサービスは単純なこともあれば、多くの地域にまたがるプロビジョニングを処理するなどして複雑になることもあります。また、ディザスター リカバリーのための geo レプリケーションをセットアップできます。 自動プロビジョニングのパターンではまず、クライアント アプリケーションまたはスクリプトからプロビジョニングの要求がキューに送信され、その要求がプロビジョニング サービスによって処理されます。その後、アプリケーションまたはスクリプトから、処理が完了したかどうかを確認するポーリングが実行されます。 事前プロビジョニングを使用した場合は要求がすばやく処理される一方で、バックグラウンドで実行されている代替データベースのプロビジョニングを別のサービスが管理します。



## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]

> * 1 つの新しいテナントをマルチテナント共有データベースおよび独自のデータベースにプロビジョニングする方法
> * バッチを使って追加のテナントをプロビジョニングする方法
> * テナントをプロビジョニングしてカタログに登録するまでの流れを詳しく確認する方法

[パフォーマンスの監視に関するチュートリアル](saas-multitenantdb-performance-monitoring.md)を試してください。

## <a name="additional-resources"></a>その他のリソース

<!--* Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [エラスティック データベース クライアント ライブラリ](sql-database-elastic-database-client-library.md)
* [Windows PowerShell ISE でスクリプトをデバッグする方法](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
