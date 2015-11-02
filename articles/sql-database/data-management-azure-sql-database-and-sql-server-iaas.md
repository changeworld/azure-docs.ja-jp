<properties 
	pageTitle="Azure SQL Database と Azure VM 内の SQL Server について | Microsoft Azure" 
	description="Azure Virtual Machines における Azure SQL Database と SQL Server について説明します。アプリケーションにとって最適な SQL テクノロジを決定する一般的なビジネスの要因について確認します。" 
	services="sql-database, virtual-machines" 
	documentationCenter="" 
	authors="Selcin" 
	manager="jeffreyg" 
	editor="tysonn"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="vm-windows-sql-server" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/19/2015" 
	ms.author="selcint"/>

# Azure SQL Database と Azure VM 内の SQL Server について

Microsoft Azure は、SQL Server をホストする方法として、[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) と [Azure VM 上の SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) の 2 つのオプションを提供しています。この記事では、最初にマイクロソフトのデータ プラットフォームの全体像における各オプションの位置付けを確認し、次に、意思決定の決め手となるビジネス要件を基により深く検討します。この記事を参照することで、コスト削減と管理の最小化のいずれを優先するかに関係なく、最大の関心事項であるビジネス要件に対してそれぞれのオプションがどのように役立つかに基づいて、適切なアプローチを判断できます。

