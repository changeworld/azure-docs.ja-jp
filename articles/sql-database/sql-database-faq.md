---
title: Azure SQL Database に関する FAQ | Microsoft Docs
description: クラウド データベースと Azure SQL Database、Microsoft のリレーショナル データベース管理システム (RDBMS)、およびクラウド内のサービスとしてのデータベースのよくある質問に対する回答です。
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: conceptual
ms.date: 07/27/2018
ms.author: carlrab
ms.openlocfilehash: 6c43a52309a6f41d73ead19fc5f5b7df2f06d6f6
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39308815"
---
# <a name="sql-database-faq"></a>SQL Database に関する FAQ

## <a name="what-is-the-current-version-of-sql-database"></a>SQL Database の現在のバージョンは何ですか。
SQL Database の現在のバージョンは V12 です。 バージョン V11 は廃止されました。

## <a name="what-is-the-sla-for-sql-database"></a>SQL Database の SLA はどうなっていますか。
Microsoft は、サービス レベルに関係なく、お使いの Microsoft Azure SQL Database と Microsoft のインターネット ゲートウェイの間で 99.99% 以上の時間接続できることを保証します。 詳細については、[SLA](http://azure.microsoft.com/support/legal/sla/) のページを参照してください。

## <a name="what-is-the-new-vcore-based-purchasing-model-for-azure-sql-database"></a>Azure SQL Database の新しい仮想コアベースの購入モデルとは何ですか。

新しい購入モデルは、既存の DTU ベースのモデルに追加されるものです。 仮想コアベースのモデルは、お客様に柔軟性、管理性、透明性が実現するように設計されており、オンプレミスのワークロード要件をクラウドに容易に移行できます。 また、お客様が自身のワークロードのニーズに基づいて、コンピューティングとストレージのリソースをスケーリングすることができます。 さらに、仮想コア モデルを使用する単一データベースと Elastic Pool のオプションは、[SQL Server 向け Azure ハイブリッド使用特典](../virtual-machines/windows/hybrid-use-benefit-licensing.md)によって、最大 30% の割引対象になります。 詳細については、[DTU ベースの購入モデル](sql-database-service-tiers-dtu.md)と[仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)に関する記事を参照してください。 

## <a name="what-is-a-vcore"></a>仮想コアとは何ですか。 
仮想コアは、ハードウェアの世代の選択が可能な論理 CPU を表します。 Gen 4 論理 CPU は、Intel E5-2673 v3 (Haswell) 2.4 GHz プロセッサをベースにしています。Gen 5 論理 CPU は、Intel E5-2673 v4 (Broadwell) 2.3 GHz プロセッサをベースにしています。

## <a name="is-moving-to-the-vcore-based-model-required"></a>仮想コアベースのモデルへの移行は必須ですか。
いいえ。Elastic Pool と単一データベースのデプロイ オプションへの仮想コアベース モデルの導入は、Microsoft によるお客様への選択肢と柔軟性の提供に対するコミットメントを反映しています。 お客様が DTU ベースのモデルを引き続き使用したいと考えている場合は、このお知らせによって、特別な対応は必要ありません。お客様は現在のエクスペリエンスをこれまでどおりの料金でお使いいただけます。 

多くの場合、アプリケーションは、事前構成済みリソース バンドルのシンプルさからメリットを得られます。 したがって、Microsoft では、これらの DTU ベースのオプションを引き続きお客様に提供し、サポートしています。 このオプションをご使用中で、それがご自分のビジネス要件を満たしている場合は、このまま使用し続けてください。

DTU ベースのモデルと仮想コアベースのモデルは並行して提供されます。 Microsoft が仮想コアベースのモデルの提供を開始するのは、データベース リソースに関する透明性の向上と、コンピューティングとストレージのリソーのスケーリングを個別に行う機能を求めるお客様の要望に応えるためです。 また、アクティブなソフトウェア アシュアランスを利用しているお客様は、仮想コアベースのモデルを利用すると、SQL Server 向けの Azure ハイブリッド特典を通じてさらに節約できます。

## <a name="how-should-i-choose-between-the-dtu-based-purchasing-model-vs-the-vcore-based-purchasing-model"></a>DTU ベースの購入モデルと仮想コアベースの購入モデルのどちらにするかは、どう選択すればよいですか。 
データベース トランザクション ユニット (DTU) は、CPU、メモリ、読み取り、および書き込みの測定値を組み合わせて算出されます。 DTU ベースのパフォーマンス レベルは、さまざまなレベルのアプリケーション パフォーマンスを実現するリソースの構成済みバンドルを表します。 基盤となるリソースを気にすることなく、毎月固定料金を支払いながら、構成済みバンドルのシンプルさを求めるお客様については、おそらく DTU ベースのモデルの方がニーズに適しています。 ただし、お客様が、基盤となるリソースに関して現在以上の分析情報を必要とする場合、またはそれらのリソースを個別にスケーリングして最適なパフォーマンスを実現する必要がある場合は、仮想コアベースのモデルが最適な選択肢です。  さらに、SQL Server のアクティブなソフトウェア アシュアランス (SA) を所有しているお客様は、[SQL Server 向け Azure ハイブリッド使用特典](../virtual-machines/windows/hybrid-use-benefit-licensing.md)で既存の投資を活用して、最大 30% を節約できます。  それぞれの購入モデルのオプションにより、自動化されたバックアップ、ソフトウェア更新プログラム、パッチなど、完全に管理されたサービスのメリットが得られます。 

## <a name="what-is-the-azure-hybrid-benefit-for-sql-server"></a>SQL Server 向け Azure ハイブリッド特典とは何ですか。 
[SQL Server 向け Azure ハイブリッド使用特典](../virtual-machines/windows/hybrid-use-benefit-licensing.md)は、既存のライセンス投資価値を最大限に高め、クラウドへの移行を促進するうえで役立ちます。 SQL Server 向け Azure ハイブリッド特典は、Azure ベースの特典であり、ソフトウェア アシュアランス付きの SQL Server ライセンスを使用して、SQL Database の支払いに割引料金 ("基本料金") を適用できます。 SQL Server 向け Azure ハイブリッド特典は、仮想コアベースの購入モデル (SQL Database の単一データベースおよびエラスティック プール) のパブリック プレビューでご利用いただけます。 この特典は、SKU がアクティブな場合でも適用できます。ただし、基本料金が適用されるのは、Azure Portal で選択した時点からである点にご注意ください。 以前にさかのぼってクレジットが発行されることはありません。

## <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>SQL Server 向け Azure ハイブリッド特典では、二重使用権がサポートされていますか。
確実かつシームレスに移行を行うために、180 日間はライセンスの二重使用権があります。 この 180 日を過ぎると、SQL Server ライセンスは、SQL Database のクラウドでしか使用できません。オンプレミスとクラウドでの二重使用権はなくなります。


## <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>SQL Server 向け Azure ハイブリッド特典とライセンス モビリティの違いは何ですか。
現在、Microsoft では、SQL Server のお客様に、ソフトウェア アシュアランスのライセンス モビリティ特典を提供しています。これにより、サード パーティ共有サーバーへのライセンスの再割り当てが可能になります。 この特典は、Azure IaaS および AWS EC2 でご利用いただけます。
SQL Server 向け Azure ハイブリッド特典は、次の 2 つの重要な点において、ライセンス モビリティとは異なります。
- 高度に仮想化されたワークロードを Azure に移動することで、コスト面でメリットがもたらされます。 SQL EE のお客様は、高度に仮想化されたアプリケーション用にオンプレミスで所有するコアごとに、Azure の General Purpose SKU で 4 つのコアを取得できます。 ライセンス モビリティの場合、仮想化されたワークロードをクラウドに移動しても、コスト上のメリットは特にありません。
- オンプレミスの SQL Server と高い互換性がある PaaS 移行先を Azure (SQL Database マネージド インスタンス) に提供します。

## <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>SQL Server 向け Azure ハイブリッド特典では、具体的にはどのような権限が付与されますか。
SQL Database のお客様には、SQL Server 向け Azure ハイブリッド特典によって次の権限が付与されます。

|ライセンス フットプリント|SQL Server 向け Azure ハイブリッド特典の内容|
|---|---|
|SA を含む SQL Server Enterprise Edition の中核的なお客様|<li>General Purpose SKU または Business Critical SKU のいずれかで基本料金を支払うことができます</li><br><li>オンプレミスの 1 コア = General Purpose SKU の 4 コア</li><br><li>オンプレミスの 1 コア = Business Critical SKU の 1 コア</li>|
|SA を含む SQL Server Standard Edition の中核的なお客様|<li>General Purpose SKU のみで基本料金を支払うことができます</li><br><li>オンプレミスの 1 コア = General Purpose SKU の 1 コア</li>|
|||

## <a name="is-the-vcore-based-model-available-to-sql-database-managed-instance"></a>仮想コアベースのモデルは、SQL Database Managed Instance で利用できますか。
仮想コアベースのモデルでのみ、[マネージド インスタンス](sql-database-managed-instance.md)をご利用いただけます。 詳細については、[SQL Database の価格のページ](https://azure.microsoft.com/pricing/details/sql-database/managed/)をご覧ください。 

## <a name="does-the-cost-of-compute-and-storage-depend-on-the-service-tier-that-i-choose"></a>コンピューティングとストレージのコストは、選択したサービス レベルによって決まりますか。
コンピューティング コストには、アプリケーションに対してプロビジョニングされたコンピューティング能力の合計が反映されています。 Business Critical サービス レベルでは、少なくとも 3 つの Always On レプリカが自動的に割り当てられます。 追加で割り当てられたこのコンピューティング リソースを反映するために、仮想コアの価格は、Business Critical では約 2.7 倍高くなっています。 同じ理由で、Business Critical レベルでは GB あたりのストレージ価格も高く、これには SSD ストレージの高 IO と低遅延が反映されています。 一方、バックアップ ストレージについては、両方のケースで Standard Storage クラスが使用されているため、コストは変わりません。

## <a name="how-am-i-charged-for-storage---based-on-what-i-configure-upfront-or-on-what-the-database-uses"></a>ストレージに対する課金はどのように行われますか。事前に構成した内容に基づきますか。あるいはデータベースによって使用されたものに基づきますか。
ストレージの種類によって課金の方法は異なります。 データ ストレージの場合、選択したデータベースまたはプールの最大サイズに基づいて、プロビジョニングされたストレージに対して課金されます。 最大値を増減しない限り、コストは変わりません。 バックアップ ストレージは、インスタンスの自動バックアップに関連付けられています。 バックアップのリテンション期間を長くすると、ご自分のインスタンスで使用されるバックアップ ストレージが増えます。 プロビジョニングされたサーバー ストレージ全体の 100 パーセントまでのバックアップ ストレージに対しては、追加料金がかかりません。 バックアップ ストレージの超過使用分については、月ごとに GB 単位で請求されます。 たとえば、データベースのストレージ サイズが 100 GB の場合、追加費用なしで 100 GB のバックアップを利用できます。 しかし、バックアップが 110 GB になると、10 GB の超過分について支払いが発生します。

単一データベースのバックアップ ストレージについては、データベース バックアップに割り当てられたストレージからデータベース サイズが引かれた分に対して按分計算で課金されます。 エラスティック プールのバックアップ ストレージについては、プール内のすべてのデータベースのデータベース バックアップに割り当てられたストレージから、エラスティック プールの最大データ サイズが引かれた分に対して按分計算で課金されます。 データベース サイズまたはエラスティック プールが増加するか、トランザクション率が増加すると、より多くのストレージが必要になるため、バックアップ ストレージの請求も増えます。  データの最大サイズを大きくすると、この新しく増加した分は請求済みバックアップ ストレージ サイズから差し引かれます。

## <a name="how-do-i-select-the-right-sku-when-converting-an-existing-database-to-the-new-service-tiers"></a>既存のデータベースを新しいサービス レベルに変換するとき、適切な SKU を選択するにはどうすればよいですか。 
DTU ベースのモデルを使用する既存の SQL Database アプリケーションでは、General Purpose サービス レベルは Standard レベルと同等です。 Business Critical サービス レベルは Premium レベルと同等です。 いずれの場合も、DTU ベースのモデルでご自身のアプリケーションによって使用されている 100 DTU ごとに、少なくとも 1 つの仮想コアを割り当てる必要があります。

## <a name="do-the-new-vcore-based-service-tiers-offer-the-performance-levels-compatible-with-all-existing-service-level-objectives-slos"></a>新しい仮想コアベースのサービス レベルでは、すべての既存サービス レベル目標 (SLO) と互換性のあるパフォーマンス レベルが提供されますか。
新しい仮想コアベースのサービス レベルには、100 DTU 以上を使用するすべてのエラスティック プールおよびデータベースと同等のパフォーマンス オプションが用意されています。  Microsoft では、引き続き徐々に SLO を追加して、100 未満の DTU ワークロードに対応できるようにする予定です。

## <a name="are-there-any-database-feature-differences-between-the-existing-dtu-based-and-new-vcore-based-service-tiers"></a>既存の DTU ベースのサービス レベルと新しい仮想コアベースのサービス レベルの間にデータベース機能の違いはありますか。 
新しいサービス レベルでは、現在の DTU ベースのサービスで使用できる機能のスーパーセットがサポートされます。 追加機能には、一連の追加の動的管理ビュー (DMV) と追加のリソース構成オプションが含まれます。 

## <a name="if-my-database-is-cpu-bound-and-does-not-use-much-storage-can-i-increase-the-compute-without-paying-for-extra-storage"></a>データベースが CPU 依存で、ストレージをそれほど使用しない場合、余分なストレージ コストをかけずにコンピューティング能力を向上させることはできますか。
はい。アプリケーションに必要なコンピューティング レベルは個別に選択できます。ストレージを変更する必要はありません。 ストレージの最小サイズは 32 GB に設定できます。 

## <a name="will-the-new-vcore-based-tiers-support-point-in-time-restore-pitr-for-35-days-as-today"></a>新しい仮想コアベースのレベルでは、現在のように 35 日間のポイントインタイム リストア (PITR) がサポートされていますか。 
PITR のバックアップ リテンション期間は 7 ～ 35 日の間で構成できます。 バックアップ ストレージは、最大データ サイズと同じストレージ容量を超えた場合に、実際のストレージ使用量に基づいて別個に課金されます。 プレビューでは、既定の PITR リテンション期間は 7 日に設定されています。 多くの場合、最大データ サイズで十分に 7 日分のバックアップを格納できます。

## <a name="why-do-you-allow-selection-of-the-hardware-generation-for-compute"></a>コンピューティング用にハードウェア世代を選択できるのはなぜですか。
Microsoft の目標は、アプリケーションのニーズに近いパフォーマンス構成を選択できるように最大限の柔軟性を実現することです。 Gen4 ハードウェアでは、仮想コアあたり大幅に多くのメモリが提供されます。 一方、Gen5 ハードウェアでは、コンピューティング リソースをはるかに高くまでスケールアップできます。 詳細については、[仮想コアの購入モデル](sql-database-service-tiers-vcore.md)を参照してください。

## <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-database-to-a-vcore-based-service-tier"></a>DTU ベースのデータベースから仮想コアベースのサービス レベルに変更するには、アプリケーションをオフラインにする必要がありますか。 
新しいサービス レベルでは、シンプルなオンラインの変換方法が提供されます。これは、Standard と Premium のサービス レベルの間でデータベースを切り替える既存のプロセスと同様です。 この変換は、Azure ポータル、PowerShell、Azure CLI、T-SQL、または REST API を使用して開始できます。 [単一データベースの管理](sql-database-single-database-scale.md)および[エラスティック プールの管理](sql-database-elastic-pool.md)に関するページをご覧ください。

## <a name="can-i-convert-a-database-from-a-vcore-based-service-tier-to-a-dtu-based-one"></a>仮想コアベースのサービス レベルから DTU ベースのサービス レベルにデータベースを変換できますか。 
はい。お使いのデータベースは、Azure portal、PowerShell、Azure CLI、T-SQL、REST API のいずれかで、サポートされている任意のパフォーマンス目標に簡単に変換できます。 [単一データベースの管理](sql-database-single-database-scale.md)および[エラスティック プールの管理](sql-database-elastic-pool.md)に関するページをご覧ください。

## <a name="can-i-upgrade-or-downgrade-between-the-general-purpose-and-business-critical-service-tiers"></a>General Purpose サービス レベルと Business Critical サービス レベルの間で、アップグレードまたはダウングレードできますか。 
はい。ただし、制限がいくつかあります。 使用している既存のデプロイに対して構成した、データベースまたはエラスティック プールの最大サイズを、移行先 SKU が満たす必要があります。 [SQL Server 向け Azure ハイブリッド使用特典](../virtual-machines/windows/hybrid-use-benefit-licensing.md)を使用している場合、Business Critical SKU をご利用いただけるのは、Enterprise Edition ライセンスをお持ちのお客様のみです。 Enterprise Edition ライセンスを持つお客様が、SQL Server 向け Azure ハイブリッド特典を使用してオンプレミスから General Purpose に移行した場合にのみ、Business Critical にアップグレードできます。 詳細については、[SQL Server 向け Azure ハイブリッド使用特典の具体的な権限](../virtual-machines/windows/hybrid-use-benefit-licensing.md)に関するページをご覧ください。

この変換によるダウンタイムはありません。また、Azure portal、PowerShell、Azure CLI、T-SQL、または REST API を使用して開始できます。 [単一データベースの管理](sql-database-single-database-scale.md)および[エラスティック プールの管理](sql-database-elastic-pool.md)に関するページをご覧ください。

## <a name="i-am-using-a-premium-rs-database-that-will-not-be-generally-available---can-i-upgrade-it-to-a-new-tier-and-achieve-a-similar-priceperformance-benefit"></a>一般公開されない Premium RS データベースを使用しています。これを新しいレベルにアップグレードして、同様の価格/パフォーマンス上のメリットを得ることができますか。
仮想コアモデルでは、プロビジョニングされたコンピューティングとストレージの量を個別に制御でき、結果として生じるコストをより効率的に管理できるため、Premium RS データベースの魅力的な移行先となっています。 さらに、仮想コアベースのモデルを使用する場合、[SQL Server 向け Azure ハイブリッド使用特典](../virtual-machines/windows/hybrid-use-benefit-licensing.md)により大幅な割引を受けることができます。 

## <a name="how-often-can-i-adjust-the-resources-per-pool"></a>各プールのリソースはどのくらいの頻度で調整できますか。
頻度に制限はありません。必要なだけ調整できます。 [エラスティック プールの管理](sql-database-elastic-pool.md)に関するページをご覧ください。

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-pool"></a>Single Database のサービス階層またはパフォーマンス レベルの変更や、エラスティック プールからのデータベースの出し入れには、どれくらいの時間がかかりますか。
データベースのサービス階層の変更やそれのプールからの出し入れでは、バック グラウンド操作でプラットフォームにデータベースをコピーする必要があります。 サービス階層の変更は、データベースのサイズに応じて数分から数時間かかることがあります。 いずれの場合も、データベースはオンラインを維持し、移動中も使用できます。 Single Database の変更の詳細については、「 [データベースのサービス階層の変更](sql-database-service-tiers-dtu.md)」を参照してください。 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>Single Database と Elastic Database はどのように使い分けできますか。
一般に、エラスティック プールは、典型的な[サービスとしてのソフトウェア (SaaS) アプリケーションのパターン](sql-database-design-patterns-multi-tenancy-saas-applications.md)用に設計されており、データベースは顧客またはテナントごとに 1 つです。 個々のデータベースを購入し、さまざまな、またはピーク時の需要に合わせてオーバープロビジョニングすることはコスト効率がよくありません。 プールを使用すると、プールでパフォーマンスは全体的に管理され、データベースは自動的にスケールアップおよびダウンされます。 使用パターンから利益があると判断される場合、Azure インテリジェント エンジンはデータベースにプールを推奨します。 詳しくは、「[エラスティック プールの使用に適した状況](sql-database-elastic-pool.md)」をご覧ください。

## <a name="how-does-the-usage-of-sql-database-using-the-dtu-based-purchasing-model-show-up-on-my-bill"></a>DTU ベースの購入モデルを使用した SQL Database の使用量は請求書にどのように表示されますか。
SQL Database は、[購入モデル](sql-database-service-tiers-dtu.md)に基づいて、予測可能な時間あたりのレートで課金されます。 実際の使用量は時間単位でコンピューティングされるため、表示される請求額は 1 時間分に満たない場合があります。 たとえば、1 か月間にデータベースが存在したのが 12 時間だった場合、請求書には半日分の使用が示されます。 

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>Single Database がアクティブであったのが 1 時間に満たない場合や上位のサービス階層の使用が 1 時間に満たない場合はどうなりますか。
使用状況やデータベースがアクティブであったのが 1 時間未満であったことに関係なく、データベースが存在していた 1 時間単位で、その時間に使用された最上位のサービス階層とパフォーマンス レベルで課金は行われます。 たとえば、Single Database を作成し、それを 5 分後に削除した場合、請求書にはデータベース時間として 1 時間の請求が表示されます。 

次に例を示します。

* Basic データベースを作成し、それをすぐに Standard S1 にアップグレードした場合、最初の 1 時間分として Standard S1 のレートが課金されます。
* データベースを午後 10 時 00 分に Basic から Premium にアップグレードし始めて、 午前 1 時 35 分にアップグレードが完了した場合、 翌日の午前 1 時 00 分から Premium 料金での課金が始まります。 
* 午前 11 時 00 分に、データベースを Premium から Basic にダウングレードし始めて、 午後 2 時 15 分に完了した場合、Premium の料金が課金されるのは午後 3 時 00 分までで、それ以降は Basic の料金が課金されます。

## <a name="how-does-elastic-pool-usage-using-the-dtu-based-purchasing-model-show-up-on-my-bill"></a>DTU ベースの購入モデルを使用したエラスティック プールの使用量は請求書にどのように表示されますか。
エラスティック プールの料金は、[価格ページ](https://azure.microsoft.com/pricing/details/sql-database/)に記載の単位に従い、請求書にエラスティック DTU (eDTU) または仮想コア、およびストレージとして表示されます。 エラスティック プールでは、データベース単位の請求はありません。 課金は、使用量やプールがアクティブであったのが 1 時間に満たないということに関係なく、プールが存在していた時間の最上位の eDTU または仮想コアを使用して 1 時間単位で行われます。 

DTU ベースの購入モデルの例:

* 午前 11 時 18 分に 200 eDTU の Standard エラスティック プールを作成し、プールに 5 つのデータベースを追加した場合、午前 11 時から始まって、その日中は 1 時間 200 eDTU が 課金されます。
* 2 日目には、データベース 1 が午前 5 時 5 分から 50 eDTU の消費を開始し、その日はそれが維持されます。 データベース 2 ～ 5 は、0 ～ 80 eDTU の間で変動します。 その日、その 1 日間異なる eDTU を消費するデータベースが他に 5 つ追加されました。 2 日目は、丸 1 日 200 eDTU が課金されます。 
* 3 日目の午前 5 時に、 さらにデータベースが 15 追加されました。 1 日中データベースの使用量は増え、午後 8 時 5 分にプールの eDTU を 200 から 400 に増やすことに決めました。 午後 8 時まで 200 eDTU レベルで課金され、残りの 4 時間は 400 eDTU に増やされました。 

## <a name="how-are-elastic-pool-billed-for-the-dtu-based-purchasing-model"></a>DTU ベースの購入モデルの場合、エラスティック プールはどのように課金されますか。
エラスティック プールは、次の特性ごとに課金されます。

* エラスティック プールは、プールにデータベースがない場合でも、その作成時に課金されます。
* エラスティック プールは 1 時間ごとに課金されます。 これは、シングル データベースのパフォーマンス レベルと同じ使用状況測定の頻度です。
* エラスティック プールのサイズが変更された場合、サイズ変更の操作が完了するまでは新しいリソースの量に応じた課金はされません。 これは、Single Database のパフォーマンス レベルを変更する場合と同様のパターンに従っています。
* エラスティック プールの価格は、プールのリソースに基づきます。 エラスティック プールの価格は、内部のエラスティック データベースの数および使用率とは関係ありません。

詳細については、[SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)、[DTU ベースの購入モデル](sql-database-service-tiers-dtu.md)、および[仮想コア ベースの購入モデル](sql-database-service-tiers-vcore.md)に関する記事を参照してください。

## <a name="how-does-the-vcore-based-usage-show-up-in-my-bill"></a>請求書に仮想コアベースの使用量はどのように表示されますか。 
仮想コアベース モデルのサービスは、サービス レベル、プロビジョニングされたコンピューティング (仮想コア)、プロビジョニングされたストレージ (GB/月)、バックアップ ストレージの使用量に基づいて、予測可能な時間単位の料金で課金されます。 バックアップのストレージがデータベースの合計サイズ (データベース サイズの 100%) を上回ると、追加の料金が発生します。 請求書には仮想コアの時間数、構成したデータベース ストレージ、使用した IO およびバックアップ ストレージが項目別で明確に記載されるため、使用したリソースの詳細がわかりやすくなっています。 最大データベース サイズの 100% までのバックアップ ストレージの使用は、料金に含まれています。この上限を超えると、1 か月の使用量 (GB/月) に応じた課金が発生します。

例: 
- SQL データベースが 1 か月に 12 時間存在した場合、請求書には 12 時間の仮想コアが使用量として記載されます。 この SQL データベースで 100 GB のストレージを追加プロビジョニングした場合、請求書には、時間割りで計算された GB/月単位のストレージ使用量と 1 か月に使用した IO の数が記載されます。
- SQL データベースのアクティブ期間が 1 時間未満の場合でも、指定のサービス レベルのうちで最も高いものと、この時間内に適用されたプロビジョニング ストレージおよび IO を使用したデータベースが存在した時間ごとに課金されます。使用量や、データベースのアクティブ期間が 1 時間未満であったかどうかは加味されません。
- マネージド インスタンスを作成し、その 5 分後にそのマネージド インスタンスを削除した場合、1 データベース時間分の料金が課金されます。
- 8 個の仮想コアを使用する General Purpose レベルのマネージド インスタンスを作成し、それをすぐに 16 個の仮想コアにアップグレードした場合、最初の 1 時間分の課金では 16 個の仮想コアのレートが使用されます。

> [!NOTE]
> 2018 年 6 月 30 日までの期間限定で、バックアップと IO の料金が無料です。

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-pool-show-up-on-my-bill"></a>エラスティック プールでアクティブ geo レプリケーションを使用した場合、請求書にはどのように表示されますか。
エラスティック データベースで [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md) を使用した場合、単一データベースとは異なり、請求書には直接の影響はありません。  各プール (プライマリ プールおよびセカンダリ プール) にプロビジョニングされたリソースに対してのみ課金されます

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>監査機能を使用すると請求書にどのような影響がありますか。
監査機能は、追加料金なしで SQL Database サービスに組み込まれており、すべてのサービス レベルで利用可能です。 ただし、監査ログを保存する場合、監査機能は Azure Storage アカウントを使用するので、監査ログのサイズに応じて Azure Storage のテーブルとキューの料金が課されます。

## <a name="how-do-i-reset-the-password-for-the-server-admin"></a>サーバー管理者のパスワードをリセットするにはどうすればよいですか。
[Azure Portal](https://portal.azure.com) で **[SQL Server]** をクリックし、一覧からサーバーを選択して、**[パスワードのリセット]** をクリックします。

## <a name="how-do-i-manage-databases-and-logins"></a>データベースとログインはどのように管理しますか。
[データベースとログインの管理](sql-database-manage-logins.md)に関するページを参照してください。

## <a name="how-do-i-make-sure-only-authorized-ip-addresses-are-allowed-to-access-a-server"></a>承認した IP アドレスにのみサーバーへのアクセスを許可するにはどうすればよいですか。
「 [方法: ファイアウォール設定を構成する (SQL Database)](sql-database-configure-firewall-settings.md)」を参照してください。

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>同じ Azure 地理的条件内で 2 つのリージョン間のデータベースを geo レプリケートする場合に予想されるレプリケーションの遅延はどのくらいですか。
現在、5 秒間の RPO をサポートしているため、geo セカンダリが Azure 推奨のペアになっているリージョンでホストされ、同じサービス レベルである限り、レプリケーションの遅延はそれより短くなっています。

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>geo セカンダリをプライマリ データベースと同じリージョンに作成する場合に予想されるレプリケーションの遅延はどのくらいですか。
実験データによれば、Azure 推奨のペアになっているリージョンが使用されている場合は、リージョン内およびリージョン間のレプリケーションの遅延に大きな差はありません。 

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>geo レプリケーションが設定されている場合、2 つのリージョン間にネットワーク エラーが発生すると、再試行ロジックはどのように動作しますか。
接続が切断されると、接続を再確立するために 10 秒ごとに再試行します。

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>プライマリ データベースでの重要な変更がレプリケートされていることを保証するには、どうすればよいでしょうか。
geo セカンダリは非同期レプリカであり、プライマリとの完全な同期を維持しようとはしていません。 ただし、重要な変更 (パスワードの更新など) を確実にレプリケートするために、強制同期を行う方法が用意されています。 強制同期を実行すると、コミットされたトランザクションがすべてレプリケートされるまで呼び出しスレッドがブロックされるため、パフォーマンスに影響します。 詳細については、[sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx) に関するページをご覧ください。 

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>プライマリ データベースと geo セカンダリの間のレプリケーションの遅延を監視するために、どのツールを使用できますか。
DMV を使ってプライマリ データベースと geo セカンダリの間のリアルタイムのレプリケーションの遅延を公開しています。 詳細については、[sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx) に関するページをご覧ください。

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>同じサブスクリプション内の別のサーバーにデータベースを移動するには
[Azure Portal](https://portal.azure.com) で **[SQL データベース]** をクリックし、一覧からデータベースを選択してから、**[コピー]** をクリックします。 詳細については、「 [Azure SQL データベースのコピー](sql-database-copy.md) 」を参照してください。

## <a name="to-move-a-database-between-subscriptions"></a>サブスクリプション間でデータベースを移動するには
[Azure Portal](https://portal.azure.com) で **[SQL Server]** をクリックし、一覧からデータベースをホストするサーバーを選択します。 **[移動]** をクリックし、移動するリソースと移動先のサブスクリプションを選択します。

