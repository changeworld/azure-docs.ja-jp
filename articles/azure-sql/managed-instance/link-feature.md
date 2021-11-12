---
title: リンク機能
titleSuffix: Azure SQL Managed Instance
description: SQL Server からクラウドにデータを継続的にレプリケートしたり、可能な限り短いダウンタイムで SQL Server データベースを移行したりするための、Azure SQL Managed Instance のリンク機能について説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1, ignite-fall-2021
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: mathoma, danil
ms.date: 11/05/2021
ms.openlocfilehash: 19851f1ff3bef1ab57d23013ada78bec5665b7c5
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131989809"
---
# <a name="link-feature-for-azure-sql-managed-instance-limited-preview"></a>Azure SQL Managed Instance のリンク機能 (制限付きプレビュー)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance の新しいリンク機能を使うと、任意の場所でホストされている SQL Server を SQL Managed Instance に接続でき、ハイブリッドの柔軟性とデータベースのモビリティが提供されます。 クラウドへのほぼリアルタイムのデータ レプリケーションを使うアプローチにより、Azure の読み取り専用セカンダリにワークロードをオフロードして、Azure のみの機能、パフォーマンス、スケールを利用できます。 

破滅的なイベントが発生した後も、Azure の SQL Managed Instance で読み取り専用ワークロードを実行し続けることができます。 また、1 つまたは複数のアプリケーションを、SQL Server から SQL Managed Instance に、同時に、お客様のペースで、現在の Azure の他のソリューションと比較して可能な最短のダウンタイムで、移行することもできます。

