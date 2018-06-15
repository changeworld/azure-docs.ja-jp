---
title: エラスティック プールを使用した複数の SQL Database の管理 | Microsoft Docs
description: エラスティック プールを使用して、複数 (数百や数千) の SQL データベースを管理およびスケーリングします。 一定の価格で必要な場所にリソースを配布できます。
keywords: 複数のデータベース, データベース リソース, データベース パフォーマンス
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.date: 06/07/2018
ms.author: ninarn
ms.topic: conceptual
ms.openlocfilehash: 6e58d3ed84771cedda126511e868ad264db88606
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850493"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>エラスティック プールを利用した複数の Azure SQL Database の管理およびスケーリング

SQL Database のエラスティック プールは、予期できない多様な使用ニーズを持つ複数のデータベースを管理しスケーリングするための、シンプルでコスト効率の高いソリューションです。 エラスティック プール内のデータベースは、単一の Azure SQL Database サーバー上にあり、設定された数のリソースを設定価格で共有します。 Azure SQL Database のエラスティック プールを使用すると、SaaS 開発者は、各データベースのパフォーマンスに弾力性を提供しながら、データベース グループの価格に対するパフォーマンスを所定の予算内で最適化できます。

## <a name="what-are-sql-elastic-pools"></a>SQL エラスティック プールとは

SaaS 開発者は、複数のデータベースで構成される大規模なデータ層の上にアプリケーションを構築します。 アプリケーションの一般的なパターンでは、顧客ごとに Single Database をプロビジョニングします。 しかし、各顧客の使用パターンは変動して予測不可能な場合が多く、各データベース ユーザーのそれぞれについてリソース要件を予測することも困難です。 これまでは、2 つのオプションがありました。

- ピーク使用量および超過支払いに基づくリソースの過剰プロビジョニング
- ピーク時のパフォーマンスと顧客満足を犠牲にした、コスト削減のための過小プロビジョニング

エラスティック プールは、データベースが必要なパフォーマンス リソースを必要なときに確保できるようにすることで、この問題を解決します。 また、予測可能な予算の範囲内でシンプルなリソース割り当てメカニズムが提供されます。 エラスティック プールを使用する SaaS アプリケーションの設計パターンの詳細については、「 [Azure SQL Database を使用するマルチテナント SaaS アプリケーションの設計パターン](sql-database-design-patterns-multi-tenancy-saas-applications.md)」を参照してください。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

エラスティック プールを使用することで、開発者は、複数のデータベースで共有されるプールのリソースを購入でき、個々のデータベースの使用期間が予測しづらい場合にも対応できます。 [DTU ベースの購入モデル](sql-database-service-tiers-dtu.md)または [仮想コアベースの購入モデル (プレビュー)](sql-database-service-tiers-vcore.md) のいずれかに基づいて、プールのリソースを構成できます。 プールのリソース要件は、そのデータベースの使用量の合計によって決まります。 プールで使用可能なリソースの量は、開発者の予算に応じて決められます。 開発者は、単純にプールにデータベースを追加し、データベースに対する最小および最大のリソース (選択したリソース モデルに応じて、最小および最大の DTU、または最小および最大の仮想コアのいずれか) を設定してから、予算に基づいてプールのリソースを設定します。 開発者はプールを使用することで、リーン スタートアップの段階から成熟企業の段階に至るまで、サービスをシームレスに拡大し続けることができます。

プール内で、個々のデータベースには、設定されたパラメーターの範囲内で自動的にスケーリングを行う柔軟性が与えられます。 負荷が大きい場合、データベースはリソースの使用量を増やして需要に対応します。 負荷が小さい場合、データベースはリソースの使用量を減らし、負荷がない場合は、リソースを使用しません。 Single Database ではなく、プール全体に対してリソースをプロビジョニングすることで、管理タスクの簡略化を実現します。 さらに、プールにかかる予算を予測することができます。 データベースのダウンタイムなしで、既存のプールにさらにリソースを追加できます。ただし、新たな eDTU 予約用の追加のコンピューティング リソースを提供するためにデータベースを移動する必要がある場合は例外です。 同様に、余分なリソースが不要になった場合は、いつでも既存のプールから削除できます。 プールへのデータベースの追加、またはプールからの削除ができます。 データベースのリソース使用率が低いと予測できる場合は、プールから削除します。

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>SQL Database エラスティック プールを検討すべきとき

