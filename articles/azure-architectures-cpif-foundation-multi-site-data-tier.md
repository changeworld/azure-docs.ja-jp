<properties 
   pageTitle="複数サイト データ層 (Azure アーキテクチャ パターン)" 
   description="複数サイト データ層パターンは、CPIF アーキテクチャのドキュメントで詳しく説明されているファンデーション領域の一部です。" 
   services="" 
   documentationCenter="" 
   authors="arynes" 
   manager="fredhar" 
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple" 
   ms.date="03/25/2015"
   ms.author="arynes"/>

# 複数サイト データ層 (Azure アーキテクチャ パターン)

[Cloud Platform Integration Framework (CPIF)](azure-architectures-cpif-overview.md) は、アプリケーションを Microsoft クラウド ソリューションにオンボードするためのワークロード統合ガイダンスを提供します。

CPIF は、組織、顧客、パートナー向けに、Azure、System Center、Windows Server のハイブリッド クラウド プラットフォームおよび管理機能を使用して、クラウドを対象とするワークロードを設計およびデプロイする方法を記述します。

**複数サイト データ層**パターンは、CPIF アーキテクチャのドキュメントで詳しく説明されている**ファンデーション**領域の一部です。

## 複数サイト データ層

複数サイト データ層設計パターンは、地理的な境界を越えて予測可能なパフォーマンスと高可用性を提供できるデータ層サービスを提供するために必要な Azure の機能とサービスの詳細を記述します。この設計パターンの目的上、データ層は、分離された方法または多層アプリケーションの一部として従来のデータ プラットフォーム サービスを提供するサービスの階層として定義されます。このパターン内では、データ層の負荷分散は、リージョン内およびリージョン間の両方でローカルに提供されます。

SQL Server 2012 で導入された AlwaysOn 可用性グループは、Azure Infrastructure Service 上に完全にサポートされる高可用性および障害復旧機能です。Microsoft Azure Infrastructure Service 上の AlwaysOn 可用性グループに関する詳細な情報と正式なサポートのアナウンスについては、AlwaysOn 可用性グループに関するページを参照してください。

このドキュメントでは、SQL AlwaysOn 可用性グループを使用する、Azure の複数サイト データ層のアーキテクチャの概要を示します。機能の追加や障害復旧を実現するには、追加の Azure データセンターにオプションの読み取り専用セカンダリ ノードを配置します。Azure で SQL AlwaysOn を使用すると、Web またはアプリケーション層で使用できる高可用性データ層を提供できます。

このドキュメントでは、アーキテクチャのパターンとプラクティスを中心に説明しています。完全なデプロイメントのガイダンスについては、Azure での AlwaysOn 可用性グループの構成と AlwaysOn 可用性グループ リスナーの構成が概要されている公式のチュートリアルを参照してください。

## アーキテクチャ パターンの概要 

このドキュメントでは、可用性と冗長性を実現するために複数の地域をまたいで Microsoft SQL Server コンテンツへのアクセスを提供するためのパターンについて説明します。データ自体にアクセスするアプリケーションまたは Web 層に関係なく重要なサービスを次に示します。該当するサービスとこれらのサービスがこのパターンの一部としてどのように使用されるかを次の図に簡単に示します。

それぞれのメイン サービス領域については、図の後に詳しく説明します。
 
![リソース ブレードとリソース グループのブレードの [タグ] 部分](./media/azure-architectures-cpif-foundation-multi-site-data-tier/overview.png)

##  その他のリソース
[負荷分散データ層 (PDF)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

## 関連項目
[CPIF アーキテクチャ](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

[グローバル負荷分散 Web 層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

[ハイブリッド ネットワーク](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Azure Search 層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

[バッチ処理層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

<!---HONumber=Oct15_HO4-->