- [Microsoft のデータ プラットフォーム](#platform)
- [Azure SQL Database と Azure VM 内の SQL Server の詳細](#close)	
- [Azure SQL Database と Azure VM 内の SQL Server を選択するときのビジネスの要因](#business)	
	- [コスト](#cost)
		- [課金とライセンスの基礎](#billing)	
		- [アプリケーションの総コストの計算](#appcost)	
	- [管理](#admin)	
	- [サービス レベル アグリーメント (SLA)](#sla)	
	- [製品化に要する時間](#market)	
- [まとめ](#summary)	
- [謝辞](#ack)	
- [その他のリソース](#resources)	


##<a name="platform"></a>Microsoft のデータ プラットフォーム

Azure とオンプレミス SQL Server データベースの話を始める前にまず、それらがすべてが利用可能であるということを理解しておいてください。Microsoft のデータ プラットフォームは、SQL Server テクノロジを活用し、物理的なオンプレミスマシン、プライベート クラウド環境、サード パーティにホストされているプライベート クラウド環境、パブリック クラウドのすべてでそのテクノロジを使用できるようにします。これにより、環境全体でサーバー製品、開発ツール、専門知識を同じ組み合わせで使用しながら、オンプレミスとクラウド ホスト型のデプロイを組み合わせることで、多様な独自のビジネス ニーズを満たすことができます。

   ![][1]

図に示すように、各製品は、インフラストラクチャに及ぼす管理のレベル (X 軸) と、データベース レベルの統合と自動化で実現するコスト効率の度合い (Y 軸) によって特徴付けることができます。

アプリケーションの設計時には、アプリケーションの SQL Server の部分をホストするために 4 つの基本的なオプションを使用できます。

- 仮想化されていない物理マシン上の SQL Server 
- オンプレミス仮想マシン内の SQL Server (プライベート クラウド)
- Azure Virtual Machines 内の SQL Server (パブリック クラウド)
- Azure SQL Database (パブリック クラウド)

次のセクションでは、最後の 2 つの、Azure SQL Database と Azure VM 内の SQL Server について学習します。さらに、アプリケーションにとって最適なオプションを決定する一般的なビジネスの要因について確認します。

##<a name="close"></a>Azure SQL Database と Azure VM 内の SQL Server の詳細

**Microsoft Azure SQL Database (Azure SQL Database)** は、サービスとしてのリレーショナル データベースです。これは業界内のカテゴリとして、*サービスとしてのプラットフォーム (PaaS)* に分類されます。Azure SQL Database は、マイクロソフトが所有、ホスト、保守する標準的なハードウェアおよびソフトウェア上に構築されています。SQL Database では、組み込みの機能を使用して、サービス上で直接開発を行えます。SQL Database は従量課金制で利用しますが、より強力にスケール アップまたはスケール アウトするためのオプションもあります。

**Azure Virtual Machines (VM) 内の SQL Server** は、*サービスとしてのインフラストラクチャ (IaaS)* という業界内カテゴリに分類され、クラウドの仮想マシン内で SQL Server を実行できます。Azure SQL Database と同様に、マイクロソフトが所有、ホスト、保守する標準的なハードウェア上に構築されています。VM 内で SQL Server を使用する場合、所有している SQL Server のライセンスを Azure に移行するか、Azure ポータルの事前構成済みの SQL Server イメージの 1 つを使用できます。

概して、目的別に最適化された次の 2 つの SQL オプションがあります。

- **Azure SQL Database** は、多くのデータベースをプロビジョニングして管理するためのコスト全体を最小限に抑えられるように最適化されています。アップグレード、高可用性、バックアップなどを含め、仮想マシン、オペレーティング システム、データベース ソフトウェアを管理する必要がないため、継続して発生する管理コストが最小限になります。通常、SQL Database を使用すると、単一の IT または開発リソースで管理されるデータベースの数を飛躍的に増加させることができます。
- **Azure VM 内で実行される SQL Server** は、ハイブリッド シナリオで既存のオンプレミス SQL Server アプリケーションを Azure に拡張したり、移行シナリオや開発およびテスト シナリオで既存のアプリケーションを Azure にデプロイしたりするために最適化されています。[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) 経由で Azure のセカンダリ データベース レプリカを保持するのは、ハイブリッド シナリオの一例です。Azure VM 内の SQL Server では、専用の SQL Server インスタンスおよびクラウド ベースの VM に対する完全な管理者権限があります。仮想マシンの維持に使用できる IT リソースが組織に既にある場合は、Azure VM 内の SQL Server を選択することをお勧めします。VM 内で SQL Server を使用すると、高度にカスタマイズされたシステムを構築して、アプリケーションの固有のパフォーマンスや可用性の要件に対処できます。

次の表に Azure SQL Database と Azure VM 内の SQL Server の主な特徴をまとめます。

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle"></th>
   <th align="left" valign="middle">Azure SQL Database</th>
   <th align="left" valign="middle">Azure VM 内の SQL Server</th>
   
</tr>
<tr>
   <td valign="middle"><p><b>最適</b></p></td>
   <td valign="middle">
          <ul>
          <li type=round>開発とマーケティングに時間的制約があるクラウド設計の新しいアプリケーション。
          <li type=round>組み込みの自動高可用性、災害復旧ソリューション、アップグレード メカニズムを必要とするアプリケーション。
          <li type=round>基になるオペレーティング システム、ハードウェア、および構成設定の管理を回避して、数百または数千ものデータベースを使用する場合。
         <li type=round>スケール アウト パターンを使用したアプリケーション。
         <li type=round>サイズが最大 500 GB のデータベース。
         <li type=round>サービスとしてのソフトウェア アプリケーションの構築。
         
  </ul>
</td>
   <td valign="middle">
      <ul>
      <li type=round>変更を最小限に抑えてクラウドに迅速に移行する必要がある既存のアプリケーション。
      <li type=round>セキュリティで保護されたトンネル経由で Azure からオンプレミス リソース (Active Directory など) にアクセスする必要がある SQL Server アプリケーション。
      <li type=round>完全な管理者アクセス権があるカスタマイズされた IT 環境を必要とする場合。
      <li type=round>運用環境以外のオンプレミス SQL Server のハードウェアを購入しない場合の迅速な開発およびテスト シナリオ。
      <li type=round><a href="http://msdn.microsoft.com/library/jj919148.aspx">Azure Storage でのバックアップ</a>や <a href="https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions">Azure VM 内の AlwaysOn レプリカ</a>を使用したオンプレミス SQL Server アプリケーションの災害復旧。
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
   <td valign="middle"><ul><li type=round>Azure SQL Database は、組み込みのフォールト トレランスのインフラストラクチャ機能以外にも、ビジネス継続性を向上させるために、特定の時点への復元、geo リストア、geo レプリケーションなどの機能を提供します。詳細については、「<a href="http://msdn.microsoft.com/library/azure/hh852669.aspx">Azure SQL Database のビジネス継続性</a>」を参照してください。</ul></td>
   <td valign="middle"><ul><li type=round>Azure VM 内の SQL Server では、データベース固有のニーズのために、高可用性と災害復旧のソリューションを設定できます。そのため、アプリケーション向けにシステムを大幅に最適化できます。必要なときに自分でフェールオーバーをテストして実行できます。詳細については、「<a href="https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions">Azure Virtual Machines における SQL Server の高可用性と災害復旧</a>」を参照してください。</ul></td>
   
</tr>
<tr>
   <td valign="middle"><p><b>ハイブリッド クラウド</b></p></td>
   <td valign="middle"><ul><li type=round>オンプレミス アプリケーションから Azure SQL Database 内のデータにアクセスできます。</ul></td>
   <td valign="middle"><ul>
      <li type=round>Azure VM 内の SQL Server を使用して、アプリケーションの一部をクラウドに、一部をオンプレミスにすることができます。たとえば、<a href="https://azure.microsoft.com/documentation/articles/virtual-networks-overview/">Azure Network Services</a> 経由でオンプレミス ネットワークと Active Domain Directory をクラウドに拡張できます。さらに、<a href="http://msdn.microsoft.com/library/dn385720.aspx">Azure の機能の SQL Server データ ファイル</a>を使用して、Azure Storage にオンプレミスのデータ ファイルを格納することができます。詳細については、「<a href="http://msdn.microsoft.com/library/dn606154.aspx">SQL Server 2014 ハイブリッド クラウドの概要</a>」を参照してください。
      <li type=round>オンプレミス SQL Server アプリケーションの災害復旧をサポートします (<a href="http://msdn.microsoft.com/library/jj919148.aspx">Azure Storage 上のバックアップ</a>または <a href="https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions">Azure VM 内の AlwaysOn レプリカ</a>を使用)。
      </ul></td>
   
</tr>
</table>

##<a name="business"></a>Azure SQL Database と Azure VM 内の SQL Server を選択するときのビジネスの要因

###<a name="cost"></a>コスト

現金が潤沢にないスタートアップ企業や厳しい予算で経営している老舗企業では、資金調達での限界がデータベースをホストする方法を決定する主な要因になることがあります。このセクションではまず、Azure SQL Database と Azure VM 内の SQL Server の 2 つのリレーショナル データベース オプションに関する Azure の課金とライセンスの基礎について説明します。次に、アプリケーションの総コストを計算する方法を見てみましょう。

####<a name="billing"></a>課金とライセンスの基礎

**Azure SQL Database** はライセンスではなくサービスとして顧客に販売されます。Azure VM 内の SQL Server が従来の SQL Server のライセンスを必要とするのと対照的です。

現在、**Azure SQL Database** はいくつかのサービス階層で利用できます。Basic、Standard、および Premium のサービス階層では、選択したサービス階層とパフォーマンス レベルに基づく固定の率で時間単位に課金されます。Basic、Standard、および Premium のサービス階層は、アプリケーションのピーク時の要件を満たすために、複数のパフォーマンス レベルで、予測できるパフォーマンスを実現するように設計されています。サービス階層とパフォーマンス レベルを変更して、アプリケーションのさまざまなスループット ニーズを満たすことができます。現在サポートされているサービス階層の最新情報については、「[Azure SQL Database のサービス階層 (エディション)](sql-database-service-tiers.md)」を参照してください。

**Azure SQL Database** を使用すると、世界中のデータ センターの Microsoft Azure によって、データベース ソフトウェアの自動的な構成、修正プログラムの適用、およびアップグレードが行われます。そのため、管理コストが削減されます。また、[組み込みバックアップ](http://msdn.microsoft.com/library/azure/jj650016.aspx)機能は、特に、大量のデータベースがある場合の大幅なコスト削減に役立ちます。Azure SQL Database を使用する場合、Azure SQL Database に対して実行される個々のクエリやインターネット トラフィックの受信には課金されません。[インターネット トラフィックの送信](http://azure.microsoft.com/pricing/details/data-transfers/)に課金されます。データベースのトランザクション量が膨大で、多数の同時ユーザーをサポートする必要がある場合は、Basic や Standard ではなく、Premium サービス階層を使用することをお勧めします。

**Azure VM 内の SQL Server** を試用する場合、従来の SQL Server ライセンスを活用します。プラットフォームによって提供される SQL Server イメージを使用するか、SQL Server ライセンスを Azure に移行することができます。SQL Server のプラットフォームによって提供されるイメージを使用する場合、コストは、VM のサイズだけでなく選択する SQL Server のバージョンによって決まります。基本的に、ユーザーは、SQL Server の 1 分あたりのライセンス費用、Windows Server の 1 分あたりのライセンス費用、Azure のストレージ コストを支払います。1 分あたりの課金のオプションでは、SQL Server のフル ライセンスを購入することなく、必要なだけ SQL Server を使用することができます。Azure に SQL Server のライセンスを移行する場合は、Azure のコンピューティング コストとストレージ コストのみに対して課金されます。詳細については、「[Azure でのソフトウェア アシュアランスによるライセンス モビリティ](http://azure.microsoft.com/pricing/license-mobility/)」を参照してください。

####<a name="appcost"></a>アプリケーションの総コストの計算

クラウド プラットフォームを使用し始めるときのアプリケーションのランニング コストには主に、開発および管理のコストと、パブリック クラウド プラットフォームで必要とされるサービスのコストが含まれます。

Azure SQL Database と Azure VM 内の SQL Server で実行されるアプリケーションの詳細なコストの計算方法を次に示します。

**Azure SQL Database を使用する場合:**

*アプリケーションの総コスト = 大幅に最小化された管理コスト + ソフトウェア開発コスト + Azure SQL Database サービス コスト*

**Azure VM 内の SQL Server を使用する場合:**

*アプリケーションの総コスト = 最小化されたソフトウェア開発および変更コスト + 管理コスト + SQL Server と Windows Server のライセンス コスト + Azure Storage のコスト*

> [AZURE.IMPORTANT]現時点では、Azure SQL Database は SQL Server の一部の機能をサポートしていません。詳細な比較情報については、「[Azure SQL Database のガイドラインと制限事項](http://msdn.microsoft.com/library/azure/ff394102.aspx)」を参照してください。データベースの設計変更で追加予算が必要になり、既存のデータベースを Azure SQL Database に移動する場合は、次の内容に注意してください。Azure SQL Database は、マイクロソフトのサービスとしてのプラットフォーム製品です。既存のオンプレミス SQL Server アプリケーションを Azure SQL Database に移行する場合は、サービスとしてのプラットフォーム製品の利点をくまなく活用できるように、アプリケーションを更新することをお勧めします。たとえば、[Azure Websites](http://azure.microsoft.com/documentation/services/websites/) や [Azure Cloud Services](http://azure.microsoft.com/services/cloud-services/) をアプリケーション層で使用すると、コスト面での利点が大きくなります。また、さまざまな Azure SQL Database のサービス階層に対してアプリケーションを検証し、どのサービス階層がアプリケーションのニーズに最も適合するかどうかを確認します。このプロセスにより、パフォーマンスの成果を高めてコストを最小限に抑制することができます。詳細については、「[Azure SQL Database のサービス階層とパフォーマンス レベル](sql-database-service-tiers.md)」を参照してください。

コストを詳しく見積もるには、[Azure の料金計算ツール](http://azure.microsoft.com/pricing/calculator/)を使用します。

料金の詳細については、次のリソースを参照してください。

- [Azure SQL Database の料金詳細](http://azure.microsoft.com/pricing/details/sql-database/) 
- [Virtual Machines の料金詳細](http://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure VM 内の SQL Server - 料金詳細](http://azure.microsoft.com/pricing/details/virtual-machines/#sql-server)
- [Azure VM 内の Windows Server - 料金詳細](http://azure.microsoft.com/pricing/details/virtual-machines/#windows) 

###<a name="admin"></a>管理

手に余るほどの多くのタスクを抱えていると、サーバーやデータベースの管理作業は後回しにしたくなることがあります。多くの企業にとって、クラウド サービスを使用するかどうかは、複雑な管理の負荷を軽減する機能にすべてがかかっています。マイクロソフトでは、**Azure SQL Database** を使用して、ハード ドライブ、サーバー、ストレージなどの物理的ハードウェアを管理しながら、高可用性を提供するためにすべてのデータを自動的にレプリケートし、データベース ソフトウェアの構成やアップグレードを実行し、負荷分散を管理し、サーバー エラーが発生した場合には透過的なフェールオーバーを実行しています。Azure SQL Database インスタンスを引き続き管理できますが、基になる SQL Server インスタンスや Azure プラットフォームの物理リソースは制御できません。たとえば、データベースとログインの管理、インデックスのチューニング、クエリの最適化を行うことができますが、システム テーブルとファイル グループは管理できません。詳細については、「[Azure SQL Database のガイドラインと制限事項](http://msdn.microsoft.com/library/ff394102.aspx)」を参照してください。

一方で、社内に専門知識を蓄積し、コンピューター自体に配置されたデータベースまで制御したいとユーザーが希望することもあります。**Azure VM 内で実行される SQL Server** では、オペレーティング システムと SQL Server インスタンスの構成を完全に制御できます。VM を使用する場合、オペレーティング システムとデータベースのソフトウェアの更新およびアップグレードのタイミングや、ウイルス対策とバックアップ ツールなど追加のソフトウェアのインストールのタイミングは、ユーザーが決定します。また、VM のサイズ、ディスクの数、ストレージの構成を制御できます。たとえば、Azure では、必要に応じて、実行中の VM のサイズを変更できます。詳細については、「[Azure の仮想マシンおよびクラウド サービスのサイズ](../virtual-machines/virtual-machines-size-specs.md)」を参照してください。

###<a name="sla"></a>サービス レベル アグリーメント (SLA)

サービス レベル アグリーメント (SLA) の稼働時間の義務の遂行は、最優先事項とされることがあります。このセクションでは、各データベースのホスト オプションについて SLA が示す意味を説明します。

**Azure SQL Database** では、Basic、Standard、および Premium サービス階層で、マイクロソフトは可用性 99.99% の SLA を提供します。可用性の SLA は、データベースに接続する機能に適用されることに注意してください。つまり、データベース レベルの SLA です。SLA の最新情報については、「[サービス レベル アグリーメント](http://azure.microsoft.com/support/legal/sla/)」を参照してください。Azure SQL Database サービス階層 (エディション) と、サポートされているビジネス継続性のプランの最新情報については、「[Azure SQL Database のサービス階層 (エディション)](sql-database-service-tiers.md)」を参照してください。

**Azure にホストされる Virtual Machines**では、マイクロソフトは可用性 99.95% の SLA を提供します。この可用性は VM 向けのものであり、VM 内部で実行されるプロセス (SQL Server など) 向けではありません。[VM SLA](http://www.microsoft.com/download/details.aspx?id=38427) では、1 つの可用性セットに少なくとも 2 つの VM をホストする必要があります。このように構成すると、Azure は VM の少なくとも 1 つを 99.95% の時間に利用できることを保証します。VM 内のデータベース高可用性 (HA) のために、AlwaysOn 可用性グループなど、SQL Server でサポートされているいずれかの高可用性オプションを構成する必要があります。Azure での AlwaysOn 設定の一部では手動による構成と管理が必要であり、運用する各セカンダリに追加で課金されることに注意してください。


###<a name="market"></a>製品化に要する時間

**Azure SQL Database** は、開発者の生産性と製品化に要する時間の短縮が重要な、クラウド用に設計されたアプリケーションに最適なソリューションです。プログラムによる DBA のような機能を備えることで、基になるオペレーティング システムとデータベースを管理する必要性が減少するため、クラウドの設計者と開発者に最適です。開発者がデータベースに関連するタスクを理解し構成しやすくなります。たとえば、[REST API](http://msdn.microsoft.com/library/azure/dn505719.aspx) と [PowerShell のコマンドレット](http://msdn.microsoft.com/library/azure/dn546726.aspx)を使用して、数千のデータベースの管理の操作を自動化し、管理できます。クラウドを[柔軟にスケーリング](sql-database-elastic-pool.md)することで、アプリケーション層に容易に専念し、アプリケーションをより早く市場に提供することができます。

**Azure VM 内で実行される SQL Server** は、既存および新規のアプリケーションで SQL Server インスタンスのすべての機能に対するアクセスと制御が必要な場合や、既存のオンプレミス アプリケーションとデータベースをクラウドにそのまま移行する場合に最適です。プレゼンテーション層、アプリケーション層、およびデータ層を変更する必要がないため、既存のソリューションを再設計する時間と予算が節約されます。代わりに、VM へのソリューションのすべてのパッケージの移行と、Azure プラットフォームで必要な一部のパフォーマンスの最適化に集中できます。詳細については、「[Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス](../virtual-machines/virtual-machines-sql-server-performance-best-practices.md)」を参照してください。

##<a name="summary"></a>概要

この記事では、Azure SQL Database と Azure VM 内の SQL Server について詳しく見てきました。また、選択での意思決定に影響を与える可能性がある一般的なビジネスの要因についても説明しました。

次に、どのようなときにどちらを使用するか検討するためのヒントをまとめます。

次の場合、**Azure SQL Database** を選択します。

- クラウド ベースの新しいアプリケーションを構築している場合。または、既存の SQL Server データベースを Azure に移行する必要があり、そのデータベースが Azure SQL Database のサポートしていない機能を使用していない場合。詳細については、「[Azure SQL Database Transact-SQL リファレンス](http://msdn.microsoft.com/library/azure/ee336281.aspx)」を参照してください。この方法では、完全に管理されたクラウド サービスの利点が生かされ、製品化に要する時間が確実に短縮されます。

- マイクロソフトのサービスを利用して、データベースで一般的な管理操作を実行し、データベースの可用性の SLA を強化する場合。この方法は、管理コストを最小限に抑えることができ、同時にデータベースの可用性を確保します。

    [最初の Azure SQL Database を作成する](sql-database-get-started.md)


次の場合、**Azure VM 内の SQL Server** を選択します。

- 既存のオンプレミス アプリケーションがあり、所有するハードウェアの管理を停止する必要がある場合や、ハイブリッド ソリューションを検討している場合。この方法では、処理能力の高いデータベースにより速くアクセスでき、また、セキュリティで保護されたトンネルを使用してオンプレミス アプリケーションをクラウドに接続できます。

- 既存の IT リソースがあるため、SQL Server で完全な管理者権限が必要であり、オンプレミス SQL Server との完全な互換性を必要とする場合 (たとえば、一部の機能が Azure SQL Database に存在しない)。この方法では、ほとんどのアプリケーションを柔軟に実行できることで、既存のアプリケーションの開発や変更のコストを最小限に抑えることができます。また、VM、オペレーティング システム、データベースの構成を完全に制御できます。

    [Azure での SQL Server 仮想マシンのプロビジョニング](virtual-machines-provision-sql-server.md)

> [AZURE.NOTE]SQL Server 2016 CTP2 を試してみますか? Microsoft Azure にサインアップし、[ここ](http://aka.ms/sql2016vm "ここ")に移動して、SQL Server 2016 CTP2 が既にインストールされている仮想マシンを作成します。


##<a name="ack"></a>謝辞

この記事は Microsoft Cloud およびエンタープライズ コンテンツ サービスのグループに帰属し、Microsoft コミュニティ内の多くのユーザーの協力の元に作成されました。

**著者**: Selcin Turkarslan

**技術寄稿者**: Conor Cunningham

**技術校閲者:** Joanne Marone (Hodgins)、Karthika Raman、Lindsey Allen、Lori Clark、Luis Carlos Vargas Herring、Nosheen Syed Wajahatulla Hussain、Pravin Mittal、Shawn Bice、Silvano Coriani、Tony Petrossian、Tracy Daugherty

**編集校閲者:** Heidi Steen、Maggie Sparkman

この記事の作成に携わった協力者全員に感謝します。

##<a name="resources"></a>その他のリソース 

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">リソース</th>
   <th align="left" valign="middle">説明</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/ee336279.aspx">MSDN: Azure SQL Database</a></p>
<p><a href="https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/">Azure Virtual Machines における SQL Server の概要</a></p>

<p><a href="http://azure.microsoft.com/services/sql-database/">Azure.com: Azure SQL Database</a></p></td>
   <td valign="middle">ライブラリのドキュメントへのリンク</td>   
</tr>
<tr>
   <td valign="middle"><p><a href="https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-application-patterns-and-development-strategies/">Azure Virtual Machines における SQL Server のアプリケーション パターンと開発計画</p></td>
   <td valign="middle">この記事では、Azure VM における SQL Server と Azure SQL Database などのハイブリッド シナリオに適用される最も一般的なアプリケーション パターンについて説明しています。</td>   
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/hh680934(v=PandP.50).aspx">Microsoft Enterprise ライブラリの一時的なエラー処理のアプリケーション ブロック</p></td>
   <td valign="middle">開発者は、このライブラリの堅牢な一時的エラー処理ロジックを追加することで、Azure SQL Database で実行するアプリケーションの復元性を高めることができます。一時的な障害とは、ネットワーク接続の問題やサービスを利用できない状況などの一時的な状態が原因で発生するエラーです。Azure SQL Database はマルチテナント サービスであるため、アプリケーションのダウンタイムを最小限に抑えるには、このようなエラーの処理が重要になります。</td>   
</tr>
</table>

<!--Image references-->
[1]: ./media/data-management-azure-sql-database-and-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png

<!---HONumber=Oct15_HO4-->