プールは、多数のデータベースが特定の使用パターンで使用されている場合に適しています。 あるデータベースは、使用が急増することはあまりなく、平均使用量が低いパターンの特徴を持っています。

プールに追加できるデータベースが多ければ多いほど、節約量も多くなります。 アプリケーションの使用パターンにもよりますが、S3 データベースが 2 つあれば節約を実現できます。

以下のセクションでは、特定のデータベースのコレクションをプールに追加した場合にメリットがあるかどうかを評価する方法を確認できます。 この例では Standard プールを使用していますが、同じ原則は Basic プールと Premium プールにも当てはまります。

### <a name="assessing-database-utilization-patterns"></a>データベースの使用パターンの評価

次の図では、アイドル時間は長いが、定期的にアクティビティが急増するデータベースを示しています。 このような使用パターンは、プールに適しています。

   ![a single database suitable for a pool](./media/sql-database-elastic-pool/one-database.png)

図に示した DB1 の 5 分間のピークは 90 DTU ですが、全体的な平均使用量は 5 DTU 未満です。 Single Database でこのワークロードを実行するためには S3 パフォーマンス レベルが必要ですが、このパフォーマンスを確保すると、アクティビティの少ない時間帯にほとんどのリソースが使用されなくなります。

プールを利用すれば、これらの未使用の DTU を複数のデータベースで共有できるので、必要な DTU と全体的なコストを削減できます。

DB1 と類似する使用パターンを持つデータベースが他にもあると仮定し、前の例に追加しています。 次に示す 2 つの図では、DTU ベースの購入モデルを使用して、時間が経過しても使用量のパターンが重ならないことを示すために、4 個のデータベースと 20 個のデータベースの使用量が同じグラフ上に重ねられています。

   ![four databases with a utilization pattern suitable for a pool](./media/sql-database-elastic-pool/four-databases.png)

   ![twenty databases with a utilization pattern suitable for a pool](./media/sql-database-elastic-pool/twenty-databases.png)

全 20 のデータベースの DTU 使用量合計は、前の図では黒線で示されています。 これは、DTU の合計使用量が 100 DTU を超えることはなく、20 のデータベースでこの期間にわたって 100 eDTU を共有することが可能であることを示しています。 これは、Single Database のそれぞれのパフォーマンス レベルが S3 であるときと比較した場合、DTU を 20 倍削減し価格を 13 倍下げます。

次の理由からこの例は理想的です。

* 各データベースのピーク時の使用量と、平均使用量に大きな差があります。
* 各データベースのピーク使用時間が異なります。
* eDTU は、多くのデータベース間で共有されます。

プールの価格は、プール eDTU の機能を表します。 プールの eDTU 単価は単一データベースの DTU 単価の 1.5 倍ですが、**プール eDTU は多数のデータベースで共有できるため、合計 eDTU は少なくて済みます**。 これらの価格と eDTU 共有の特徴が、プールで節約を可能にするベースとなります。

データベース数とデータベースの使用量に関連する次の経験則から、Single Database でのパフォーマンス レベルを使用した場合と比べて、プールがコスト削減に繋がることがわかります。

### <a name="minimum-number-of-databases"></a>最小数のデータベース

単一データベースのリソースの総量が、プールに必要なリソースの 1.5 倍を超える場合、エラスティック プールのコスト効率の方が高くなります。

***DTU ベースの購入モデルの例***<br>
Single Database のパフォーマンス レベルを使用した場合と比較して、100 eDTU のプールのコスト効果の方が高くなるようにするには、最低 2 個の S3 データベースまたは最低 15 個の S0 データベースが必要です。

### <a name="maximum-number-of-concurrently-peaking-databases"></a>同時にピークとなるデータベースの最大数

リソースを共有することで、プール内のすべてのデータベースが、単一のデータベースで使用可能な上限に達するまで、リソースを同時に使用できるわけではありません。 同時にピークになるデータベース数が少ないほど、プール リソースを低く設定して、よりプールのコスト効果を高めることができます。 一般的に、そのリソースの上限まで同時にピークになることができるのは、プールの 2/3 (67%) 以下のデータベースです。

