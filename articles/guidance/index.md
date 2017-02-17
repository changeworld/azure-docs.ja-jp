---
title: "Azure ガイダンスのドキュメント - チュートリアル | Microsoft Docs"
description: "Azure のベスト プラクティスとガイダンス"
services: guidance
author: carolz
manager: carolz
layout: LandingPage
ms.assetid: 
ms.service: guidance
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing-page
ms.date: 01/23/2017
ms.author: carolz
translationtype: Human Translation
ms.sourcegitcommit: f5ced8f436f4d1426e4d2b307fcfb6b644e94aaf
ms.openlocfilehash: c05d311633b9c9aa5490e55baafe83376d0c8fbc

---
# <a name="azure-guidance"></a>Azure ガイダンス
[!INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Microsoft patterns & practices チームは、Azure Customer Advisory Team の一員です。 チームの目的は、開発者、設計者、IT 担当者が Microsoft Azure Platform を適切に使用できるよう支援することです。 私たちチームでは、Azure でクラウド ソリューションを構築する際のベスト プラクティスを示すガイダンスを作成しました。

## <a name="checklists"></a>Checklists
以下のリストは、可用性とスケーラビリティの基本的な側面を確認するためのクイック リファレンスです。 

* [可用性のチェックリスト][AvailabilityChecklist] 
  
    可用性を確保するための推奨プラクティスの概要です。
* [スケーラビリティのチェックリスト][ScalabilityChecklist]
  
    スケーラブルなサービスの設計および実装とデータ管理の扱いに関する推奨プラクティスの概要です。

## <a name="best-practices-articles"></a>ベスト プラクティス記事
以下の記事では、クラウド コンピューティングと関連することの多い重要な概念について詳細に説明しています。 

* [API 設計][APIDesign] 
  
    Web API の設計で考慮する必要がある設計上の問題についての説明です。
* [API 実装][APIImplementation] 
  
    Web API の実装および公開に関する推奨プラクティスをまとめています。
* [Azure のセキュリティに関するガイダンス](https://github.com/mspnp/azure-guidance/blob/master/API-security.md) 
  
    認証と承認に関する検討事項 (トークンの種類、承認プロトコル、承認フロー、および脅威の軽減など) についての説明です。
* [自動スケール ガイダンス][AutoscalingGuidance] 
  
    手動による操作を行わずにソリューションのスケール調整を行う際の検討事項の概要です。
* [バックグラウンド ジョブのガイダンス][BackgroundJobsGuidance] 
  
    フォアグラウンド操作および対話型操作とは独立してバックグラウンドで実行されるタスクの実装で使用可能なオプションと推奨プラクティスについての説明です。
* [コンテンツ配信ネットワーク (CDN) のガイダンス][CDNGuidance] 
  
    CDN を使用してアプリケーションにかかる負荷を最小限に抑え可用性とパフォーマンスを最大化する際の、一般的なガイダンスと推奨プラクティスです。
* [キャッシュに関するガイダンス][CachingGuidance] 
  
    キャッシュを使用してシステムのパフォーマンスとスケーラビリティを改善する方法の概要です。
* [データのパーティション分割のガイダンス][DataPartitioningGuidance]
  
    パーティション分割を使用してスケーラビリティを向上させ、競合を少なくし、パフォーマンスを最適化する際に役立つ方針を示します。
* [監視と診断のガイダンス][MonitoringandDiagnosticsGuidance] 
  
    ユーザーによるシステムの使用方法の追跡、リソースの使用率のトレース、およびシステムの正常性とパフォーマンスの総合的な監視に関するガイダンスです。
* [推奨される名前付け規則][naming-conventions] 
  
    Azure リソースの推奨される名前付け規則。
* [再試行の一般的なガイダンス][RetryGeneralGuidance] 
  
    一時的エラーに関する一般概念についての説明です。
* [再試行サービス固有のガイダンス][RetryServiceSpecificGuidance]
  
    多くの Azure サービスの再試行メカニズム機能についての、各サービスの再試行メカニズムを使用、応用、拡張するために役立つ情報を含めた概要です。

## <a name="scenario-guides"></a>シナリオのガイド
* [Azure で Elasticsearch を実行する][elasticsearch] 
  
    Elasticsearch は、非常にスケーラブルなオープンソースの検索エンジンとデータベースです。 これは、大規模なデータセットに含まれる情報を高速で検索し、分析する場合に最適です。 このガイダンスでは、Elasticsearch クラスターを設計するときに考慮する必要がある、いくつかの重要な側面について説明します。
* [マルチテナント アプリケーションの ID 管理][identity-multitenant] 
  
    マルチテナントとは、複数のテナントでアプリケーションを共有していながら、各テナントは互いに分離されているアーキテクチャのことです。 このガイダンスでは、[Azure Active Directory][AzureAD] を使用してサインインと認証を処理し、マルチテナント アプリケーションのユーザー ID を管理する方法について説明します。
* [ビッグ データ ソリューションの開発](https://msdn.microsoft.com/library/dn749874.aspx)
  
    このガイドでは、反復探索などのシナリオで HDInsight をデータ ウェアハウスとして使用して ETL プロセスを行う方法と、既存の BI システムへの統合について説明します。 また、ビッグ データという概念の詳細、ビッグ データ ソリューションの計画と設計、およびこうしたソリューションの実装に関するガイダンスも含まれています。

## <a name="patterns"></a>パターン
* [クラウドの設計パターン: クラウド アプリケーションのアーキテクチャに関する規範的なガイダンス](https://msdn.microsoft.com/library/dn568099.aspx)
  
    クラウド設計パターンは、設計パターンおよび関連するガイダンス トピックをまとめたものです。 パターンを適用することのメリットについて、クラウド アプリケーション アーキテクチャに各ピースを適合させる方法示しながら説明します。
* [クラウド アプリケーションのパフォーマンスの最適化](https://github.com/mspnp/performance-optimization)
  
    このガイダンスでは、負荷がかかるとアプリを拡張できなくなる一般的なアンチパターンについて説明します。 8 種類のアンチパターンを示したサンプルと、[パフォーマンス分析の手引き](https://github.com/mspnp/performance-optimization/blob/master/Performance-Analysis-Primer.md)および[主要なメトリックによるパフォーマンス評価のガイド](https://github.com/mspnp/performance-optimization/blob/master/Assessing-System-Performance-Against-KPI.md)が含まれています。

## <a name="reference-architectures"></a>参照用アーキテクチャ
シナリオ別に参照用アーキテクチャが用意されています。
各アーキテクチャには、推奨プラクティスと規範となる手順、および推奨事項を取り入れた実行可能なコンポーネントが含まれています。

最新の参照用アーキテクチャ ライブラリは [http://aka.ms/architecture](http://aka.ms/architecture) にあります。

## <a name="resiliency-guidance"></a>回復性のガイダンス
以下のトピックでは、分散クラウド環境での障害に対して回復性を持つアプリケーションを設計する方法について説明しています。   

* [回復性の概要][ResiliencyOvervew]
  
     障害から回復可能であり、継続して動作することのできるアプリケーションを Azure プラットフォームで構築する方法を示します。 設計から実装、デプロイ、運用にわたり回復性を実現する体系化された方法について説明します。
* [回復性のチェックリスト][resiliency-checklist]
  
    発生する可能性のあるさまざまな障害モードへの備えに役立つ推奨事項のチェックリストです。
* [障害モードの分析][resiliency-fma] 
  
    障害モード分析 (FMA) は、考えられる障害点を特定することによって、システムに回復力を持たせるためのプロセスです。 この記事では、FMA プロセスの出発点として、起こりうる障害モードの一覧とその緩和方法について説明します。 

<!-- links -->

[AzureAD]: https://azure.microsoft.com/documentation/services/active-directory/

[PerformanceOptimization]: https://github.com/mspnp/performance-optimization

[APIDesign]: ../best-practices-api-design.md
[APIImplementation]: ../best-practices-api-implementation.md
[AutoscalingGuidance]: ../best-practices-auto-scaling.md
[BackgroundJobsGuidance]: ../best-practices-background-jobs.md
[CDNGuidance]: ../best-practices-cdn.md
[CachingGuidance]: ../best-practices-caching.md
[DataPartitioningGuidance]: ../best-practices-data-partitioning.md
[MonitoringandDiagnosticsGuidance]: ../best-practices-monitoring.md
[RetryGeneralGuidance]: ../best-practices-retry-general.md
[RetryServiceSpecificGuidance]: ../best-practices-retry-service-specific.md
[RetryPolicies]: Retry-Policies.md
[ScalabilityChecklist]: ../best-practices-scalability-checklist.md
[AvailabilityChecklist]: ../best-practices-availability-checklist.md
[naming-conventions]: guidance-naming-conventions.md

<!-- guidance projects -->
[elasticsearch]: guidance-elasticsearch.md
[identity-multitenant]: guidance-multitenant-identity.md

<!-- reference architectures -->
[ref-arch-single-vm-windows]: guidance-compute-single-vm.md
[ref-arch-single-vm-linux]: guidance-compute-single-vm-linux.md
[ref-arch-multi-vm]: guidance-compute-multi-vm.md
[ref-arch-3-tier]: guidance-compute-3-tier-vm.md
[ref-arch-n-tier-windows]: guidance-compute-n-tier-vm.md
[ref-arch-n-tier-linux]: guidance-compute-n-tier-vm-linux.md
[ref-arch-multi-dc-windows]: guidance-compute-multiple-datacenters.md
[ref-arch-multi-dc-linux]: guidance-compute-multiple-datacenters-linux.md

<!-- resiliency -->
[resiliency-fma]: guidance-resiliency-failure-mode-analysis.md
[resiliency-checklist]: guidance-resiliency-checklist.md
[ResiliencyOvervew]: guidance-resiliency-overview.md




<!--HONumber=Feb17_HO2-->


