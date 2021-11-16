---
title: HADR 構成のベスト プラクティス
description: Azure Virtual Machines 上の SQL Server に対して高可用性とディザスター リカバリー (HADR) を構成する場合に、サポートされるクラスター構成について説明します (サポートされるクォーラムまたは接続ルーティングのオプションなど)。
services: virtual-machines
documentationCenter: na
author: rajeshsetlem
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: 8be0dc33d580314fd6b5e6472ed1cf41c5be2d4e
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158435"
---
# <a name="hadr-configuration-best-practices-sql-server-on-azure-vms"></a>HADR 構成のベスト プラクティス (Azure VM 上の SQL Server)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure Virtual Machines (VM) 上の SQL Server を使用して高可用性とディザスター リカバリー (HADR) を実現するには、[Windows Server フェールオーバー クラスター](hadr-windows-server-failover-cluster-overview.md)が使用されます。 

この記事では、[フェールオーバー クラスター インスタンス (FCI)](failover-cluster-instance-overview.md) と[可用性グループ](availability-group-overview.md)の両方について、それらを Azure VM 上の SQL Server と共に使用する場合のクラスター構成のベスト プラクティスについて説明します。 

詳しくは、このシリーズの他の記事 ([チェックリスト](performance-guidelines-best-practices-checklist.md)、[VM のサイズ](performance-guidelines-best-practices-vm-size.md)、[ストレージ](performance-guidelines-best-practices-storage.md)、[セキュリティ](security-considerations-best-practices.md)、[HADR の構成](hadr-cluster-best-practices.md)、[ベースラインの収集](performance-guidelines-best-practices-collect-baseline.md)) をご覧ください。 

## <a name="checklist"></a>チェック リスト

次のチェックリストを参照して、この記事の残りの部分で詳しく説明されている HADR のベスト プラクティスの概要を確認してください。 

Windows クラスターの場合は、次のベスト プラクティスについて検討します。 

