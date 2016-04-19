<properties
	pageTitle="SQL Database (PaaS) とクラウドの VM 上の SQL Server (IaaS) | Microsoft Azure"
	description="Azure SQL Database (PaaS) とクラウドの Azure Virtual Machines 上の SQL Server のどちらのクラウド SQL Server オプションがお客様のアプリケーションに適合するかを説明します。"
	services="sql-database, virtual-machines"
	keywords="SQL Server クラウド、クラウド内の SQL Server、PaaS データベース、クラウド SQL Server、DBaaS"
	documentationCenter=""
	authors="carlrabeler"
	manager="jhubbard"
	editor="cjgronlund"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/25/2016"
	ms.author="carlrab"/>

# クラウド SQL Server オプションの選択: Azure SQL (PaaS) Database または Azure VM (IaaS) の SQL Server

Azure には、クラウドで SQL Server ワークロードをホストするためのオプションが 2 つあります。

* [Azure SQL Database](https://azure.microsoft.com/services/sql-database/): クラウドにネイティブに対応した SQL データベースで、サービスとしてのプラットフォーム (PaaS) データベースまたはサービスとしてのデータベース (DBaaS) とも呼ばれます。サービスとしてのソフトウェア (SaaS) アプリの開発用に最適化されています。SQL Server の機能の大部分と互換性があります。
* [Azure Virtual Machines 上の SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/): Azure で実行される Windows Server Virtual Machines (VM) にインストールされてホストされているクラウド上の SQL Server で、サービスとしてのインフラストラクチャ (IaaS) とも呼ばれます。

この記事では、各オプションがマイクロソフトのデータ プラットフォームにどのように組み込まれているかを説明します。お客様のビジネス要件に適したオプションを判断するのにお役立てください。この記事を参照することで、コスト削減と管理の最小化のいずれを優先するかに関係なく、最大の関心事項であるビジネス要件に適したアプローチを判断できます。


## マイクロソフトのデータ プラットフォーム

Azure とオンプレミス SQL Server データベースの話を始める前にまず、それらがすべてが利用可能であるということを理解しておいてください。Microsoft のデータ プラットフォームは、SQL Server テクノロジを活用し、物理的なオンプレミスマシン、プライベート クラウド環境、サード パーティにホストされているプライベート クラウド環境、パブリック クラウドのすべてでそのテクノロジを使用できるようにします。これにより、環境全体でサーバー製品、開発ツール、専門知識を同じ組み合わせで使用しながら、オンプレミスとクラウド ホスト型のデプロイメントを組み合わせることで、多様な独自のビジネス ニーズを満たすことができます。

   ![クラウド SQL Server オプション: IaaS 上の SQL Server またはクラウド内の SaaS SQL Database](./media/data-management-azure-sql-database-and-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

図に示すように、各製品は、インフラストラクチャに及ぼす管理のレベル (X 軸) と、データベース レベルの統合と自動化で実現するコスト効率の度合い (Y 軸) によって特徴付けることができます。

アプリケーションの設計時には、アプリケーションの SQL Server の部分をホストするために 4 つの基本的なオプションを使用できます。

- 仮想化されていない物理マシン上の SQL Server
- オンプレミス仮想マシン内の SQL Server (プライベート クラウド)
- Azure Virtual Machines 内の SQL Server (パブリック クラウド)
- Azure SQL Database (パブリック クラウド)

次のセクションでは、パブリック クラウドの SQL Server (Azure SQL Database と Azure VM 上の SQL Server) について説明します。さらに、アプリケーションにとって最適なオプションを決定する一般的なビジネスの要因について確認します。

## Azure SQL Database と Azure VM 上の SQL Server の詳細

**Azure SQL Database** は、Azure クラウドでホストされる、サービスとしてのリレーショナル データベース (DBaaS) で、業界内のカテゴリとしては*サービスとしてのソフトウェア (SaaS)* または*サービスとしてのプラットフォーム (PaaS)* に分類されます。SQL Database は、マイクロソフトが所有、ホスト、保守する標準的なハードウェアおよびソフトウェアをベースにして構築されています。SQL Database では、組み込みの機能を使用して、サービス上で直接開発を行えます。SQL Database は従量課金制で利用でき、中断することなくスケールアップまたはスケールアウトして強化できるオプションもあります。

**Azure Virtual Machines (VM) 上の SQL Server** は、業界内のカテゴリとしては*サービスとしてのインフラストラクチャ (IaaS)* に分類され、クラウド上の仮想マシン内で SQL Server を実行できます。SQL Database と同様に、マイクロソフトが所有、ホスト、保守する標準的なハードウェア上に構築されています。VM 上の SQL Server を使用する場合は、お客様が所有している SQL Server ライセンスを移行するか、事前にライセンス付与された SQL Server イメージを Azure ポータルから使用できます。

概して、目的別に最適化された次の 2 つの SQL オプションがあります。

- **SQL Database** は、多数のデータベースをプロビジョニングして管理するためのコスト全体を最小限に抑えられるように最適化されています。仮想マシン、オペレーティング システム、データベース ソフトウェアを管理する必要がないため、継続的な管理コストが軽減されます。アップグレード、高可用性、バックアップなどを管理する必要がありません。一般的には、Azure SQL Database を使用すると、単一の IT または開発リソースが管理するデータベースの数を大幅に増やすことができます。
- **Azure VM 上で実行される SQL Server** は、ハイブリッド シナリオで既存のオンプレミス SQL Server アプリケーションをクラウドに拡張したり、移行シナリオや開発/テスト シナリオで既存のアプリケーションを Azure にデプロイしたりできるように最適化されています。[Azure Virtual Networks](../virtual-network/virtual-networks-overview.md) を使用して Azure のセカンダリ データベース レプリカを保持するのも、ハイブリッド シナリオの一例です。Azure VM 上の SQL Server では、専用の SQL Server インスタンスとクラウド ベースの VM に対して完全な管理者権限があります。仮想マシンの維持に使用できる IT リソースが組織に既にある場合は、Azure VM 内の SQL Server を選択することをお勧めします。VM 上の SQL Server を使用すると、高度にカスタマイズされたシステムを構築して、アプリケーション固有のパフォーマンスや可用性の要件に対処できます。

次の表に SQL Database と Azure VM 上の SQL Server の主な特徴をまとめます。

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle"></th>
   <th align="left" valign="middle">SQL Database</th>
   <th align="left" valign="middle">Azure VM 内の SQL Server</th>

</tr>
<tr>
   <td valign="middle"><p><b>最適</b></p></td>
   <td valign="middle">
          <ul>
          <li type=round>開発とマーケティングに時間的制約があるクラウド設計の新しいアプリケーション。
          <li type=round>高可用性、障害復旧、アップグレードに関する組み込みのメカニズムを必要とするアプリケーション。
          <li type=round>基盤のオペレーティング システムと構成設定の管理を回避したいチーム。
         <li type=round>スケール アウト パターンを使用したアプリケーション。
         <li type=round>サイズが最大 1 TB のデータベース。
         <li type=round>サービスとしてのソフトウェア (SaaS) アプリケーションの構築。
  </ul>
</td>
   <td valign="middle">
      <ul>
      <li type=round>変更を最小限に抑えてクラウドに迅速に移行する必要がある既存のアプリケーション。
      <li type=round>セキュリティで保護されたトンネル経由で Azure からオンプレミス リソース (Active Directory など) にアクセスする必要がある SQL Server アプリケーション。
      <li type=round>完全な管理者アクセス権があるカスタマイズされた IT 環境を必要とする場合。
      <li type=round>運用環境以外のオンプレミス SQL Server のハードウェアを購入しない場合の迅速な開発およびテスト シナリオ。
      <li type=round>[Azure Storage へのバックアップ](http://msdn.microsoft.com/library/jj919148.aspx) や Azure VM の AlwaysOn レプリカを使用したオンプレミス SQL Server アプリケーションの障害復旧。
      <li type=round>サイズが 1 TB を超える大きなデータベース。
      </ul></td>
</tr>
<tr>
   <td valign="middle"><p><b>リソース</b></p></td>
   <td valign="middle">
       <ul>
       <li type=round>基になるインフラストラクチャのサポートおよび保守のために IT リソースを使用しない。
       <li type=round>アプリケーション層に的を絞る。
       </ul></td>
   <td valign="middle"><ul><li type=round>サポートおよび保守ための IT リソースがある。</ul></td>

</tr>
<tr>
   <td valign="middle"><p><b>総保有コスト</b></p></td>
   <td valign="middle"><ul><li type=round>ハードウェアのコストを削減します。管理コストを削減します。</ul></td>
   <td valign="middle"><ul><li type=round>ハードウェアのコストを削減します。</ul></td>

</tr>
<tr>
   <td valign="middle"><p><b>ビジネス継続性</b></p></td>
   <td valign="middle"><ul><li type=round>Azure SQL Database は、組み込みのフォールト トレランスのインフラストラクチャ機能以外にも、ビジネス継続性を向上させるために、ポイントインタイム リストア、geo リストア、geo レプリケーションなどの機能を提供します。詳細については、[SQL Database のビジネス継続性の概要](sql-database-business-continuity.md) に関するページを参照してください。</ul></td>
   <td valign="middle"><ul><li type=round>Azure VM 上の SQL Server では、データベース固有のニーズに合わせて、高可用性と障害復旧のソリューションを設定できます。そのため、アプリケーション向けにシステムを大幅に最適化できます。必要なときに自分でフェールオーバーをテストして実行できます。詳細については、「[High Availability and Disaster Recovery for SQL Server on Azure Virtual Machines (Azure Virtual Machines 上の SQL Server の高可用性と障害復旧)] (../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md)」を参照してください。</ul></td>

</tr>
<tr>
   <td valign="middle"><p><b>ハイブリッド クラウド</b></p></td>
   <td valign="middle"><ul><li type=round>オンプレミス アプリケーションから Azure SQL Database 内のデータにアクセスできます。</ul></td>
   <td valign="middle"><ul>
      <li type=round>Azure VM 上の SQL Server を使用して、アプリケーションの一部をクラウド上で実行し、一部をオンプレミスで実行することができます。たとえば、[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) を介してオンプレミス ネットワークと Active Directory ドメインをクラウドに拡張できます。さらに、[Azure の SQL Server データ ファイル](http://msdn.microsoft.com/library/dn385720.aspx) を使用して、Azure Storage にオンプレミスのデータ ファイルを格納することができます。詳細については、「[SQL Server 2014 ハイブリッド クラウドの概要](http://msdn.microsoft.com/library/dn606154.aspx)」を参照してください。
      <li type=round>[Azure Blob Storage を使用した SQL Server のバックアップと復元](http://msdn.microsoft.com/library/jj919148.aspx) や [Azure VM の AlwaysOn レプリカ](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md) を使用して、オンプレミス SQL Server アプリケーションの障害復旧をサポートします。
      </ul></td>

</tr>
</table>

## Azure SQL Database と Azure VM 上の SQL Server のいずれかを選択するときのビジネスの要因

### コスト

現金が潤沢にないスタートアップ企業や厳しい予算で経営している老舗企業では、資金調達での限界がデータベースをホストする方法を決定する主な要因になることがあります。このセクションではまず、SQL Database と Azure VM 上の SQL Server の 2 つのリレーショナル データベース オプションに関する Azure の課金とライセンスの基礎について説明します。次に、アプリケーションの総コストを計算する方法を見てみましょう。

#### 課金とライセンスの基礎

**SQL Database** はライセンスではなくサービスとしてお客様に販売されます。これに対して、Azure VM 上の SQL Server には従来の SQL Server のライセンスが必要です。

現在、**SQL Database** は複数のサービス階層で利用でき、選択したサービス階層とパフォーマンス レベルに基づく固定率で時間単位で課金されます。さらに、インターネット トラフィックの送信も課金されます。Basic、Standard、および Premium のサービス階層は、アプリケーションのピーク時の要件を満たすために、複数のパフォーマンス レベルで、予測できるパフォーマンスを実現するように設計されています。サービス階層とパフォーマンス レベルを変更して、アプリケーションのさまざまなスループット ニーズを満たすことができます。データベースのトランザクション量が膨大で、多数の同時ユーザーをサポートする必要がある場合は、Premium サービス階層をお勧めします。現在サポートされているサービス階層の最新情報については、[Azure SQL Database のサービス階層](sql-database-service-tiers.md)に関するページを参照してください。

**SQL Database** では、データベース ソフトウェアの自動的な構成、修正プログラムの適用、およびアップグレードがマイクロソフトによって行われるため、管理コストが軽減されます。また、[組み込みのバックアップ](sql-database-business-continuity.md)機能は、特に、多数のデータベースがある場合の大幅なコスト削減に役立ちます。

**Azure VM 上の SQL Server** では、従来の SQL Server ライセンスを使用します。プラットフォームによって提供される SQL Server イメージ (ライセンスが含まれています) を使用するか、所有している SQL Server ライセンスを移行することができます。Azure によって提供されるイメージを使用する場合、追加コストは、VM のサイズだけでなく選択する SQL Server のエディションによって決まります。VM のサイズや SQL Server のエディションにかかわらず、ユーザーは、SQL Server および Windows Server の 1 分あたりのライセンス費用と VM ディスクの Azure Storage コストを支払います。1 分あたりの課金のオプションでは、SQL Server の追加ライセンスを購入することなく、必要なだけ SQL Server を使用することができます。Azure に SQL Server のライセンスを移行する場合は、Windows Server コストとストレージ コストのみに対して課金されます。現在所有しているライセンスの移行の詳細については、「[Azure でのソフトウェア アシュアランスによるライセンス モビリティ](https://azure.microsoft.com/pricing/license-mobility/)」を参照してください。

#### アプリケーションの総コストの計算

クラウド プラットフォームの使用を開始する場合、アプリケーションの実行コストには主に、開発および管理のコストと、パブリック クラウド プラットフォームで必要とされるサービスのコストが含まれます。

SQL Database と Azure VM 上の SQL Server で実行されるアプリケーションの詳細なコストの計算方法を次に示します。

**Azure SQL Database を使用する場合:**

*アプリケーションの総コスト = 大幅に最小化される管理コスト + ソフトウェア開発コスト + SQL Database サービス コスト*

**Azure VM 上の SQL Server を使用する場合:**

*アプリケーションの総コスト = 最小化されるソフトウェア開発および変更コスト + 管理コスト + SQL Server と Windows Server のライセンス コスト + Azure Storage のコスト*

価格の詳細については、次のリソースを参照してください。

- [SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)
- [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) および [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows) 向けの [Virtual Machines の価格](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)

> [AZURE.NOTE] SQL Server の機能には、SQL Database に適用できないものや SQL Database で利用できないものがいくつかあります。詳細については、[SQL Database の一般的な制限事項とガイドライン](sql-database-general-limitations.md)および [SQL Database Transact-SQL 情報](sql-database-transact-sql-information.md)に関するページを参照してください。既存の SQL Server ソリューションをクラウドに移行する場合は、「[SQL Server データベースの Azure SQL Database への移行](sql-database-cloud-migrate.md)」を参照してください。既存のオンプレミス SQL Server アプリケーションを SQL Database に移行する場合は、クラウド サービスで提供されている機能を活用できるようにアプリケーションを更新することを検討してください。たとえば、[Azure Web Apps サービス](https://azure.microsoft.com/services/app-service/web/)または [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/) を使用してアプリケーション層をホストすると、コスト面での利点が大きくなります。

### 管理

多くの企業にとって、クラウド サービスに切り替えるかどうかの決定には、コストだけでなく、管理の複雑さの軽減も重要な要素です。**SQL Database** では、基盤となるハードウェアの管理、すべてのデータの自動的なレプリケートによる高可用性の実現、データベース ソフトウェアの構成とアップグレード、負荷分散の管理、サーバーに障害が発生した場合の透過的なフェールオーバーをマイクロソフトが実行します。ユーザーは引き続きデータベースを管理できますが、データベース エンジン、サーバー オペレーティング システム、ハードウェアを管理する必要はありません。引き続き管理できるアイテムの例として、データベースとログイン、インデックスとクエリのチューニング、監査とセキュリティなどがあります。

一方で、社内に専門知識を蓄積し、ディスクに配置するデータベースの場所まで制御したいとユーザーが希望することもあります。**Azure VM 上で実行される SQL Server** では、オペレーティング システムと SQL Server インスタンスの構成を全面的に制御できます。VM を使用する場合、オペレーティング システムとデータベース ソフトウェアの更新やアップグレードのタイミングに加えて、ウイルス対策やバックアップ ツールなど追加ソフトウェアのインストールのタイミングは、ユーザーが決定します。また、VM のサイズ、ディスクの数、ストレージの構成を制御できます。たとえば、Azure では、必要に応じて、VM のサイズを変更できます。詳細については、「[Azure の仮想マシンおよびクラウド サービスのサイズ](../virtual-machines/virtual-machines-linux-sizes.md)」を参照してください。

### サービス レベル アグリーメント (SLA)

多くの IT 部門では、サービス レベル アグリーメント (SLA) の稼働時間に関する義務を遂行することは、最優先事項です。このセクションでは、各データベースのホスト オプションに適用される SLA について説明します。

**SQL Database** では、Basic、Standard、および Premium サービス階層で、可用性 99.99% の SLA が提供されます。最新情報については、[サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/sql-database/)に関するページを参照してください。SQL Database サービス階層と、サポートされているビジネス継続性計画の最新情報については、[サービス階層](sql-database-service-tiers.md)に関するページを参照してください。

**Azure VM 上で実行される SQL Server** では、仮想マシンのみを対象に、可用性 99.95% の SLA が提供されます。この SLA は VM 上で実行される (SQL Server などの) プロセスは対象としておらず、可用性セットに少なくとも 2 つの VM インスタンスをホストしている必要があります。最新情報については、[VM の SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) に関するページを参照してください。VM 内のデータベース高可用性 (HA) を実現するには、[AlwaysOn 可用性グループ](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx)など、SQL Server でサポートされているいずれかの高可用性オプションを構成する必要があります。

### <a name="market"></a>製品化に要する時間

**SQL Database** は、開発者の生産性と製品化に要する時間の短縮が重要な、クラウド用に設計されたアプリケーションに最適なソリューションです。プログラムによる DBA のような機能を備えることで、基になるオペレーティング システムとデータベースを管理する必要性が減少するため、クラウドの設計者と開発者に最適です。たとえば、[REST API](http://msdn.microsoft.com/library/azure/dn505719.aspx) と [PowerShell コマンドレット](http://msdn.microsoft.com/library/azure/dn546726.aspx)を使用して、数千のデータベースの管理の操作を自動化して管理できます。[エラスティック データベース プール](sql-database-elastic-pool.md)などの機能を使用すると、アプリケーション層に専念し、ソリューションをより早く市場に提供することができます。

**Azure VM 上で実行される SQL Server** は、既存のアプリケーションや新しいアプリケーションから SQL Server インスタンスのすべての機能にアクセスして制御する必要がある場合に最適です。また、既存のオンプレミス アプリケーションとデータベースを Azure にそのまま移行する場合にも適しています。プレゼンテーション層、アプリケーション層、およびデータ層を変更する必要がないため、既存のソリューションを再設計する時間と予算が節約されます。その一方で、すべてのソリューションを Azure に移行し、Azure プラットフォームで必要な一部のパフォーマンスの最適化に集中できます。詳細については、[Azure Virtual Machines 上の SQL Server のパフォーマンスに関するベスト プラクティス](../virtual-machines/virtual-machines-windows-sql-performance.md)に関するページを参照してください。

## 概要

この記事では、SQL Database と Azure Virtual Machines (VM) 上の SQL Server について詳しく紹介し、意思決定に影響する可能性のある一般的なビジネス要因について説明しました。以下は、検討に役立つ提案をまとめたものです。

次の状況に該当する場合は、**Azure SQL Database** を選択します。

- 新しいクラウド ベース アプリケーションを構築しようとしている。または、クラウド サービスによって実現されるコスト削減やパフォーマンス最適化を活用するために既存の SQL Server ソリューションを移行したい。このアプローチでは、完全に管理されたクラウド サービスの利点を活かして、最初の市場投入までの時間を短縮し、長期的なコスト最適化を実現できます。

- マイクロソフトのサービスを利用して、データベースで一般的な管理操作を実行し、データベースの可用性の SLA を強化する場合。



次の状況に該当する場合は、**Azure VM 上の SQL Server** を選択します。

- 既存のオンプレミス アプリケーションがあり、自社によるハードウェア管理を停止したい。または、ハイブリッド ソリューションを検討している。このアプローチでは、処理能力の高いデータベースに短時間でアクセスできる一方で、セキュリティで保護されたトンネルを使用してオンプレミス アプリケーションにスムーズに接続できます。

- 既存の IT リソースがあり、SQL Server に対する完全な管理者権限が必要で、オンプレミス SQL Server との完全な互換性が必要である。この方法では、ほとんどのアプリケーションを柔軟に実行できることで、既存のアプリケーションの開発や変更のコストを最小限に抑えることができます。また、VM、オペレーティング システム、データベースの構成を全面的に制御できます。



> [AZURE.NOTE] SQL Server 2016 CTP2 の試用を希望される場合は、 Microsoft Azure にサインアップし、[ここ](http://aka.ms/sql2016vm "ここ")に移動して、SQL Server 2016 CTP2 が既にインストールされている仮想マシンを作成します。

## 次のステップ
- SQL Database の基本的な使い方については、「[SQL Database チュートリアル: サンプル データと Azure ポータルを使用して分単位で SQL Database を作成する](sql-database-get-started.md)」を参照してください。
- 「[SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」を参照してください。
- Azure VM での SQL Server の基本的な使い方については、「[Provision a SQL Server virtual machine in Azure (Azure での SQL Server 仮想マシンのプロビジョニング)](../virtual-machines/virtual-machines-windows-portal-sql-server-provision.md)」を参照してください。

<!---HONumber=AcomDC_0413_2016-->