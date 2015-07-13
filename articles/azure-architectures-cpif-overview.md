<properties 
   pageTitle="Cloud Platform Integration Framework - Azure アーキテクチャ パターン" 
   description="Cloud Platform Integration Framework は、アプリケーションを Microsoft Azure のアーキテクチャ パターンで構成される Microsoft クラウド ソリューションにオンボードするためのワークロード統合ガイダンスを提供します。" 
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


# Cloud Platform Integration Framework (Azure アーキテクチャ パターン)

Cloud Platform Integration Framework は、アプリケーションを Microsoft クラウド ソリューションにオンボードするためのワークロード統合ガイダンスを提供します。

CPIF は、組織、顧客、パートナー向けに、Azure、System Center、Windows Server のハイブリッド クラウド プラットフォームおよび管理機能を使用して、クラウドを対象とするワークロードを設計およびデプロイする方法を記述します。CPIF ドメインは、次の機能領域に分解されます。

![リソース ブレードとリソース グループのブレードの [タグ] 部分](./media/azure-architecture-cpif-overview/overview.png)

##  CPIF アーキテクチャ

パブリック クラウド環境とプライベート クラウド環境には、複雑なワークロードの実行をサポートするための共通の要素があります。これらのアーキテクチャは、従来のオンプレミスの物理環境および仮想環境で比較的よく理解されていますが、Microsoft Azure の構造上、パブリック クラウド環境のインフラストラクチャおよびプラットフォーム機能を合理的に処理するには追加の計画が必要です。Azure でホストされるアプリケーションまたはサービスの開発をサポートするには、特定のワークロードのソリューションを作成するために必要なさまざまなコンポーネントの概略を示す一連のパターンが必要になります。

これらのアーキテクチャ パターンは、次のカテゴリに分類されます。

- **インフラストラクチャ** - Microsoft Azure は、いくつかの基になるサービスと機能から構成される、サービスとしてのインフラストラクチャおよびプラットフォーム ソリューションです。これらのサービスは、コンピューティング、ストレージ、ネットワーク サービスに大きく分解できますが、これらの定義に該当しないいくつかの機能があります。インフラストラクチャ パターンは、特定のサービスを特定の Azure サブスクリプション内でホストされる 1 つ以上のソリューションに提供するために必要な、Microsoft Azure の特定の機能領域を詳しく記述します。 
- **ファンデーション** - Azure 内で多層アプリケーションまたはサービスを作成するときは、いくつかのコンポーネントを組み合わせて適切なホスティング環境を提供する必要があります。ファンデーション パターンは、Microsoft Azure の 1 つ以上のサービスの基礎を構成して、アプリケーション内の特定の機能のレイヤーをサポートします。そのためには、上記のインフラストラクチャ パターンで記述される 1 つ以上のコンポーネントを使用することが必要になる場合があります。たとえば、多層アプリケーションのプレゼンテーション レイヤーが動作するには、Azure 内にコンピューティング、ネットワーク、ストレージの各機能が必要になります。ファンデーション パターンは、特定のソリューションの一部として、他のパターンと組み合わせて使用します。
- **ソリューション** - ソリューション パターンは、インフラストラクチャ パターンおよびファンデーション パターンから構成され、開発するエンド アプリケーションまたはサービスを表します。通常、複雑なソリューションを他のパターンから独立して開発することはありません。それどころか、複雑なソリューションでは、上記のそれぞれのパターンで定義されたコンポーネントやインターフェイスを使用する必要があります。    

## Azure アーキテクチャ パターンの概念

Azure でのソリューション アーキテクチャの開発をサポートするために、一般的なシナリオに対応する一連のパターン ガイドが提供されています。これらのシナリオ ガイドは時間をかけて作成される予定です。現在想定されているパターンには、次のパターンが含まれます。

- グローバル負荷分散 Web 層 
- 負荷分散データ層
- バッチ処理層
- ハイブリッド ネットワーク
- Azure Search 

##  その他のリソース
[CPIF アーキテクチャ (PDF)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

## 関連項目
[グローバル負荷分散 Web 層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

[負荷分散データ層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[バッチ処理層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

[Azure のネットワーク](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Azure Search](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

<!---HONumber=July15_HO1-->