> [!NOTE]
> リンク機能は、制限付きパブリック プレビューでリリースされており、現時点では SQL Server 2019 Enterprise Edition CU13 (またはそれ以降) のみがサポートされています。 [今すぐサインアップ](https://aka.ms/mi-link-signup)して、限定パブリック プレビューに参加してください。 

## <a name="overview"></a>概要

SQL Server と SQL Managed Instance の間のほぼリアルタイムのデータ レプリケーションの基礎となるテクノロジは、よく知られていて実績のある Always On 可用性グループ テクノロジ スタックの一部である分散型可用性グループに基づいています。 オンプレミスの SQL Server 可用性グループが、安全なセキュリティ保護された方法で Azure の SQL Managed Instance に拡張されます。 

既存の可用性グループまたは複数のノードが存在する必要はありません。 リンクにより、既存の可用性グループを持たない単一ノードの SQL Server インスタンスと、既存の可用性グループを持つ複数ノードの SQL Server インスタンスがサポートされます。 このリンクにより、SQL Server データ資産全体をクラウドに移行することなく、Azure の最新のベネフィットを利用できます。

連続して何か月さらには何年でも、必要な期間だけリンクを実行し続けることができます。 また、最新化の過程では、Azure に移行する準備ができたら、このリンクにより、現在利用可能な他のすべてのオプションと比較して最小限のダウンタイムでの、大幅に改善された移行エクスペリエンスが可能になり、SQL Managed Instance への真のオンライン移行が提供されます。

## <a name="supported-scenarios"></a>サポートされるシナリオ

SQL Server から Azure SQL Managed Instance へのリンク機能によってレプリケートされるデータは、次のようなさまざまなシナリオで使うことができます。 

- **クラウドに移行することなく Azure サービスを使う** 
- **読み取り専用ワークロードを Azure にオフロードする** 
- **Azure への移行**

![Managed Instance のリンクの主要なシナリオ](./media/managed-instance-link/mi-link-main-scenario.png)


### <a name="use-azure-services"></a>Azure サービスを使用する 

リンク機能を使って、クラウドに移行することなく、SQL Server のデータを使って Azure サービスを利用します。 たとえば、レポート、分析、バックアップ、機械学習、Azure にデータを送信するその他のジョブなどです。 

### <a name="offload-workloads-to-azure"></a>ワークロードを Azure にオフロードする 

リンク機能を使って、ワークロードを Azure にオフロードすることもできます。 たとえば、アプリケーションでは、読み取り/書き込みのワークロードには SQL Server を使い、読み取り専用ワークロードは世界中の 60 以上の Azure リージョンにある SQL Managed Instance にオフロードすることができます。 リンクが確立されると、SQL Server のプライマリ データベースには読み取り/書き込みアクセスでき、Azure 内の SQL Managed Instance にレプリケートされたデータには読み取り専用でアクセスできます。 これにより、SQL Managed Instance 上のレプリケートされたデータベースを読み取りスケールアウトと Azure への読み取り専用ワークロードのオフロードに使うことができる、さまざまなシナリオが可能になります。 SQL Managed Instance では、並行して、独立した読み取り/書き込みデータベースをホストすることもできます。 これにより、レプリケートされたデータベースを同じマネージド インスタンス上の別の読み取り/書き込みデータベースにコピーして、さらにデータ処理を行うことができます。

リンクはデータベース スコープ (1 つのデータベースにつき 1 つのリンク) であり、Azure でワークロードを統合および分離できます。 たとえば、複数の SQL Server から Azure の 1 つの SQL Managed Instance にデータベースをレプリケートしたり (統合)、1 つの SQL Server から世界中の任意の Azure リージョンの複数のマネージド インスタンスに、データベースとマネージド インスタンスの間の 1 対 1 のリレーションシップを使用してデータベースをレプリケートしたり (分離) できます。 後者では、世界中のどこでも、お客様の近くのリージョンにワークロードをすばやく移動して読み取り専用レプリカとして使用する、効率的な方法が提供されます。

### <a name="migrate-to-azure"></a>Azure への移行 

リンク機能を使うと、SQL Server から SQL Managed Instance への移行も容易になり、次のことが可能になります。 

- 現在利用可能な他のすべてのソリューションと比較して、最もパフォーマンスに優れた最小のダウンタイムでの移行
- 任意のサービス レベルでの SQL Managed Instance への真のオンライン移行 

リンク機能を使うと、最小限のダウンタイムでの移行が可能になるため、プライマリ ワークロードをオンラインにしたまま、マネージド インスタンスに移行できます。 汎用サービス レベルに移行するときはこれまでも他のソリューションでオンライン移行を実現できましたが、リンク機能を使うと、ビジネス クリティカルなサービス レベルでも真のオンライン移行を行うことができます。 

## <a name="how-it-works"></a>しくみ

SQL Managed Instance のリンク機能の背後にある基になるテクノロジは分散型可用性グループです。 このソリューションでは、既存の可用性グループを持たない単一ノード システム、または既存の可用性グループを持つ複数のノード システムがサポートされています。  

![SQL Managed Instance のリンク機能が動作する方法](./media/managed-instance-link/mi-link-ag-dag.png)

オンプレミス ネットワークと Azure の間では、セキュリティ保護された接続 (VPN や ExpressRoute など) が使われます。 SQL Server が Azure VM でホストされている場合は、Azure の内部バックボーンを VM とマネージド インスタンスの間に使用できます (たとえば、グローバル VNet ピアリング)。 2 つのシステム間の信頼は、証明書ベースの認証を使用して確立されます。そのとき、SQL Server と SQL Managed Instance の間で公開キーが交換されます。

同じ、または異なる SQL Server ソースから、1 つの SQL Managed Instance に対して、最大で 100 個のリンクが存在できます。 この制限は、現時点ではマネージド インスタンスでホストできるデータベースの数によって管理されます。 同様に、1 つの SQL Server が、異なる Azure リージョンの複数のマネージド インスタンスとの間に、データベースとマネージド インスタンスの間に 1 対 1 のリレーションシップを持つ、複数の並列データベース レプリケーション リンクを確立できます。 この機能を使うには、SQL Server 2019 に CU13 以降をインストールする必要があります。

## <a name="sign-up-for-link"></a>リンクにサインアップする

リンク機能を使うには、次のものが必要です。

- オンプレミスまたは Azure VM の、[CU13 以降](https://support.microsoft.com/topic/kb5005679-cumulative-update-13-for-sql-server-2019-5c1be850-460a-4be4-a569-fe11f0adc535)がインストールされた SQL Server 2019 Enterprise Edition。
- SQL Server とマネージド インスタンスの間のネットワーク接続が必要です。 SQL Server がオンプレミスで実行されている場合は、VPN リンクまたは ExpressRoute を使います。 SQL Server が Azure VM 上で実行されている場合は、VM をマネージド インスタンスと同じサブネットにデプロイするか、グローバル VNet ピアリングを使って 2 つの異なるサブネットを接続します。 
- 任意のサービス レベルでプロビジョニングされた Azure SQL Managed Instance。

リンク機能の限定プレビューにサインアップするには、次のリンクを使ってください。 

> [!div class="nextstepaction"]
> [リンク機能のプレビューにサインアップする](https://aka.ms/mi-link-signup)

## <a name="next-steps"></a>次のステップ

リンク機能について詳しくは、以下をご覧ください。

- [Managed Instance リンク – SQL Server を再仮定された Azure に接続します](https://aka.ms/mi-link-techblog)。

他のレプリケーション シナリオについては、以下をご検討ください。 

- [Azure SQL Managed Instance (プレビュー) でのトランザクション レプリケーション](replication-transactional-overview.md)
