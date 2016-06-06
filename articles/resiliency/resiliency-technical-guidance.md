<properties
   pageTitle="回復性技術ガイダンスのインデックス |Microsoft Azure"
   description="回復力のあるアプリケーション、高可用性のアプリケーション、およびフォールト トレラント アプリケーションの内容および設計方法、ならびに障害復旧とビジネス継続性の計画策定に関する技術情報記事のインデックス。"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="hongfeig"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/13/2016"
   ms.author="patw;jroth;aglick"/>

#Azure の回復性技術ガイダンス

##はじめに
高可用性と障害復旧の要件を満たすには、2 種類の知識が必要です。つまり、1) クラウド プラットフォームの機能について技術的な詳細を理解していること、および 2) 分散サービスを適切に構築する方法を知っていることが必要です。このシリーズの記事では、前者 (回復性に関する Azure プラットフォームの機能と制限) について説明します。回復性のことをビジネス継続性と言う場合もあります。後者について関心がある場合は、[Azure アプリケーションの障害復旧と高可用性](https://aka.ms/drtechguide)に焦点を当てた記事シリーズを参照してください。このシリーズの記事ではアーキテクチャと設計パターンに触れていますが、それはこのシリーズの主要な焦点ではありません。設計のガイダンスについては、「[その他のリソース](#additional-resources)」のセクションに示されている資料を参照してください。

この情報を構成する記事は次のとおりです。
##[ローカル障害からの復旧](./resiliency-technical-guidance-recovery-local-failures.md)
どの物理ハードウェア (ドライブ、サーバー、ネットワーク デバイスなど) でも、障害が発生して負荷が急増し、リソースが使い果たされる可能性があります。このセクションでは、次のような条件下で高可用性を維持するために Azure に用意されている機能について説明します。

##[Azure リージョン全体にわたるサービスの停止からの復旧](./resiliency-technical-guidance-recovery-loss-azure-region.md)
広範囲にわたる障害はまれですが、理論的には発生する可能性があります。リージョン全体がネットワーク障害によって孤立することや、自然災害によって物理的に破損することがあり得ます。このセクションでは、Azure の機能を使用して地理的にさまざまなリージョンにまたがるアプリケーションを作成する方法について説明します。

##[オンプレミスから Azure への復旧](./resiliency-technical-guidance-recovery-on-premises-azure.md)
クラウドによって障害復旧のコストが大きく変わり、組織は Azure を使用して復旧のための 2 番目のサイトを確立することが可能になります。これは、セカンダリ データセンターを構築して維持する場合の何分の 1 かのわずかなコストで実現できます。このセクションでは、オンプレミスのデータセンターをクラウドに拡張するために Azure で提供される機能について説明します。

##[データの破損または偶発的な削除からの復旧](./resiliency-technical-guidance-recovery-data-corruption.md)
アプリケーションのバグによってデータが破損する場合や、オペレーターが誤って重要なデータを削除する可能性があります。このセクションでは、データのバックアップと以前の時点への復元のために Azure で提供される機能について説明します。

##その他のリソース

###[Azure のビジネス継続性テクニカル ガイダンス](./resiliency-technical-guidance.md)
オンプレミス、ハイブリッド、およびパブリック クラウド アプリケーションで、Microsoft Azure を使用してビジネス継続性と回復性を実現するための概念とベスト プラクティスを詳しく取り上げています。

###[Microsoft Azure 上に構築されたアプリケーションの障害復旧と高可用性](./resiliency-disaster-recovery-high-availability-azure-applications.md)
高可用性と障害復旧の全体像を詳細に示します。参照データおよびトランザクション データの手動レプリケーションの課題についても説明しています。最後のセクションでは、複数の Azure リージョンにわたって最高レベルの可用性をもたらす、さまざまな種類の障害復旧トポロジの概要を示します。

###[概要: SQL Database を使用したクラウド ビジネス継続性とデータベース障害復旧](../sql-database/sql-database-business-continuity.md)
可用性に関する Azure SQL Database の手法に重点を置き、主としてバックアップと復元の戦略について説明します。クラウド サービスで Azure SQL Database を使用する場合は、この記事および関連するリソースを確認してください。

###[Azure 仮想マシンにおける SQL Server の高可用性と障害復旧](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md)
サービスとしてのインフラストラクチャ (IaaS) を使用してデータベース サービスをホストする場合に利用できる可用性オプションについて説明します。説明する内容は、AlwaysOn 可用性グループ、データベース ミラーリング、ログ配布、およびバックアップ/復元です。また、このような手法の使い方を示すいくつかのチュートリアルが同じセクションにあります。

###[Azure Cloud Services で大規模なサービスを設計するためのベスト プラクティス](https://azure.microsoft.com//blog/best-practices-for-designing-large-scale-services-on-windows-azure/)
非常にスケーラブルなクラウド アーキテクチャの開発について重点的に説明します。スケーラビリティ向上のために使用する手法の多くは、可用性の向上ももたらします。また、負荷の増加に応じてアプリケーションを拡張できない場合、スケーラビリティは可用性の問題になります。

###[Azure Virtual Machines における SQL Server のバックアップと復元](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md)
Azure Virtual Machines で実行されている Microsoft SQL Server のバックアップと復元の方法に関する技術的なガイダンスです。

###[フェールセーフ: 回復力のあるクラウド アーキテクチャに関するガイダンス](https://channel9.msdn.com/Series/FailSafe)
回復力のあるクラウド アーキテクチャに関するガイダンスと、Microsoft テクノロジを基盤とするこのようなアーキテクチャの実装に関するガイダンスを示し、特定のシナリオに沿ってこのようなアーキテクチャを実装する手順を説明します。

##次のステップ
この記事は、[Azure の回復性技術ガイダンス](./resiliency-technical-guidance.md)について重点的に説明した一連の記事の一部です。このシリーズの他の記事に関心がある場合は、この次の「[ローカル障害からの復旧](./resiliency-technical-guidance-recovery-local-failures.md)」というタイトルの記事を最初にお読みください。

<!---HONumber=AcomDC_0525_2016-->