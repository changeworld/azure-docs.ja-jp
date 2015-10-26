<properties 
   pageTitle="オフサイトのバッチ処理層 (Azure アーキテクチャ パターン)" 
   description="オフサイトのバッチ処理層パターンは、CPIF アーキテクチャのドキュメントで詳しく説明されているインフラストラクチャ領域の一部です。" 
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

# オフサイトのバッチ処理層 (Azure アーキテクチャ パターン)

[Cloud Platform Integration Framework (CPIF)](azure-architectures-cpif-overview.md) は、アプリケーションを Microsoft クラウド ソリューションにオンボードするためのワークロード統合ガイダンスを提供します。

CPIF は、組織、顧客、パートナー向けに、Azure、System Center、Windows Server のハイブリッド クラウド プラットフォームおよび管理機能を使用して、クラウドを対象とするワークロードを設計およびデプロイする方法を記述します。

**オフサイトのバッチ処理層**パターンは、CPIF アーキテクチャのドキュメントで詳しく説明されている**インフラストラクチャ**領域の一部です。

##  オフサイトのバッチ処理層

オフサイトのバッチ処理層設計パターンは、フォールト トレラントでありかつスケーラブルなバックエンド データ処理を提供するために必要な Azure の機能とサービスの詳細を記述します。これらのサービスは、現在任意の Azure データ センターにデプロイできる、Azure のクラウド サービスの worker ロールとして実現されます。

バッチ処理のワークロードには、通常ユーザー インターフェイスをほとんどまたはまったく提供しないという特徴があります。この種類のオンプレミスのワークロードの例として、Windows Server で実行されている Windows サービスが挙げられます。クラウド環境でのこの種類のワークロードを検討した場合、本当に必要なものがコンピューティング、ストレージ、ネットワーク接続であるときにワークロードを実行するためにサーバー全体をデプロイするのは無駄になります。worker ロールは、Azure のこの操作の実装です。

定義上、Azure で実行されるバッチ処理ジョブは、リソースに接続し、ビジネス ロジックを提供し (コンピューティング)、出力を提供するワークロードです。入力リソースと出力リソースは、ユーザーによって定義されます。フラット ファイル、Azure BLOB ストレージ内の BLOB、NoSQL データベース、リレーショナル データベースなどを使用できます。

ビジネス ロジックは、通常、必要なビジネス ロジックを .NET ライブラリで定義することによって、Azure worker ロールで実装されます。Azure への worker ロールのデプロイメントは単純な操作ですが、フォールト トレラントでありかつスケーラブルな worker ロールをデプロイするには、Azure でのサービスの実行方法および管理方法を考慮した設計が必要になります。このパターンでは、これらの要件を考慮した設計とその実装方法について詳しく説明します。

## アーキテクチャ パターンの概要 

このドキュメントでは、Azure のクラウド サービスに含まれる worker ロール インスタンスを使用して、オフサイトのバッチ処理を実行するためのパターンについて説明します。この設計の重要なコンポーネントを次に以下に示します。この図は、フォールト トレランスを実現するために最低限必要なインスタンスを示しています。追加のインスタンスをデプロイすると、サービスのパフォーマンスを向上させることができます。さらに、自動スケーリングを有効にすると、時間やその他のサーバー メトリックに基づくインスタンスの拡大縮小を支援することができます。

##  その他のリソース
[バッチ処理層 (PDF)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

## 関連項目
[CPIF アーキテクチャ](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

[グローバル負荷分散 Web 層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

[負荷分散データ層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[ハイブリッド ネットワーク](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Azure Search 層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

<!---HONumber=Oct15_HO3-->