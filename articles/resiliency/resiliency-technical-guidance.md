---
title: 回復性技術ガイダンスのインデックス | Microsoft Docs
description: 回復力のあるアプリケーション、高可用性のアプリケーション、およびフォールト トレラント アプリケーションの内容および設計方法、ならびに障害復旧とビジネス継続性の計画策定に関する技術情報記事のインデックス
services: ''
documentationcenter: na
author: adamglick
manager: saladki
editor: ''

ms.service: resiliency
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: aglick

---
# Azure の回復性技術ガイダンス
## はじめに
高可用性と障害復旧の要件を満たすには、次の 2 種類の知識が必要です。

* クラウド プラットフォームの機能についての技術面の詳細な理解
* 分散サービスを適切に構築する方法についての知識

このシリーズの記事では、前者 (回復性に関する Azure プラットフォームの機能と制限) について説明します。回復性のことをビジネス継続性と言う場合もあります。後者について関心がある場合は、[Azure アプリケーションの障害復旧と高可用性](https://aka.ms/drtechguide)に重点を置いた記事シリーズを参照してください。このシリーズの記事ではアーキテクチャと設計パターンに触れていますが、それはこのシリーズの中心ではありません。設計のガイダンスについては、「[その他のリソース](#additional-resources)」セクションに示されている資料を参照してください。

この情報を構成する記事は次のとおりです。

* [ローカル障害からの復旧](resiliency-technical-guidance-recovery-local-failures.md)に関する記事。物理ハードウェア (ドライブ、サーバー、ネットワーク デバイスなど) では、障害が発生することがあります。負荷が急増すると、リソースが使い果たされることもあります。この記事では、このような条件下で高可用性を維持するために Azure に用意されている機能について説明します。
* [Azure リージョン全体のサービス中断からの復旧](resiliency-technical-guidance-recovery-loss-azure-region.md)に関する記事。広範囲にわたる障害はまれですが、理論的には発生する可能性があります。リージョン全体がネットワーク障害によって孤立することや、自然災害によって物理的に被害を受けることがあり得ます。この記事では、Azure を使用して、地理的にさまざまなリージョンにまたがるアプリケーションを作成する方法について説明します。
* [オンプレミスから Azure への復旧](resiliency-technical-guidance-recovery-on-premises-azure.md)に関する記事。クラウドによって障害復旧のコストが大きく変わり、組織は Azure を使用して復旧のためのもう 1 つのサイトを確立することが可能になります。これは、セカンダリ データセンターを構築して維持する場合の何分の 1 かのわずかなコストで実現できます。この記事では、オンプレミスのデータセンターをクラウドに拡張するために Azure で提供される機能について説明します。
* [データの破損または偶発的な削除からの復旧](resiliency-technical-guidance-recovery-data-corruption.md)に関する記事。アプリケーションのバグによって、データが破損することがあります。オペレーターが誤って重要なデータを削除してしまうこともあります。この記事では、データのバックアップと以前の時点への復元のために Azure で提供される機能について説明します。

## その他のリソース
* [Microsoft Azure 上に構築されたアプリケーションの障害復旧と高可用性](resiliency-disaster-recovery-high-availability-azure-applications.md)。この記事では、高可用性と障害復旧の全体像を詳細に取り上げています。参照データおよびトランザクション データの手動レプリケーションの課題についても説明しています。最後のセクションでは、複数の Azure リージョンにわたって最高レベルの可用性をもたらす、さまざまな種類の障害復旧トポロジの概要を示します。
* [High-availability checklist (高可用性のチェックリスト)](resiliency-high-availability-checklist.md)。この記事では、アプリケーションの回復性と可用性を上げるための機能、サービス、設計を一覧にしてまとめています。
* [Microsoft Azure サービスの回復性ガイダンス](resiliency-service-guidance-index.md)。この記事は Azure サービスの索引となっており、障害復旧ガイドとデザイン ガイドの両方へのリンクがあります。
* [概要: SQL Database を使用したクラウド ビジネス継続性とデータベース障害復旧](../sql-database/sql-database-business-continuity.md)。この記事では、可用性に関する Azure SQL Database の手法について説明しています。バックアップと復元の戦略を中心に説明します。クラウド サービスで Azure SQL Database を使用する場合は、この記事および関連するリソースを確認してください。
* [Azure 仮想マシンにおける SQL Server の高可用性と障害復旧](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md)。この記事では、サービスとしてのインフラストラクチャ (IaaS) を使用してデータベース サービスをホストする場合に利用できる可用性オプションについて説明しています。説明する内容は、AlwaysOn 可用性グループ、データベース ミラーリング、ログ配布、およびバックアップ/復元です。いくつかのチュートリアルで、これらの手法の使用方法を示しています。
* [Azure Cloud Services で大規模なサービスを設計するためのベスト プラクティス](https://azure.microsoft.com//blog/best-practices-for-designing-large-scale-services-on-windows-azure/)に関する記事。この記事では、非常にスケーラブルなクラウド アーキテクチャの開発について重点的に説明しています。スケーラビリティ向上のために使用する手法の多くは、可用性の向上ももたらします。また、負荷の増加に応じてアプリケーションを拡張できない場合、スケーラビリティは可用性の問題になります。
* [Azure Virtual Machines における SQL Server のバックアップと復元](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md)。この記事は、Azure Virtual Machines で実行されている Microsoft SQL Server のバックアップと復元の方法に関する技術的なガイダンスです。
* [Failsafe: Guidance for resilient cloud architectures (フェールセーフ: 回復力のあるクラウド アーキテクチャに関するガイダンス)](https://channel9.msdn.com/Series/FailSafe)。この記事では、回復力のあるクラウド アーキテクチャに関するガイダンスと、Microsoft テクノロジを基盤とするこのようなアーキテクチャの実装に関するガイダンスを示し、特定のシナリオに沿ってこのようなアーキテクチャを実装する手順を説明しています。
* [Technical case study: Using cloud technologies to improve disaster recovery (技術的なケース スタディ: クラウド技術で障害復旧機能を高める)](https://www.microsoft.com/itshowcase/Article/Content/737/Using-cloud-technologies-to-improve-disaster-recovery)。このケース スタディは、Microsoft IT が Azure を利用して障害復旧機能を改善した方法について説明しています。

## 次のステップ
この記事は、Azure の回復性の技術ガイダンスについて重点的に説明したシリーズの一部です。このシリーズの他の記事に関心がある場合は、[ローカル障害からの復旧](resiliency-technical-guidance-recovery-local-failures.md)に関する記事からお読みください。

<!---HONumber=AcomDC_0824_2016-->