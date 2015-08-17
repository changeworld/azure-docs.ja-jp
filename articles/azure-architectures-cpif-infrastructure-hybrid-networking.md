<properties 
   pageTitle="ハイブリッド ネットワーク (Azure アーキテクチャ パターン)" 
   description="ハイブリッド ネットワーク パターンは、CPIF アーキテクチャのドキュメントで詳しく説明されているインフラストラクチャ領域の一部です。" 
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

# ハイブリッド ネットワーク (Azure アーキテクチャ パターン)

[Cloud Platform Integration Framework (CPIF)](azure-architectures-cpif-overview.md) は、アプリケーションを Microsoft クラウド ソリューションにオンボードするためのワークロード統合ガイダンスを提供します。

CPIF は、組織、顧客、パートナー向けに、Azure、System Center、Windows Server のハイブリッド クラウド プラットフォームおよび管理機能を使用して、クラウドを対象とするワークロードを設計およびデプロイする方法を記述します。

**ハイブリッド ネットワーク** パターンは、CPIF アーキテクチャのドキュメントで詳しく説明されている**インフラストラクチャ**領域の一部です。

##  ハイブリッド ネットワーク

ハイブリッド ネットワーク設計パターンは、地理的な境界を越えて予測可能なパフォーマンスと高可用性を提供できるネットワーク機能を提供するために必要な Azure の機能とサービスの詳細を記述します。Microsoft Azure のリージョンの完全な一覧と各リージョン内で使用できるサービスの一覧については、Microsoft Azure のドキュメント サイトを参照してください。このドキュメントでは、ハイブリッド環境向けの Microsoft Azure のネットワーク機能の概要を示します。Microsoft Azure Virtual Networking では、論理的に分離されたネットワークを Azure に作成し、それらをインターネット接続またはプライベート ネットワーク接続を使用してオンプレミスのデータ センターに安全に接続することができます。さらに、個々のクライアント コンピューターは、IPsec VPN 接続を使用して、分離された Azure ネットワークに接続できます。

ハイブリッド ネットワーク アーキテクチャ パターンには、次の重点領域が含まれています。

- オンプレミスのネットワークを Azure に接続する 
- リージョン間で Azure 仮想ネットワークを拡張する 
- サブスクリプション間で Azure 仮想ネットワークを拡張する 
- リモート ネットワーク アクセスを開発者に提供する 

## アーキテクチャ パターンの概要 

ハイブリッド ネットワーク アーキテクチャ パターンは、作成できるシナリオの数が数多くあるため、複雑になります。このアーキテクチャ パターンでは、次の 4 つのシナリオに重点を置いています。

- 単一のサブスクリプションとリージョン内でマルチホップの仮想ネットワーク ルーティングを使用した、サイト間のハイブリッド ネットワーク 
- サブスクリプションとリージョンにわたってマルチホップの仮想ネットワーク ルーティングを使用した、サイト間のハイブリッド ネットワーク 
- MPLS 接続を使用した ExpressRoute ハイブリッド ネットワーク 
- IXP 接続を使用した ExpressRoute ハイブリッド ネットワーク 

##  その他のリソース
[ハイブリッド ネットワーク (PDF)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

## 関連項目
[CPIF アーキテクチャ](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

[グローバル負荷分散 Web 層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

[負荷分散データ層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Azure Search 層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

[バッチ処理層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

<!---HONumber=August15_HO6-->