---
title: SQL Server Always On 可用性グループの概要
description: この記事では、Azure Virtual Machines での SQL Server Always On 可用性グループについて説明します。
services: virtual-machines
documentationCenter: na
author: rajeshsetlem
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.custom: seo-lt-2019
ms.reviewer: mathoma
ms.openlocfilehash: 21aef2227768d49da9a5eab5f4e772441c9f15b0
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158672"
---
# <a name="always-on-availability-group-on-sql-server-on-azure-vms"></a>Azure VM 上の SQL Server の Always On 可用性グループ
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

この記事では、Azure 仮想マシン (VM) 上の SQL Server の Always On 可用性グループ (AG) について説明します。 

## <a name="overview"></a>概要

Azure 仮想マシン上の Always On 可用性グループは、[オンプレミスの Always On 可用性グループ](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)と似ており、基盤となる [Windows Server フェールオーバー クラスター](hadr-windows-server-failover-cluster-overview.md)に依存しています。 ただし、仮想マシンは Azure でホストされているため、VM の冗長性や Azure ネットワーク上でのトラフィックのルーティングなど、追加の考慮事項もいくつかあります。 

次の図は、Azure VM 上の SQL Server の可用性グループを示しています。

![可用性グループ](./media/availability-group-overview/00-EndstateSampleNoELB.png)

> [!NOTE]
> これで Azure Migrate を使用して、可用性グループ ソリューションを Azure VM 上の SQL Server にリフト アンド シフトできるようになりました。 詳細については、[可用性グループの移行](../../migration-guides/virtual-machines/sql-server-availability-group-to-sql-on-azure-vm.md)に関するページを参照してください。 

## <a name="vm-redundancy"></a>VM の冗長性 

冗長性と高可用性を向上させるには、SQL Server VM を同じ[可用性セット](../../../virtual-machines/availability-set-overview.md)に配置するか、異なる[可用性ゾーン](../../../availability-zones/az-overview.md)に配置する必要があります。

一連の VM を同じ可用性セットに配置すると、機器の障害が原因で発生するデータ センター内での停止から保護され (可用性セット内の VM はリソースを共有しません)、更新からも保護されます (可用性セット内の VM は同時に更新されることはありません)。 

可用性ゾーンによって、データ センター全体の障害から保護されます。各ゾーンは、リージョン内の一連のデータ センターを表します。  リソースがさまざまな可用性ゾーンに配置されるようにすることで、データ センターレベルの停止によってすべての VM がオフラインになることはなくなります。

Azure VM を作成するときは、可用性セットと可用性ゾーンのどちらを構成するかを選択する必要があります。  Azure VM が両方に参加することはできません。 

可用性ゾーンは、可用性セットよりも可用性が優れている場合がありますが (99.99% 対 99.95%)、パフォーマンスも考慮する必要があります。 可用性セット内の VM は、[近接配置グループ](../../../virtual-machines/co-location.md)に配置することができます。これにより、相互に近接していることが保証され、VM 間のネットワーク待機時間が最小限に抑えられます。 異なる可用性ゾーンに配置されている VM の場合、それらの間のネットワーク待機時間が大きくなり、プライマリ レプリカとセカンダリ レプリカ間のデータ同期にかかる時間が長くなる可能性があります。 これにより、プライマリ レプリカに待機時間が発生する可能性があるだけでなく、予定外のフェールオーバーが発生した場合にデータが失われる可能性が高くなります。 提案されたソリューションに負荷をかけてテストし、パフォーマンスと可用性の両面で SLA を満たすことの確認が重要です。

## <a name="connectivity"></a>接続

可用性グループ リスナーに接続するためのオンプレミス エクスペリエンスに一致するよう、SQL Server VM を同じ仮想ネットワーク内の[複数のサブネット](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md)にデプロイします。 複数のサブネットを使用すると、トラフィックをリスナーにルーティングするための分散ネットワーク名 (DNN) や Azure Load Balancer への余分な依存関係が不要になります。 

SQL Server VM を 1 つのサブネットにデプロイする場合、仮想ネットワーク名 (VNN) と Azure Load Balancer を構成するか、または分散ネットワーク名 (DNN) を構成して可用性グループ リスナーにトラフィックをルーティングすることができます。 [この 2 つの違いを確認](hadr-windows-server-failover-cluster-overview.md)してから、可用性グループに対して[分散ネットワーク名 (DNN)](availability-group-distributed-network-name-dnn-listener-configure.md) または[仮想ネットワーク名 (VNN)](availability-group-vnn-azure-load-balancer-configure.md) をデプロイします。 

DNN を使用すると、ほとんどの SQL Server 機能は可用性グループに対して透過的に機能しますが、特定の機能については、特別な考慮が必要となる場合があります。 詳細については、[AG と DNN の相互運用性](availability-group-dnn-interoperability.md)に関する記事を参照してください。 

また、VNN リスナーと DNN リスナーの機能には、注意が必要な動作の違いがいつくかあります。 

