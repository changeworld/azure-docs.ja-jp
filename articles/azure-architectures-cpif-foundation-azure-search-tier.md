<properties 
   pageTitle="Azure Search 層 (Azure アーキテクチャ パターン)" 
   description="Azure Search 層パターンは、CPIF アーキテクチャのドキュメントで詳しく説明されているファンデーション領域の一部です" 
   services="" 
   documentationCenter="" 
   authors="arynes" 
   manager="fredhar" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple" 
   ms.date="03/25/2015"
   ms.author="arynes"/>

# Azure Search 層 (Azure アーキテクチャ パターン)

[Cloud Platform Integration Framework (CPIF)](azure-architectures-cpif-overview.md) は、アプリケーションを Microsoft クラウド ソリューションにオンボードするためのワークロード統合ガイダンスを提供します。

CPIF は、組織、顧客、パートナー向けに、Azure、System Center、Windows Server のハイブリッド クラウド プラットフォームおよび管理機能を使用して、クラウドを対象とするワークロードを設計およびデプロイする方法を記述します。

**Azure Search 層**パターンは、CPIF アーキテクチャのドキュメントで詳しく説明されている**ファンデーション**領域の一部です。

##  Azure Search 層

Azure Search 層設計パターンは、地理的な境界を越えて予測可能なパフォーマンスと高可用性を提供できる検索サービスを提供するために必要な Azure の機能とサービスの詳細を記述し、検索ソリューションの提供にあたって Azure Search を使用するためのアーキテクチャ パターンを提供します。Azure Search は、Microsoft Azure に組み込まれた "サービスとしての検索" です。これにより、開発者は、検索機能をアプリケーションに追加するためにインフラストラクチャ サービスをデプロイ、管理、維持する必要なく、アプリケーションに検索機能を組み込むことができます。このパターンの目的は、さまざまな状況と設計に対応する、反復使用が可能なソリューションを提供することです。

## アーキテクチャ パターンの概要 

このドキュメントでは、Azure Search を使用するためのコア パターンと、サービスのアーキテクチャの範囲を示すコアの 2 つのバリエーションについて説明します。コア パターンは、Azure Search とそれに関連する Azure サービスから構成され、エンド ツー エンドの設計を作成するためのガイダンスを提供します。このセクションには、パターンのバリエーション (具体的には共有サービス パターンと同時実行パターン) も含まれています。これらのバリエーションは、さまざまな要件、サービス レベル アグリーメント (SLA)、その他の特定の条件に基づいたガイダンスを提供します。

##  その他のリソース
[Azure Search 層 (PDF)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

## 関連項目
[CPIF アーキテクチャ](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

[グローバル負荷分散 Web 層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

[負荷分散データ層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[ハイブリッド ネットワーク](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[バッチ処理層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

<!---HONumber=Oct15_HO3-->