***DTU ベースの購入モデルの例***<br>
200 eDTU のプール内の 3 つの S3 データベースのコストを削減する場合、同時にピークになることが許容されるのは、これらのデータベースのうちの 2 つまでです。 これら 4 つの S3 データベースの 3 つ以上が同時にピークとなる場合、プール サイズを 200 eDTU よりも大きくする必要があります。 プール サイズを 200 eDTU よりも大きくした場合、単一データベースのパフォーマンス レベルを使用した場合よりもコストを低く抑えるには、プールの S3 データベース数を増やす必要があります。

この例では、プール内の他のデータベースの使用は考慮されていないことに注意してください。 すべてのデータベースがある特定の時間にいくらか使用されている場合、同時にピークとなることができるのは、2/3 (67%) 未満のデータベースです。

### <a name="resource-utilization-per-database"></a>各データベースのリソース使用量
データベースのピーク時と平均使用時の差が大きいということは、使用量が低い時間が長く、使用量が高い時間が短いことを示します。 使用量がこのようなパターンになっている場合、リソースを複数のデータベースで共有するのが適しています。 データベースのピーク時の使用量が平均使用量よりも約 1.5 倍多い場合、プールを使用することを検討した方がよいでしょう。

***DTU ベースの購入モデルの例***<br>
S3 データベースが、ピーク時には 100 DTU を使用し、平均では 67 DTU 以下を使用する場合、eDTU をプールで共有するのが適しています。 または、S1 データベースがピーク時には 20 DTU を使用し、平均では 13 DTU 以下を使用する場合、プールが適しています。

## <a name="how-do-i-choose-the-correct-pool-size"></a>正しいプール サイズを選択するにはどうすればよいですか?

プールに最適なサイズは、プール内のすべてのデータベースに必要な合計リソースに左右されます。 これには、次の値を特定する必要があります。

* プール内のすべてのデータベースに使用される最大リソース数 (選択したリソース モデルに応じて、最大 DTU または最大仮想コアのいずれか)。
* プール内のすべてのデータベースに使用される記憶域の最大バイト数。

各リソース モデルで使用可能なサービス レベルについては、「[DTU-based purchasing model](sql-database-service-tiers-dtu.md)」(DTU ベースの購入レベル) または「[vCore-based purchasing model (preview)](sql-database-service-tiers-vcore.md)」(vCore ベースの購入モデル (プレビュー)) をご覧ください。