- **フェールオーバー時間**: DNN リスナーを使用すると、ネットワーク ロード バランサーによって障害イベントが検出され、ルーティングが変更されるまで待つ必要がないため、フェールオーバー時間が短縮されます。 
- **既存の接続**: フェールオーバー可用性グループ内の *特定のデータベース* に対する接続は閉じられますが、フェールオーバー処理中も DNN によってオンライン状態が維持されるため、プライマリ レプリカへの他の接続は開いたままになります。 これは、可用性グループがフェールオーバーすると通常はプライマリ レプリカへのすべての接続が閉じられ、リスナーはオフラインになり、プライマリ レプリカはセカンダリの役割に移行するという従来の VNN 環境とは異なります。 DNN リスナーを使用する場合は、フェールオーバー時に接続が新しいプライマリ レプリカにリダイレクトされるように、必要に応じてアプリケーションの接続文字列を調整します。
- **開いているトランザクション**: フェールオーバー可用性グループ内のデータベースに対して開かれているトランザクションは、閉じられ、ロールバックされるので、*手動* で再接続する必要があります。 たとえば、SQL Server Management Studio で、クエリ ウィンドウを閉じて、新しいウィンドウを開きます。 

Azure で VNN リスナーを設定するには、ロード バランサーが必要です。 Azure のロード バランサーには、主に外部 (パブリック) と内部の 2 つのオプションがあります。 外部 (パブリック) ロード バランサーは、インターネットに面しており、インターネット経由でアクセスできるパブリック仮想 IP に関連付けられています。 内部ロード バランサーは、同じ仮想ネットワーク内のクライアントのみをサポートします。 どちらのタイプのロード バランサーの場合でも、[Direct Server Return](../../../load-balancer/load-balancer-multivip-overview.md#rule-type-2-backend-port-reuse-by-using-floating-ip) を有効にする必要があります。 

この場合でも、サービス インスタンスに直接接続することで、各可用性レプリカに個別に接続できます。 また、可用性グループはデータベース ミラーリング クライアントとの下位互換性があるため、レプリカがデータベース ミラーリングと同様に、次のように構成されていれば、データベース ミラーリング パートナーのように可用性レプリカに接続できます。

* 1 つのプライマリ レプリカと 1 つのセカンダリ レプリカがある。
* セカンダリ レプリカが読み取り不可として構成されている ( **[読み取り可能なセカンダリ]** オプションが **[いいえ]** に設定されている)。

ADO.NET または SQL Server Native Client を使用する、このデータベース ミラーリングと同様の構成に対応するクライアント接続文字列の例を以下に示します。

```console
Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;
```

クライアント接続の詳細については、以下を参照してください。

* [SQL Server Native Client での接続文字列キーワードの使用](/sql/relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client)
* [データベース ミラーリング セッションへのクライアントの接続 (SQL Server)](/sql/database-engine/database-mirroring/connect-clients-to-a-database-mirroring-session-sql-server)
* [ハイブリッド IT での可用性グループ リスナーへの接続](/archive/blogs/sqlalwayson/connecting-to-availability-group-listener-in-hybrid-it)
* [可用性グループ リスナー、クライアント接続、およびアプリケーションのフェールオーバー (SQL Server)](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover)
* [可用性グループでのデータベース ミラーリング接続文字列の使用](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover)

## <a name="lease-mechanism"></a>リースのメカニズム 

SQL Server の場合、AG リソース DLL は、AG リース メカニズムと Always On 正常性検出に基づいて、AG の正常性を判断します。 AG リソース DLL により、*IsAlive* 操作を介してリソースの正常性が公開されています。 リソース モニターにより、クラスターのハートビート間隔 (クラスター全体の **CrossSubnetDelay** 値と **SameSubnetDelay** 値で設定されます) で IsAlive がポーリングされます。 プライマリ ノード上では、リソース DLL に対する IsAlive 呼び出しから AG が正常でないことが返されるたびに、クラスター サービスによってフェールオーバーが開始されます。

AG リソース DLL により、内部の SQL Server コンポーネントの状態が監視されます。 sp_server_diagnostics により、**HealthCheckTimeout** で制御される間隔で、これらのコンポーネントの正常性が SQL Server に報告されます。

他のフェールオーバーのメカニズムとは異なり、SQL Server インスタンスはリースのメカニズムで積極的な役割を果たします。 リース メカニズムは、クラスター リソースのホストと SQL Server プロセス間の *LooksAlive* 検証として使用されます。 このメカニズムは、両者 (クラスター サービスと SQL Server サービス) が頻繁に接触していることを確認する目的で使用されます。互いの状態を確認し、最終的にはスプリットブレインを防止します。

Azure VM で AG を構成する場合、これらのしきい値をオンプレミス環境での構成とは異なる方法で構成する必要があることがよくあります。 Azure VM のベスト プラクティスに従ってしきい値設定を構成するには、[クラスターのベスト プラクティス](hadr-cluster-best-practices.md)に関する記事を参照してください。 


## <a name="network-configuration"></a>ネットワークの構成  

Azure Load Balancer または分散ネットワーク名 (DNN) に依存しなくても可用性グループ リスナーにトラフィックをルーティングできるよう、可能な限り SQL Server VM を複数のサブネットにデプロイします。 

Azure VM フェールオーバー クラスターでは、サーバー (クラスター ノード) ごとに 1 つの NIC を推奨しています。 Azure ネットワークは物理的な冗長性を備えているので、Azure VM フェールオーバー クラスターに NIC を追加する必要はありません。 クラスター検証レポートで、1 つのネットワークでしかノードに到達できないという警告が出されますが、Azure VM フェールオーバー クラスターではこの警告は無視して構いません。

## <a name="basic-availability-group"></a>基本的な可用性グループ

基本的な可用性グループの場合、複数のセカンダリ レプリカを使用できず、セカンダリ レプリカへの読み取りアクセス権もないので、基本的な可用性グループにデータベース ミラーリング接続文字列を使用することができます。 接続文字列を使用すると、リスナーが不要になります。 リスナーへの依存をなくすと、Azure VM 上の可用性グループに役立ちます。これは、ロード バランサーが不要になったり、追加のデータベースのために複数のリスナーを用意するときにロード バランサーに追加の IP を追加する必要がなくなるためです。 

たとえば、基本的な AG (つまり 1 つのセカンダリ レプリカのみを持ち、セカンダリ レプリカで読み取りアクセスが許可されていない AG) の Replica_A または Replica_B 上の AG データベース AdventureWorks に TCP/IP を使用して明示的に接続する場合、クライアント アプリケーションから次のデータベース ミラーリング接続文字列を指定して、AG への接続を成功させることができます

`Server=Replica_A; Failover_Partner=Replica_B; Database=AdventureWorks; Network=dbmssocn`


## <a name="deployment-options"></a>デプロイ オプション

Azure VM 上の SQL Server に可用性グループをデプロイするためのオプションは複数あり、その一部は他のものよりも自動化されています。 

次の表は、使用可能なオプションの比較を示しています。

| | [Azure portal](availability-group-azure-portal-configure.md)、 | [Azure CLI / PowerShell](./availability-group-az-commandline-configure.md) | [クイック スタート テンプレート](availability-group-quickstart-template-configure.md) | [手動 (1 つのサブネット)](availability-group-manually-configure-prerequisites-tutorial-single-subnet.md) | [手動 (複数のサブネット)](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md)
|---------|---------|---------|---------|---------|
|**SQL Server のバージョン** |2016 以降 |2016 以降|2016 以降|2012 以降|2012 以降| 
|**SQL Server のエディション** |Enterprise |Enterprise |Enterprise |Enterprise、Standard|Enterprise、Standard|
|**Windows Server のバージョン**| 2016 以降 | 2016 以降 | 2016 以降 | All| All|
|**クラスターを自動作成**|はい|はい | はい |いいえ| いいえ| 
|**可用性グループを自動作成** |はい |いいえ|いいえ|いいえ| いいえ| 
|**リスナーとロード バランサーを別々に作成** |いいえ|いいえ|いいえ|はい|なし|
|**この方法を使用して DNN リスナーを作成可能**|いいえ|いいえ|いいえ|はい|なし|
|**WSFC クォーラムの構成**|クラウド監視|クラウド監視|クラウド監視|All|All|
|**複数のリージョンを使用した DR** |いいえ|いいえ|いいえ|はい|はい|
|**マルチサブネットのサポート** |いいえ|いいえ|いいえ|該当なし|はい|
|**既存の AD のサポート**|はい|はい|はい|はい|はい|
|**同一リージョン内の複数のゾーンを使用した DR**|はい|はい|はい|はい|はい|
|**AD なしの分散型 AG**|いいえ|いいえ|いいえ|はい| はい| 
|**クラスターなしの分散型 AG** |いいえ|いいえ|いいえ|はい|はい|
|**ロード バランサーまたは DNN が必要**| はい | はい | はい | はい | いいえ|

## <a name="next-steps"></a>次の手順

[HADR のベスト プラクティス](hadr-cluster-best-practices.md)を確認し、[Azure portal](availability-group-azure-portal-configure.md)、[Azure CLI または PowerShell](./availability-group-az-commandline-configure.md)、[クイックスタート テンプレート](availability-group-quickstart-template-configure.md)を使用するか、または[手動](availability-group-manually-configure-prerequisites-tutorial-single-subnet.md)で可用性グループのデプロイを開始します。

また、[クラスターレス可用性グループ](availability-group-clusterless-workgroup-configure.md)または可用性グループを[複数のリージョン](availability-group-manually-configure-multiple-regions.md)にデプロイすることもできます。

詳細については、以下をご覧ください。

- [Windows Server フェールオーバー クラスターと Azure VM 上の SQL Server](hadr-windows-server-failover-cluster-overview.md)
- [AlwaysOn 可用性グループの概要](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