* Azure Load Balancer または分散ネットワーク名 (DNN) に依存しなくても HADR ソリューションにトラフィックをルーティングできるよう、可能な限り SQL Server VM を複数のサブネットにデプロイします。 
* 一時的なネットワーク障害や Azure プラットフォーム メンテナンスによって予期しない停止が起こらないよう、クラスターを変更してパラメーターを緩和します。 詳細については、[ハートビートとしきい値の設定](#heartbeat-and-threshold)に関する記事を参照してください。 Windows Server 2012 以降の場合は、次の推奨値を使用します。 
   - **SameSubnetDelay**: 1 秒
   - **SameSubnetThreshold**: 40 ハートビート
   - **CrossSubnetDelay**: 1 秒
   - **CrossSubnetThreshold**: 40 ハートビート
* VM は可用性セットまたは別の可用性ゾーンに配置します。  詳細については、「[VM の可用性の設定](#vm-availability-settings)」を参照してください。 
* クラスター ノードごとに 1 つの NIC を使用します。 
* 3 つ以上の奇数の投票を使用するように、クラスターの[クォーラム投票](#quorum-voting)を構成します。 投票は DR リージョンに割り当てないでください。 
* リソースの制約による予期しない再起動やフェールオーバーが発生しないように、[リソース制限](#resource-limits)を慎重に監視します。
   - OS、ドライバー、SQL Server が最新のビルドになっていることを確認します。 
   - Azure VM 上での SQL Server のパフォーマンスを最適化します。 詳細については、この記事の他のセクションを参照してください。 
   - リソース制限に達しないように、ワークロードを削減または分散します。 
   - 制約を回避するために、より制限の高い VM またはディスクに移行します。 

SQL Server の可用性グループまたはフェールオーバー クラスター インスタンスの場合は、こちらのベスト プラクティスを検討してください。 

* 予期しないエラーが頻繁に発生する場合は、この記事の残りの部分で説明されているパフォーマンスのベスト プラクティスに従ってください。 
* SQL Server VM のパフォーマンスを最適化しても予期しないフェールオーバーが解決されない場合は、可用性グループまたフェールオーバー クラスター インスタンスの[監視を緩和](#relaxed-monitoring)することを検討してください。 ただし、そうすることで問題の根底にある原因に対処できない場合があり、障害の可能性を減らすことで症状が表に現れない可能性があります。 その場合でも、根底にある根本原因を調査して対処しなければならない場合があります。 Windows Server 2012 以降の場合は、次の推奨値を使用します。 
   - **リース タイムアウト**: こちらの式を使用して、リース タイムアウトの最大値を計算します。   
   `Lease timeout < (2 * SameSubnetThreshold * SameSubnetDelay)`.   
   40 秒から始めます。 先ほど推奨した緩和されている `SameSubnetThreshold` と `SameSubnetDelay` の値を使用している場合は、リース タイムアウト値が 80 秒を超えないようにしてください。   
   - **指定した期間の最大エラー数**: この値は 6 に設定します。 
* 仮想ネットワーク名 (VNN) と Azure Load Balancer を使用して HADR ソリューションに接続する場合は、お使いのクラスターが 1 つのサブネットにしかまたがっていない場合でも、接続文字列に `MultiSubnetFailover = true` を指定します。 
   - クライアントで `MultiSubnetFailover = True` がサポートされていない場合は、`RegisterAllProvidersIP = 0` および `HostRecordTTL = 300` を設定して、クライアント資格情報をより短期間だけキャッシュすることが必要になる可能性があります。 ただし、そうすることで、DNS サーバーに対して追加のクエリが発生する場合があります。 
- 分散ネットワーク名 (DNN) を使用して HADR ソリューションに接続する場合は、以下を検討してください。
   - `MultiSubnetFailover = True` をサポートするクライアント ドライバーを使用する必要があります。このパラメーターは接続文字列に含める必要があります。 
   - 可用性グループの DNN リスナーに接続するときに、接続文字列内の一意の DNN ポートを使用します。 
- 基本の可用性グループのデータベース ミラーリング接続文字列を使用して、ロード バランサーまたは DNN の必要性をなくします。 
- 高可用性ソリューションをデプロイする前に VHD のセクター サイズを検証して、I/O の不整合を回避します。 詳細については、[KB3009974](https://support.microsoft.com/topic/kb3009974-fix-slow-synchronization-when-disks-have-different-sector-sizes-for-primary-and-secondary-replica-log-files-in-sql-server-ag-and-logshipping-environments-ed181bf3-ce80-b6d0-f268-34135711043c) を参照してください。 


## <a name="vm-availability-settings"></a>VM 可用性設定

ダウンタイムの影響を軽減するために、次に示す VM の最適な可用性設定を検討してください。 

* 近接配置グループを高速ネットワークと共に使用して、最短の待ち時間を実現します。
* 仮想マシンのクラスター ノードの配置場所を、別々の可用性ゾーン内にしてデータセンターレベルの障害から保護するか、1 つの可用性セット内にして同じデータセンター内で低遅延冗長性を確保します。
* 可用性セット内の VM では、Premium マネージド OS およびデータ ディスクを使用します。
* 各アプリケーション層に対して別々の可用性セットを構成します。

## <a name="quorum"></a>Quorum

2 ノード クラスターは[クォーラム リソース](/windows-server/storage/storage-spaces/understand-quorum)なしでも機能しますが、実稼働サポートを受けるにはクォーラム リソースを使用することが必須です。 クラスターの検証で、クォーラム リソースを使用していないクラスターが合格することはありません。 

技術的には、3 ノード クラスターの場合、クォーラム リソースなしでも 1 つのノードの損失 (2 ノードにダウンします) を乗り切ることができます。 しかし、クラスターが 2 ノードまで下がると、ノードが失われたり通信エラーが発生したりした場合、スプリットブレイン シナリオを防ぐために、クラスター化されたリソースがオフラインになるリスクがあります。 クォーラム リソースを構成することで、1 つのノードのみをオンラインにしてクラスターのオンラインを続行できます。

ディスク監視は最も回復性の高いクォーラム オプションですが、Azure VM 上の SQL Server でディスク監視を使用するには、高可用性ソリューションにいくつかの制限を課す Azure 共有ディスクを使用する必要があります。 そのため、Azure 共有ディスクを使用してフェールオーバー クラスター インスタンスを構成する場合は、ディスク監視を使用します。それ以外の場合は、可能な限りクラウド監視を使用します。 

次の表は、Azure VM 上の SQL Server で使用できるクォーラム オプションの一覧です。 

|  |[クラウド監視](/windows-server/failover-clustering/deploy-cloud-witness) |[ディスク監視](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum) |[ファイル共有監視](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**サポートされる OS**| Windows Server 2016 以降 |All | すべて|

- **クラウド監視** は、複数のサイト、複数のゾーン、複数のリージョンでのデプロイに最適です。 共有ストレージ クラスター ソリューションを使用している場合を除き、可能な限りクラウド監視を使用してください。
- **ディスク監視** は、最も回復性に優れたクォーラム オプションであり、Azure 共有ディスク (または共有 SCSI、iSCSI、ファイバー チャネル SAN などの共有ディスク ソリューション) を使用するすべてのクラスターに最適です。  クラスター共有ボリュームをディスク監視として使用することはできません。 
- **ファイル共有監視** は、ディスク監視とクラウド監視がオプションとして使用できない場合に適しています。 

概要については、[クラスター クォーラムの構成](hadr-cluster-quorum-configure-how-to.md)に関するページを参照してください。 

## <a name="quorum-voting"></a>クォーラム投票

Windows Server フェールオーバー クラスターに参加しているノードのクォーラム投票を変更することができます。 

ノードの投票設定を変更するときは、これらのガイドラインに従ってください。 

| クォーラム投票のガイドライン |
|-|
| 既定では最初は各ノードで投票は行いません。 各ノードでは正当性が明白である場合にのみ投票を行います。|
| 可用性グループのプライマリ レプリカをホストするクラスター ノード、またはフェールオーバー クラスター インスタンスの優先所有者の投票を有効にします。 |
| 自動フェールオーバー所有者の投票を有効にします。 自動フェールオーバーの結果として、プライマリ レプリカまたは FCI をホストする可能性がある各ノードでは投票を行う必要があります。 | 
| 可用性グループに複数のセカンダリ レプリカがある場合は、自動フェールオーバーが設定されているレプリカの投票のみを有効にします。 | 
| セカンダリ ディザスター リカバリー サイトにあるノードの投票を無効にします。 プライマリ サイトに問題がない場合は、セカンダリ サイト内のノードがクラスターをオフラインにするかどうかの判断に影響すべきではありません。 | 
| 投票数を奇数にし、クォーラム投票が 3 つ以上になるようにします。 2 ノード クラスターで必要に応じて、追加の投票のために[クォーラム監視](hadr-cluster-quorum-configure-how-to.md)を追加します。 | 
| フェールオーバー後の投票割り当てを再評価します。 正常なクォーラムをサポートしていないクラスター構成にフェールオーバーすることは避けてください。 |


## <a name="connectivity"></a>接続性

可用性グループ リスナーまたはフェールオーバー クラスター インスタンスに接続するためのオンプレミス エクスペリエンスに一致するよう、SQL Server VM を同じ仮想ネットワーク内の複数のサブネットにデプロイします。 複数のサブネットを使用すると、トラフィックをリスナーにルーティングするための分散ネットワーク名や Azure Load Balancer への余分な依存関係が不要になります。  

HADR ソリューションを簡素化するには、可能な限り、SQL Server VM を複数のサブネットにデプロイします。  詳細については、[複数サブネットの AG](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md) および[複数サブネットの FCI](failover-cluster-instance-prepare-vm.md#subnets) に関するページを参照してください。 

SQL Server VM が 1 つのサブネット内にある場合は、フェールオーバー クラスター インスタンスと可用性グループ リスナーの両方に対して、仮想ネットワーク名 (VNN) と Azure Load Balancer、または分散ネットワーク名 (DNN) のいずれかを構成できます。 

推奨されている接続オプションは、分散ネットワーク名です (使用可能な場合)。 
- ロード バランサーのリソースを維持する必要がなくなるため、エンド ツー エンド ソリューションはより堅牢になります。 
- ロード バランサーのプローブを排除すると、フェールオーバー時間を最小限に抑えられます。 
- DNN を使用すると、Azure VM 上の SQL Server を使用するフェールオーバー クラスター インスタンスまたは可用性グループ リスナーのプロビジョニングと管理が簡単になります。 

次の制限が適用されます。 
- クライアント ドライバーで `MultiSubnetFailover=True` パラメーターがサポートされている必要があります。 
- DNN 機能は、Windows Server 2016 以降の [SQL Server 2016 SP3](https://support.microsoft.com/topic/kb5003279-sql-server-2016-service-pack-3-release-information-46ab9543-5cf9-464d-bd63-796279591c31)、[SQL Server 2017 CU25](https://support.microsoft.com/topic/kb5003830-cumulative-update-25-for-sql-server-2017-357b80dc-43b5-447c-b544-7503eee189e9)、[SQL Server 2019 CU8](https://support.microsoft.com/topic/cumulative-update-8-for-sql-server-2019-ed7f79d9-a3f0-a5c2-0bef-d0b7961d2d72) 以降で使用できます。

詳細については、[Windows Server フェールオーバー クラスターの概要](hadr-windows-server-failover-cluster-overview.md#virtual-network-name-vnn)に関するページを参照してください。 

接続を構成するには、次の記事を参照してください。
- 可用性グループ: [DNN を構成する](availability-group-distributed-network-name-dnn-listener-configure.md)、[VNN を構成する](availability-group-vnn-azure-load-balancer-configure.md)
- フェールオーバー クラスター インスタンス: [DNN を構成する](failover-cluster-instance-distributed-network-name-dnn-configure.md)、[VNN を構成する](failover-cluster-instance-vnn-azure-load-balancer-configure.md) 

DNN を使用すると、ほとんどの SQL Server 機能は FCI と可用性グループに対して透過的に機能しますが、特定の機能については、特別な考慮が必要となる場合があります。 詳細については、[FCI と DNN の相互運用性](failover-cluster-instance-dnn-interoperability.md)に関するページと、[AG と DNN の相互運用性](availability-group-dnn-interoperability.md)に関するページを参照してください。 

>[!TIP]
> 1 つのサブネットの HADR ソリューションでも、接続文字列内で MultiSubnetFailover パラメーターを true に設定することで、今後、接続文字列を更新しなくても、複数のサブネットにまたがることができます。  

## <a name="heartbeat-and-threshold"></a>ハートビートとしきい値 

クラスターのハートビートとしきい値の設定を、緩和した設定に変更します。 既定のハートビートとしきい値のクラスター設定は、高度にチューニングされたオンプレミス ネットワーク用に設計され、クラウド環境で待機時間が長くなる可能性は考慮されていません。 ハートビート ネットワークは UDP 3343 で維持されています。これは従来、TCP よりはるかに信頼性が低く、不完全な会話が発生しやすくなっています。
 
そのため、Azure VM 高可用性ソリューションで SQL Server のクラスター ノードを実行する場合は、より緩和した監視状態にクラスター設定を変更して、ネットワークの待機時間や障害の可能性の増加、Azure メンテナンス、またはリソースのボトルネックの発生による一時的な障害を回避します。 

遅延としきい値の設定は、全体的な正常性検出に累積的な影響を与えます。 たとえば、復旧を行う前に、2 秒ごとにハートビートを送信するように *CrossSubnetDelay* を設定し、*CrossSubnetThreshold* を 10 回のハートビート失敗に設定していると、復旧アクションが実行されるまでにクラスターで可能なネットワーク許容値の合計は 20 秒になります。 一般に、ハートビートを頻繁に送信し続ける一方で、しきい値を大きくすることが推奨されています。 

一時的な問題に対する許容性を高めながら、正当な停止中に復旧を確実に行うため、遅延としきい値の設定を、次の表に詳しく示す推奨値に緩和してください。 

| 設定 | Windows Server 2012 またはそれ以降 | Windows Server 2008R2 |
|:---------------------|:----------------------------|:-----------------------|
| SameSubnetDelay      | 1 秒                    | 2 秒               |
| SameSubnetThreshold  | 40 ハートビート               | 10 ハートビート (最大)         |
| CrossSubnetDelay     | 1 秒                    | 2 秒               |  
| CrossSubnetThreshold | 40 ハートビート               | 20 ハートビート (最大)         |


PowerShell を使用してクラスター パラメーターを変更します。 

# <a name="windows-server-2012-2019"></a>[Windows Server 2012 から 2019](#tab/windows2012)


```powershell
(get-cluster).SameSubnetThreshold = 40
(get-cluster).CrossSubnetThreshold = 40
```

# <a name="windows-server-2008r2"></a>[Windows Server 2008/R2](#tab/windows2008)


```powershell
(get-cluster).SameSubnetThreshold = 10
(get-cluster).CrossSubnetThreshold = 20 
(get-cluster).SameSubnetDelay = 2000
(get-cluster).CrossSubnetDelay = 2000
```

---

PowerShell を使用して変更を確認します。 

```powershell
get-cluster | fl *subnet*
```

以下、具体例に沿って説明します。 

* この変更は即時に行われ、クラスターやリソースを再起動する必要はありません。 
* 同じサブネットの値をクロス サブネットの値より大きくしてはなりません。 
* SameSubnetThreshold <= CrossSubnetThreshold
* SameSubnetDelay <= CrossSubnetDelay

お使いのアプリケーション、ビジネス ニーズ、環境に応じて、許容されるダウンタイムの長さおよび是正措置が実行されるまでの期間に基づいて、緩和した値を選択します。 既定の Windows Server 2019 の値を超えることができない場合は、可能な限り、それらと一致させることだけでも試みてください。 

参照用として、次の表に既定値の詳細を示します。 


| 設定 | Windows Server 2019 |  Windows Server 2016 |    Windows Server 2008 - 2012 R2 |
|:---------------------|:----------------|   ------------|:----------------------------|
| SameSubnetDelay      | 1 秒        | 1 秒       | 1 秒                    |
| SameSubnetThreshold  | 20 ハートビート   | 10 ハートビート  | 5 ハートビート               |
| CrossSubnetDelay     | 1 秒        | 1 秒     | 1 秒                    |
| CrossSubnetThreshold | 20 ハートビート   | 10 ハートビート   | 5 ハートビート               |


詳細については、[フェールオーバー クラスター ネットワークのしきい値の調整](/windows-server/troubleshoot/iaas-sql-failover-cluster)に関するページを参照してください。

## <a name="relaxed-monitoring"></a>緩和された監視

推奨どおりにクラスターのハートビートとしきい値の設定をチューニングしても許容度が不十分であり、実際の障害ではなく一時的な問題による障害が引き続き発生する場合は、AG または FCI の監視をより緩く構成できます。 一部のシナリオでは、アクティビティのレベルを考慮して、一定の期間、監視を一時的に緩和することが有益な場合があります。 たとえば、データベースのバックアップ、インデックスのメンテナンス、DBCC CHECKDB などの IO 集中型のワークロードを実行している場合は、監視を緩和することができます。アクティビティが完了したら、より厳しい値に監視を設定します。 

> [!WARNING]
> これらの設定を変更すると根本的な問題が隠されてしまう可能性があるため、障害の可能性を低減 (排除ではなく) するための一時的な解決策として利用してください。 根本的な問題は引き続き調査して対処する必要があります。 

まず、監視を緩和するように次のパラメーターを既定値から増やし、必要に応じて調整します。 


|パラメーター |既定値  |緩和された値  |説明  |
|---------|---------|---------|---------|
|**正常性チェック タイムアウト**|30000 |60000 |プライマリ レプリカまたはノードの正常性を特定します。 クラスター リソース DLL sp_server_diagnostics は、正常性チェックのタイムアウトしきい値の 3 分の 1 の間隔で結果を返します。 sp_server_diagnostics が低速であるか、情報を返さない場合、リソース DLL は正常性チェックのタイムアウトしきい値の間隔が完全に経過するのを待ってから、リソースが無応答であると判断し、自動フェールオーバーを開始します (そのように構成されている場合)。 |
|**エラー条件レベル** |  3  |   2  |自動フェールオーバーをトリガーする条件。 エラー条件レベルの範囲は、最も制限が緩いものから (レベル 1)、最も制限の厳しい指定 (レベル 5) まで 5 つあります  |

Transact-SQL (T-SQL) を使用して、AG と FCI の両方の正常性チェックと失敗の条件を変更します。 

可用性グループの場合: 

```sql
ALTER AVAILABILITY GROUP AG1 SET (HEALTH_CHECK_TIMEOUT =60000);
ALTER AVAILABILITY GROUP AG1 SET (FAILURE_CONDITION_LEVEL = 2);
```

フェールオーバー クラスター インスタンスの場合: 

```sql
ALTER SERVER CONFIGURATION SET FAILOVER CLUSTER PROPERTY HealthCheckTimeout = 60000;
ALTER SERVER CONFIGURATION SET FAILOVER CLUSTER PROPERTY FailureConditionLevel = 2; 
```

**可用性グループ** の場合のみ、次の推奨パラメーターから始めて、必要に応じて調整します。 

|パラメーター |既定値  |緩和された値  |説明  |
|---------|---------|---------|---------|
|**リースのタイムアウト**|20000|40000|スプリットブレインを防止します。 |
|**セッションのタイムアウト**|10000 |20000|レプリカ間の通信の問題を確認します。 セッションタイムアウト期間は、接続されたレプリカからの ping 応答を可用性レプリカが待機する期間を制御するレプリカ プロパティです。この期間を過ぎると、接続に失敗したと見なされます。 既定では、レプリカは ping 応答を 10 秒間待機します。 このレプリカ プロパティは、可用性グループ内の指定したセカンダリ レプリカとプライマリ レプリカ間の接続のみに適用されます。 |
| **指定した期間の最大エラー数** | 2 | 6 |複数のノード障害の中で、クラスター化されたリソースが無期限に移動されるのを避けるために使用されます。 値が小さすぎると、可用性グループが失敗した状態になるおそれがあります。 値を大きくすることで、パフォーマンスの問題による短時間の中断を防いでください。値が小さすぎると AG が失敗した状態になるおそれがあるためです。 | 

変更を行う前に、次の点を考慮してください。 
- タイムアウト値は既定値を下回るほど低くしないでください。 
- こちらの式を使用して、リース タイムアウトの最大値を計算します。   
 `Lease timeout < (2 * SameSubnetThreshold * SameSubnetDelay)`.   
  40 秒から始めます。 先ほど推奨した緩和されている `SameSubnetThreshold` と `SameSubnetDelay` の値を使用している場合は、リース タイムアウト値が 80 秒を超えないようにしてください。    
- 同期コミット レプリカの場合、セッション タイムアウトを大きな値に変更すると、HADR_sync_commit 待機が増える場合があります。

**リースのタイムアウト** 

**フェールオーバー クラスター マネージャー** を使用して、可用性グループの **リース タイムアウト** 設定を変更します。 詳細な手順については、SQL Server [可用性グループのリース正常性チェック](/sql/database-engine/availability-groups/windows/availability-group-lease-healthcheck-timeout#lease-timeout)に関するドキュメントを参照してください。

**セッションのタイムアウト** 

Transact-SQL (T-SQL) を使用して、可用性グループの **セッション タイムアウト** を変更します。 

```sql
ALTER AVAILABILITY GROUP AG1
MODIFY REPLICA ON 'INSTANCE01' WITH (SESSION_TIMEOUT = 15);
```

**指定した期間の最大エラー数**

フェールオーバー クラスター マネージャーを使用して、 **[指定した期間の最大エラー数]** の値を変更します。 
1. ナビゲーション ウィンドウの **[役割]** を選択します。
1. **[役割]** で、クラスター化されたリソースを右クリックし、 **[プロパティ]** を選択します。 
1. **[フェールオーバー]** タブを選択し、必要に応じて **[指定した期間の最大エラー数]** の値を増やします。 

## <a name="resource-limits"></a>リソースの制限

VM またはディスクの制限により、クラスターの正常性に影響を与え、正常性チェックを妨げるリソースのボトルネックが発生するおそれがあります。 リソースの制限に関する問題が発生している場合は、以下を検討してください。 

* OS、ドライバー、SQL Server が最新のビルドになっていることを確認します。
* Azure Virtual Machines における SQL Server の[パフォーマンス ガイドライン](performance-guidelines-best-practices-checklist.md)の説明に従って、Azure VM 環境の SQL Server を最適化してください
* ワークロードを削減または分散して、リソースの制限を超えることなく使用率を削減してください
* 次のような機会があれば、SQL Server のワークロードを調整します。
    * インデックスを追加/最適化する
    * 必要に応じて、可能であればフル スキャンで統計を更新する  
    * Resource Governor (SQL Server 2014 enterprise 以降のみ) などの機能を使用して、バックアップやインデックスのメンテナンスなど、特定のワークロード時のリソース使用率を制限する。 
* より制限の高い VM またはディスクに移動して、ワークロードの要求を満たす、または超えるようにします。 

## <a name="networking"></a>ネットワーク

Azure Load Balancer または分散ネットワーク名 (DNN) に依存しなくても HADR ソリューションにトラフィックをルーティングできるよう、可能な限り SQL Server VM を複数のサブネットにデプロイします。

サーバー (クラスター ノード) ごとに 1 つの NIC を使用します。 Azure ネットワークは物理的な冗長を備えているので、Azure 仮想マシンのゲスト クラスターに NIC を追加する必要はありません。 クラスター検証レポートには、ノードは 1 つのネットワーク上でのみ到達可能であることを警告するメッセージが表示されます。 Azure 仮想マシンのゲスト フェールオーバー クラスターでは、この警告を無視できます。 

特定の VM の帯域幅の制限は NIC 間で共有され、NIC を追加しても、Azure VM 上の SQL Server の可用性グループ パフォーマンスは向上しません。 そのため、2 つ目の NIC を追加する必要はありません。 

Azure の RFC に準拠していない DHCP サービスにより、特定のフェールオーバー クラスター構成の作成に失敗する可能性があります。 この失敗は、クラスター ネットワーク名に重複する IP アドレス (クラスター ノードの 1 つと同じ IP アドレスなど) が割り当てられていることが原因で発生します。 これは、Windows フェールオーバー クラスター機能に依存する、可用性グループを使用するときに問題になります。

2 ノード クラスターを作成し、オンラインにするシナリオを考えてみましょう。

1. クラスターがオンラインになると、NODE1 によって、クラスター ネットワーク名のために動的に割り当てられた IP アドレスが要求されます。
2. DHCP サービスでは要求が NODE1 自体からのものであることが認識されるため、DHCP サービスで NODE1 自体の IP アドレス以外の IP アドレスは提供されません。
3. NODE1 とフェールオーバー クラスターのネットワーク名の両方に重複するアドレスが割り当てられていることが Windows によって検出されると、既定のクラスター グループはオンラインになることができません。
4. 既定のクラスター グループは NODE2 に移動されます。 NODE2 によって、NODE1 の IP アドレスはクラスター IP アドレスとして処理され、既定のクラスター グループがオンラインになります。
5. NODE2 では、NODE1 との接続を確立しようとするときに、NODE1 の IP アドレスがそれ自体に解決されるため、NODE1 宛てのパケットは NODE2 から送信されません。 NODE2 では NODE1 との接続を確立できず、クォーラムが失われ、クラスターがシャットダウンされます。
6. NODE1 では NODE2 にパケットを送信できますが、NODE2 は応答できません。 NODE1 はクォーラムを失い、クラスターをシャットダウンします。

このシナリオは、クラスター ネットワーク名をオンラインにし、IP アドレスを [Azure Load Balancer](availability-group-load-balancer-portal-configure.md) に追加するために、クラスター ネットワーク名に未使用の静的 IP アドレスを割り当てることによって回避できます。

## <a name="known-issues"></a>既知の問題

一般的に知られている問題とエラーの解決策を確認します。 

**クラスター ノードがメンバーシップから削除された**


[Windows クラスターのハートビートとしきい値の設定](#heartbeat-and-threshold)が環境に対して厳格すぎる場合は、システム イベント ログに次のメッセージが頻繁に表示されることがあります。 

```
Error 1135
Cluster node 'Node1' was removed from the active failover cluster membership. 
The Cluster service on this node may have stopped. This could also be due to the node having 
lost communication with other active nodes in the failover cluster. Run the Validate a 
Configuration Wizard to check your network configuration. If the condition persists, check 
for hardware or software errors related to the network adapters on this node. Also check for 
failures in any other network components to which the node is connected such as hubs, switches, or bridges.
```


詳細については、「[イベント ID 1135 のクラスターの問題のトラブルシューティング](/windows-server/troubleshoot/troubleshooting-cluster-event-id-1135)」を参照してください。


**リースの有効期限が切れている** / **リースが無効になった**


[監視](#relaxed-monitoring)が環境に対して厳格すぎる場合は、AG または FCI の再起動、エラー、またはフェールオーバーが頻繁に発生することがあります。 また、可用性グループについて、SQL Server エラー ログに次のメッセージが表示される場合があります。 

```
Error 19407: The lease between availability group 'PRODAG' and the Windows Server Failover Cluster has expired. 
A connectivity issue occurred between the instance of SQL Server and the Windows Server Failover Cluster. 
To determine whether the availability group is failing over correctly, check the corresponding availability group 
resource in the Windows Server Failover Cluster
```

```
Error 19419: The renewal of the lease between availability group '%.*ls' and the Windows Server Failover Cluster 
failed because the existing lease is no longer valid. 
``` 

**[接続タイムアウト]**

**セッション タイムアウト** が可用性グループの環境に対して厳格すぎる場合は、次のメッセージが頻繁に表示されることがあります。

```
Error 35201: A connection timeout has occurred while attempting to establish a connection to availability 
replica 'replicaname' with ID [availability_group_id]. Either a networking or firewall issue exists, 
or the endpoint address provided for the replica is not the database mirroring endpoint of the host server instance.
```

```
Error 35206
A connection timeout has occurred on a previously established connection to availability 
replica 'replicaname' with ID [availability_group_id]. Either a networking or a firewall issue 
exists, or the availability replica has transitioned to the resolving role. 
```

**グループがフェールオーバーされない**



**[指定した期間の最大エラー数]** の値が小さすぎて、一時的な問題による断続的なエラーが発生している場合、可用性グループが失敗した状態で終了するおそれがあります。 この値を増やして、一時的な障害をより多く許容するようにします。 

```
Not failing over group <Resource name>, failoverCount 3, failoverThresholdSetting <Number>, computedFailoverThreshold 2. 
```


## <a name="next-steps"></a>次のステップ

詳細については、以下をご覧ください。

- [Azure VM 上の SQL Server に対する HADR 設定](hadr-cluster-best-practices.md)
- [Windows Server フェールオーバー クラスターと Azure VM 上の SQL Server](hadr-windows-server-failover-cluster-overview.md)
- [AlwaysOn 可用性グループと Azure VM 上の SQL Server](availability-group-overview.md)
- [Windows Server フェールオーバー クラスターと Azure VM 上の SQL Server](hadr-windows-server-failover-cluster-overview.md)
- [Azure VM 上の SQL Server を使用したフェールオーバー クラスター インスタンス](failover-cluster-instance-overview.md)
- [フェールオーバー クラスター インスタンスの概要](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
