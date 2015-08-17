<properties 
   pageTitle="グローバル負荷分散 Web 層 (Azure アーキテクチャ パターン)" 
   description="グローバル負荷分散 Web 層パターンは、CPIF アーキテクチャのドキュメントで詳しく説明されているファンデーション領域の一部です。" 
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

# グローバル負荷分散 Web 層 (Azure アーキテクチャ パターン)

[Cloud Platform Integration Framework (CPIF)](azure-architectures-cpif-overview.md) は、アプリケーションを Microsoft クラウド ソリューションにオンボードするためのワークロード統合ガイダンスを提供します。

CPIF は、組織、顧客、パートナー向けに、Azure、System Center、Windows Server のハイブリッド クラウド プラットフォームおよび管理機能を使用して、クラウドを対象とするワークロードを設計およびデプロイする方法を記述します。

**グローバル負荷分散 Web 層**パターンは、CPIF アーキテクチャのドキュメントで詳しく説明されている**ファンデーション**領域の一部です。

##  グローバル負荷分散 Web 層

グローバル負荷分散 Web 層設計パターンは、地理的な境界を越えて予測可能なパフォーマンスと高可用性を提供できる Web 層サービスを提供するために必要な Azure の機能とサービスの詳細を記述します。

この設計パターンの目的上、Web 層は、分離された方法または多層 Web アプリケーションの一部として従来の HTTP/HTTPS コンテンツまたはアプリケーション サービスを提供するサービスの階層として定義されます。このパターン内では、Web 層の負荷分散は、リージョン内およびリージョン間の両方でローカルに提供されます。コンピューティングの観点からは、これらのサービスは、Microsoft Azure 仮想マシン、Web サイト、またはその 2 つの組み合わせを通じて提供できます。Web サービスを提供する仮想マシンでは、Microsoft Azure 内でサポートされている任意の Microsoft Windows または Linux ディストリビューション ゲスト オペレーティング システムを使用して、コンテンツをホストできます。


## アーキテクチャ パターンの概要 

このドキュメントでは、可用性と冗長性を実現するために複数の地域をまたいで Web サービスまたは Web サーバー コンテンツへのアクセスを提供するためのパターンについて説明します。Web プラットフォームの制約や Web サービス内での開発方法論に関係なく重要なサービスを次に示します。このパターンには、(Azure でサポートされるオペレーティング システムとファミリを使用して) 仮想マシン上で Web コンテンツまたはサービスをホストするバリエーションと、Azure Websites を使用するバリエーションの 2 つがあります。仮想マシンの例を使用して、該当するサービスとこれらのサービスがこのパターンの一部としてどのように使用されるかを次の図に簡単に示します。

##  その他のリソース
[グローバル負荷分散 Web 層 (PDF)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

## 関連項目
[CPIF アーキテクチャ](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

[負荷分散データ層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[ハイブリッド ネットワーク](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Azure Search 層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

[バッチ処理層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

<!---HONumber=August15_HO6-->