SQL Database は、既存の SQL Database サーバー内にあるデータベースのリソース使用量の履歴を自動的に評価し、Azure ポータルでのプールの適切な構成を推奨します。 構成を推奨するだけでなく、サーバー上にあるデータベースのカスタム グループの eDTU 使用量の見積もりが、組み込み済みの機能によって実施されます。 対話形式でのプールへのデータベースの追加やデータベースの削除を行い、変更をコミットする前にリソース使用状況分析やサイズ設定のアドバイスを入手できるため、"what-if" 分析が可能です。 その方法については、 [エラスティック プールの監視、管理、およびサイズ設定に関する記事](#monitor-an-elastic-pool-and-its-databases)を参照してください。

ツールを使用できない場合、プールのコスト効果が Single Database よりも高いかどうかを確認するには、次の手順が役立ちます。

1. プールに必要な eDTU または vCore は、次のように見積もります。

   DTU ベースの購入モデルの場合: MAX(<*DB の合計数* X *DB あたりの DTU 平均使用率*>,<br>
   <*同時にピークとなる DB の数* X *DB あたりのピーク DTU 使用率*)

   仮想コアベースの購入モデル (プレビュー) の場合: MAX(<*DB の合計数* X *DB あたりの vCore 平均使用率*>,<br>
   <*同時にピークとなる DB の数* X *DB あたりのピーク vCore 使用率*)

2. プール内のすべてのデータベースに必要なバイト数を追加することで、プールに必要なストレージ領域を見積もります。 次に、このストレージの容量を提供する eDTU プール サイズを決定します。
3. DTU ベースの購入モデルの場合、手順 1 と手順 2 の eDTU の見積もりのうち、大きい方を使用します。 仮想コアベースの購入モデル (プレビュー) の場合、手順 1 の仮想コアの見積もりを使用します。
4. 「[SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」ページを参照し、手順 3 の見積もりを超える最小のプール サイズを探します。
5. 手順 5. のプールの価格と、Single Database の適切なパフォーマンス レベルを使用した場合の価格を比較します。

## <a name="using-other-sql-database-features-with-elastic-pools"></a>エラスティック プールでのその他の SQL Database 機能の使用

### <a name="elastic-jobs-and-elastic-pools"></a>エラスティック ジョブとエラスティック プール

プールでは、**[エラスティック ジョブ](sql-database-elastic-jobs-overview.md)** でスクリプトを実行することで、管理タスクが簡素化されます。 エラスティック ジョブを使用すると、大量のデータベースに関連する面倒な作業のほとんどが不要になります。 まず、[エラスティック ジョブの概要](sql-database-elastic-jobs-getting-started.md)に関するページを参照してください。

複数のデータベースを操作するための他のデータベース ツールの詳細については、「[Azure SQL Database によるスケール アウト](sql-database-elastic-scale-introduction.md)」を参照してください。

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>エラスティック プール内のデータベースのためのビジネス継続性オプション
プールされたデータベースは、一般的に、単一データベースで使用できるのと同じ[ビジネス継続性機能](sql-database-business-continuity.md)をサポートしています。

- **ポイントインタイム リストア**: ポイントインタイム リストアは、自動データベース バックアップを使用して、プール内のデータベースを特定の時点に復旧します。 「 [ポイントインタイム リストア](sql-database-recovery-using-backups.md#point-in-time-restore)

- **geo リストア**: geo リストアは、データベースがホストされているリージョン内のインシデントのためにデータベースが使用できない場合の既定の復旧オプションを提供します。 「 [Azure SQL Database を復元する、またはセカンダリにフェールオーバーする](sql-database-disaster-recovery.md)

- **アクティブ geo レプリケーション**: geo リストアが提供できるものよりアグレッシブな復旧要件があるアプリケーションの場合は、[アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)を構成します。

## <a name="manage-elastic-pools-and-databases-using-the-azure-portal"></a>Azure Portal を使用したエラスティック プールとデータベースの管理

### <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Azure Portal を使用した新しい SQL Database エラスティック プールの作成

Azure Portal でエラスティック プールを作成できる方法には次の 2 つがあります。
1. エラスティック プールは、**Marketplace** で "**SQL エラスティック プール**" を検索するか、[SQL エラスティック プール] 参照ブレードの **[+追加]** をクリックする方法で作成できます。 このプールのプロビジョニング ワークフローでは、新しいサーバーまたは既存のサーバーを指定できます。
2. または、既存の SQL Server に移動して **[プールの作成]** をクリックし、サーバーに直接プールを作成することで、エラスティック プールを作成できます。 ここでの唯一の違いは、プールのプロビジョニング ワークフローの最中にサーバーを指定する手順をスキップすることです。

> [!NOTE]
> サーバーに複数のプールを作成することはできますが、同じプールに異なるサーバーからデータベースを追加することはできません。

プールのエラスティックに使用できる機能のほか、各データベースに使用可能な最大リソース量が、プールのサービス レベルによって決定されます。 詳細については、[DTU モデル](sql-database-dtu-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels)および [vCore モデル](sql-database-vcore-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels)でのエラスティック プールのリソース上限をご覧ください。

プールのリソースと価格を構成するには、**[プールの構成]** をクリックします。 そのため、サービス レベルを選択し、プールにデータベースを追加して、プールとそのデータベースに対するリソース上限を構成します。

プールの構成を完了したら、[適用] をクリックしてプールに名前を付け、[OK] をクリックするとプールを作成できます。

### <a name="monitor-an-elastic-pool-and-its-databases"></a>エラスティック プールとプール内のデータベースの監視

Azure Portal では、エラスティック プールとそのプール内のデータベースの使用率を監視できます。 また、エラスティック プールに対して複数の変更を行い、すべての変更を同時に送信できます。 このような変更としては、データベースの追加または削除、エラスティック プールの設定の変更、データベース設定の変更などがあります。

エラスティック プールの監視を開始するには、ポータルでエラスティック プールを検索して開きます。 最初に、エラスティック プールの状態の概要を示す画面が表示されます。 次のトピックがあります。

* エラスティック プールのリソース使用率を示す監視グラフ
* エラスティック プールに関する最近のアラートと推奨事項 (使用可能な場合)

次の図に、エラスティック プールの例を示します。

![プール ビュー](./media/sql-database-elastic-pool-manage-portal/basic.png)

プールに関するより詳細な情報を確認したい場合、この概要のいずれかの使用可能な情報をクリックできます。 **[リソース使用率]** グラフをクリックすると、グラフに示されたメトリックと時間枠をカスタマイズできる [Azure Monitoring] ビューが表示されます。 使用可能な任意の通知をクリックすると、該当のアラートや推奨事項の完全な詳細を示すブレードが表示されます。

プール内のデータベースを監視したい場合は、左側のリソース メニューの **[監視]** セクションにある **[データベース リソースの使用率]** をクリックします。

![[データベース リソース使用率] ページ](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

#### <a name="to-customize-the-chart-display"></a>グラフの表示をカスタマイズするには

グラフおよびメトリックのページを編集して、使用された CPU の割合、データ IO の割合、ログ IO の割合など、他のメトリックを表示できます。

**[グラフの編集]** フォームで、固定の時間の範囲を選択するか、または **[カスタム]** をクリックして最後の 2 週間の任意の 24 時間枠を選択してから、監視するリソースを選択できます。

#### <a name="to-select-databases-to-monitor"></a>監視するデータベースを選択するには

既定では、**[データベース リソースの使用率]** ブレードにあるグラフには、DTU または CPU 別に (使用しているサービス レベルに応じる) 上位 5 個のデータベースが表示されます。 左側にあるチェックボックスを使って、グラフの下にある一覧のデータベースをオンおよびオフにすることで、このグラフのデータベースを切り替えることができます。

また、より多くのメトリックを選択してこのデータベース テーブルで並列に表示し、データベース パフォーマンスのより詳細なビューを取得することもできます。

詳細については、[Azure Portal での SQL Database アラートの作成](sql-database-insights-alerts-portal.md)に関するトピックを参照してください。

### <a name="manage-an-elastic-pool-and-its-databases"></a>エラスティック プールとプール内のデータベースの管理

すべてのプール設定は、**[プールの構成]** ブレードという 1 つの場所で見つけることができます。 このブレードを表示するには、ポータルでエラスティック プールを検索し、ブレードの上部または左側のリソース メニューのいずれかから **[プールの構成]** をクリックします。

このブレードから、以下のような変更を任意に組み合わせて行い、そららの変更すべてを 1 回にまとめて保存できます。
1. プールのサービス レベルの変更
2. パフォーマンス (DTU または vCores) およびストレージをスケール アップまたはスケール ダウンする
3. プールに対してデータベースを追加または削除する
4. プール内のデータベースの最小 (保証される) および最大のパフォーマンスを設定する
5. 新しい選択の結果としての請求書への変更を表示するコストの概要を確認する

![エラスティック プールの構成のブレード](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="manage-elastic-pools-and-databases-using-powershell"></a>PowerShell を使用したエラスティック プールとデータベースの管理

Azure PowerShell を使用して SQL Database エラスティック プールを作成および管理するには、次の PowerShell コマンドレットを使用します。 PowerShell をインストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 データベース、サーバー、およびファイアウォール規則を作成および管理するには、「[Create and manage Azure SQL Database servers and databases using PowerShell (PowerShell を使用して Azure SQL Database サーバーおよびデータベースを作成および管理する)](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-powershell)」を参照してください。

> [!TIP]
> PowerShell のサンプル スクリプトについては、「[Create elastic pools and move databases between pools and out of a pool using PowerShell (エラスティック プールを作成し、PowerShell を使用してデータベースをプール間で、およびプールから外に移動する)](scripts/sql-database-move-database-between-pools-powershell.md)」および「[Use PowerShell to monitor and scale a SQL elastic pool in Azure SQL Database (PowerShell を使用して、Azure SQL Database 内の SQL エラスティック プールを監視およびスケーリングする)](scripts/sql-database-monitor-and-scale-pool-powershell.md)」を参照してください。
>

| コマンドレット | 説明 |
| --- | --- |
|[New-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/new-azurermsqlelasticpool)|論理 SQL サーバー上にエラスティック データベース プールを作成します。|
|[Get-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/get-azurermsqlelasticpool)|論理 SQL サーバー上のエラスティック プールとそのプロパティ値を取得します。|
|[Set-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)|論理 SQL サーバー上のエラスティック データベース プールのプロパティを変更します。 たとえば、**StorageMB** プロパティを使用して、エラスティック プールの最大容量を変更します。|
|[Remove-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/remove-azurermsqlelasticpool)|論理 SQL サーバー上のエラスティック データベース プールを削除します。|
|[Get-AzureRmSqlElasticPoolActivity](/powershell/module/azurerm.sql/get-azurermsqlelasticpoolactivity)|論理 SQL サーバー上のエラスティック プールに対する操作の状態を取得します。|
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|新しいデータベースを既存のプール内に、または単一のデータベースとして作成します。 |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|1 つまたは複数のデータベースを取得します。|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|データベースのプロパティを設定するか、または既存のデータベースをエラスティック プール内に、エラスティックから外に、またはエラスティック間で移動します。|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|データベースを削除します。|


> [!TIP]
> ポータルまたは PowerShell コマンドレットで一度に作成できるデータベースは 1 つのみであるため、エラスティック プールに多数のデータベースを作成しようとすると時間がかかることがあります。 エラスティック プールへの作成を自動化するには、「[CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae)」を参照してください。
>

## <a name="manage-elastic-pools-and-databases-using-the-azure-cli"></a>Azure CLI を使用したエラスティック プールとデータベースの管理


  [Azure CLI](/cli/azure) を使用して SQL Database エラスティック プールを作成および管理するには、次の [Azure CLI SQL Database](/cli/azure/sql/db) コマンドを使用します。 [Cloud Shell](/azure/cloud-shell/overview) を使用して CLI をブラウザーで実行することも、macOS、Linux、または Windows に[インストール](/cli/azure/install-azure-cli)することもできます。

> [!TIP]
> Azure CLI のサンプル スクリプトについては、「[Use CLI to move an Azure SQL database in a SQL elastic pool (CLI を使用して、SQL エラスティック プール内の Azure SQL データベースを移動する)](scripts/sql-database-move-database-between-pools-cli.md)」および「[Use Azure CLI to scale a SQL elastic pool in Azure SQL Database (Azure CLI を使用して、Azure SQL Database 内の SQL エラスティック プールをスケーリングする)](scripts/sql-database-scale-pool-cli.md)」を参照してください。
>

| コマンドレット | 説明 |
| --- | --- |
|[az sql elastic-pool create](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create)|エラスティック プールを作成します。|
|[az sql elastic-pool list](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list)|サーバー内のエラスティック プールの一覧を返します。|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_dbs)|エラスティック プール内のデータベースの一覧を返します。|
|[az sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_editions)|使用可能なプール DTU の設定、ストレージ制限、およびデータベースごとの設定も含まれます。 詳細度を減らすために、既定では、追加のストレージ制限とデータベースごとの設定は非表示になります。|
|[az sql elastic-pool update](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update)|エラスティック プールを更新します。|
|[az sql elastic-pool delete](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_delete)|エラスティック プールを削除します。|

## <a name="manage-databases-within-elastic-pools-using-transact-sql"></a>Transact-SQL を使用したエラスティック プール内のデータベースの管理

Transact-SQL を使用して既存のエラスティック プール内のデータベースを作成および移動するか、または SQL Database エラスティック プールに関する情報を返すには、次の T-SQL コマンドを使用します。 これらのコマンドは、Azure Portal、[SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio)、[Visual Studio Code](https://code.visualstudio.com/docs)、または Azure SQL Database サーバーに接続して Transact-SQL コマンドを渡すことができるその他のプログラムを使用して実行できます。 データベース、サーバー、およびファイアウォール規則を作成および管理するには、「[Create and manage Azure SQL Database servers and databases using Transact-SQL (Transact-SQL を使用して Azure SQL Database サーバーおよびデータベースを作成および管理する)](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-transact-sql)」を参照してください。

> [!IMPORTANT]
> Transact-SQL を使用して Azure SQL Database エラスティック プールを作成、更新、または削除するすることはできません。 データベースをエラスティック プールに追加または削除したり、DMV を使用して既存のエラスティック プールに関する情報を返したりすることができます。
>

| コマンド | 説明 |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|新しいデータベースを既存のプール内に、または単一のデータベースとして作成します。 新しいデータベースを作成するには、マスター データベースに接続する必要があります。|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |データベースをエラスティック プール内に、エラスティックから外に、またはエラスティック間で移動します。|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|データベースを削除します。|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|論理サーバー内のすべてのエラスティック データベース プールのリソース使用状況の統計を返します。 各エラスティック データベース プールについて、15 秒のレポート ウィンドウごとに 1 行 (1 分あたり 4 行) が存在します。 これには、プール内のすべてのデータベースごとの CPU、I/O、ログ、ストレージ消費、および同時実行要求/セッション使用率が含まれます。|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Azure SQL Database または Azure SQL Data Warehouse のエディション (サービス レベル)、サービス目標 (価格レベル)、およびエラスティック プール名 (存在する場合) を返します。 Azure SQL Database サーバーの master データベースにログオンしている場合は、すべてのデータベースの情報が返されます。 Azure SQL Data Warehouse の場合は、master データベースに接続する必要があります。|

## <a name="manage-elastic-pools-and-databases-using-the-rest-api"></a>REST API を使用したエラスティック プールとデータベースの管理

SQL Database エラスティック プールを作成して管理するには、これらの REST API 要求を使用します。

| コマンド | 説明 |
| --- | --- |
|
  [エラスティック プール - 作成または更新](/rest/api/sql/elasticpools/createorupdate)|新しいエラスティック プールを作成するか、既存のエラスティック プールを更新します。|
|
  [エラスティック プール - 削除](/rest/api/sql/elasticpools/delete)|エラスティック プールを削除します。|
|
  [エラスティック プール - 取得](/rest/api/sql/elasticpools/get)|エラスティック プールを取得します。|
|[ - サーバーごとの一覧取得](/rest/api/sql/elasticpools/listbyserver)|サーバー内のエラスティック プールの一覧を返します。|
|
  [エラスティック プール - 更新](/rest/api/sql/elasticpools/update)|既存のエラスティック プールを更新します。|
|
  [推奨されるエラスティック プール - 取得](/rest/api/sql/recommendedelasticpools/get)|推奨されるエラスティック プールを取得します。|
|
  [推奨されるエラスティック プール - サーバーごとの一覧取得](/rest/api/sql/recommendedelasticpools/listbyserver)|推奨されるエラスティック プールを返します。|
|
  [推奨されるエラスティック プール - メトリックの一覧取得](/rest/api/sql/recommendedelasticpools/listmetrics)|推奨されるエラスティック プールのメトリックを返します。|
|
  [エラスティック プールのアクティビティ](/rest/api/sql/elasticpoolactivities)|エラスティック プールのアクティビティを返します。|
|
  [エラスティック プール データベースのアクティビティ](/rest/api/sql/elasticpooldatabaseactivities)|エラスティック プール内のデータベースのアクティビティを返します。|
|[データベース - 作成または更新](/rest/api/sql/databases/createorupdate)|新しいデータベースを作成するか、既存のデータベースを更新します。|
|[データベース - 取得](/rest/api/sql/databases/get)|データベースを取得します。|
|
  [データベース - エラスティック プールごとに取得](/rest/api/sql/databases/getbyelasticpool)|エラスティック プール内のデータベースを取得します。|
|
  [データベース - 推奨されるエラスティック プールごとに取得](/rest/api/sql/databases/getbyrecommendedelasticpool)|推奨されるエラスティック プール内のデータベースを取得します。|
|
  [データベース - エラスティック プールごとの一覧取得](/rest/api/sql/databases/listbyelasticpool)|エラスティック プール内のデータベースの一覧を返します。|
|
  [データベース - 推奨されるエラスティック プールごとの一覧取得](/rest/api/sql/databases/listbyrecommendedelasticpool)|推奨されるエラスティック プール内のデータベースの一覧を返します。|
|[データベース - サーバーごとの一覧取得](/rest/api/sql/databases/listbyserver)|サーバー内のデータベースの一覧を返します。|
|[データベース - 更新](/rest/api/sql/databases/update)|既存のデータベースを更新します。|

## <a name="next-steps"></a>次の手順

* ビデオについては、[Azure SQL Database のエラスティック機能に関する Microsoft Virtual Academy のビデオ コース](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)をご覧ください。
* エラスティック プールを使用する SaaS アプリケーションの設計パターンの詳細については、「 [Azure SQL Database を使用するマルチテナント SaaS アプリケーションの設計パターン](sql-database-design-patterns-multi-tenancy-saas-applications.md)」を参照してください。
* エラスティック プールを使用した SaaS チュートリアルについては、「[Introduction to the Wingtip SaaS application (Wingtip SaaS アプリケーションの概要)](sql-database-wtp-overview.md)」を参照してください。
