---
title: Azure Migrate のアーキテクチャ | Microsoft Docs
description: Azure Migrate サービスの概要を示します。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: raynew
ms.openlocfilehash: 16fbf8d3523ac2ab36447aa51a93a0bb5a9fad54
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67810106"
---
# <a name="azure-migrate-architecture-and-processes"></a>Azure Migrate のアーキテクチャとプロセス

[Azure Migrate](migrate-overview.md) では、アプリ、インフラストラクチャ、およびワークロードを検出、評価するため、およびそれらを Microsoft Azure に移行するために役立つツールのハブが提供されます。 このハブには、Azure Migrate ツールと、サードパーティ製の独立系ソフトウェア ベンダー (ISV) オファリングが含まれます。 

 この記事では、Azure Migrate Server Assessment および Azure Migrate Server Migration の評価と移行のアーキテクチャとプロセスについて概要を示します。

## <a name="assessment-with-azure-migrate-server-assessment"></a>Azure Migrate Server Assessment による評価

Azure Migrate Server Assessment では、Azure への移行に関して VMware VM と Hyper-V VM を評価できます。 評価は次のように行われます。

1. **評価の準備**:オンプレミスでは、VMware VM または Hyper-V VM として軽量 Azure Migrate アプライアンスをオンプレミスで設定し、そのアプライアンスを Azure Migrate に登録します。
2. **VM の検出**:オンプレミス VM を検出するために Azure Migrate アプライアンスが実行されます。 
    - 検出された VM には何もインストールする必要はありません。
    - VM のメタデータには、コア、メモリ、ディスク、ディスク サイズ、およびネットワーク アダプターに関する情報が含まれます。
    - パフォーマンス データには、CPU とメモリの使用率、ディスク IOPS、ディスクのスループット (MBps)、およびネットワーク出力 (MBps) に関する情報が含まれます。
- **マシンの収集および評価**:検出が完了した後、Azure portal で、検出された VM をグループにまとめます。このグループは、通常、一緒に移行する VM で構成されます。 グループに対して評価を実行します。


## <a name="vmware-assessment"></a>VMware の評価 

この図は、Azure Migrate Server Assessment を使用して VMware VM の評価がどのように行われるかを示しています。

![評価のアーキテクチャ (VMware)](./media/migrate-architecture/sas-architecture-vmware.png)

このプロセスは次のとおりです。

1. **Azure Migrate アプライアンスをデプロイする**:

    a. Azure portal から (OVA) テンプレートをダウンロードします。

    b. これを vCenter Server マシンにインポートして、VM を作成します。

    c. この VM に接続し、その基本設定を構成し、Azure Migrate に登録します。

2. **検出を開始する**:

    a. アプライアンスで実行されているコレクター アプリに接続し、検出を開始します。

    b. アプライアンスは、メタデータとパフォーマンス データを VM から Azure に継続的に送信します。

    - アプライアンスは Azure Migrate サービスに常時接続されています。
    - 継続的な検出中の環境の変更が取り込まれます。 たとえば、検出スコープへの VM の追加、VM ディスクや NIC の追加などです。

3. **マシンを評価する**:

    a. 検出が完了した後、Azure portal で、検出された VM をグループにまとめます。  グループは、通常、一緒に移行する VM で構成されます。

    b. グループごとに評価を実行します。

4. **評価を確認する**: 

    a. 評価が完了した後、ポータルでそれを表示します。

    b. 詳しく分析する場合は、評価を Excel 形式でダウンロードします。



### <a name="vmware-ports"></a>VMware のポート
Azure Migrate は、VMware の次の接続ポートを使用します。

**ソース** | **ターゲット** | **ポート** | **詳細**
--- | --- | --- | ---
Azure Migrate アプライアンス | Azure Migrate サービス | ターゲット - TCP 443 | メタデータとパフォーマンス データを Azure Migrate に送信します。
Azure Migrate アプライアンス | vCenter Server | ターゲット - TCP 443 | メタデータとパフォーマンス データ用の vCenter Server に接続します。 443 が既定ですが、vCenter が別のポートでリッスンする場合は変更できます。 
RDP クライアント | Azure Migrate アプライアンス | ターゲット - TCP3389 | アプライアンスから検出をトリガーするための RDP 接続。

### <a name="collected-vmware-metadata"></a>収集対象の VMware メタデータ

アプライアンスは、メタデータとパフォーマンス関連のデータを VM から Azure に送信します。

**アクション** | **詳細**
--- | ---
**収集対象のメタデータ** | vCenter VM の名前<br/> vCenter VM のパス (ホスト/クラスター フォルダー)<br/> IP アドレスと MAC アドレス<br/> オペレーティング システム<br/> コア/ディスク/NIC の数<br/> メモリとディスクのサイズ。
**収集対象のパフォーマンス データ** | CPU/メモリの使用量<br/> ディスクごとのデータ (ディスク読み取り/書き込みのスループット、1 秒あたりのディスク読み取り/書き込み)<br/> NIC データ (ネットワーク受信、ネットワーク送信)。<br/><br/> パフォーマンス データは、アプライアンスが vCenter Server に接続した後、継続的に収集されます。 履歴データは収集されません。

## <a name="hyper-v-assessment"></a>Hyper-V の評価

この図は、Azure Migrate Server Assessment を使用して Hyper-V の評価がどのように行われるかを示しています。

![評価のアーキテクチャ (Hyper-V)](./media/migrate-architecture/sas-architecture-hyper-v.png)

このプロセスは次のとおりです。

1. **Azure Migrate アプライアンスを作成する**:

    a. Azure portal から圧縮形式で VM をダウンロードします。

    b. それを Hyper-V ホストにインポートします。

    c. アプライアンス VM に接続します。 その基本設定を構成し、Azure Migrate に登録します。

2. **検出を開始する**:

    a. Azure Migrate アプライアンスに接続して、検出を開始します。 検出された VM には何もインストールする必要はありません。

    b. アプライアンスは、VM のメタデータとパフォーマンス データを Azure Migrate に継続的に送信します。

    - アプライアンスは (CIM セッションを使用して) Azure Migrate サービスに常時接続されています。
    - 継続的な検出中の環境の変更が取り込まれます。 たとえば、検出スコープへの VM の追加、VM ディスクや NIC の追加などです。


3. **マシンを評価する**:

    a. 検出が完了した後、Azure portal で、検出された VM をグループにまとめます。  グループは、通常、一緒に移行する VM で構成されます。

    b. グループごとに評価を実行します。

4. **評価を確認する**:

    a. 評価が完了した後、ポータルでそれを表示します。

    b. 詳しく分析する場合は、評価を Excel 形式でダウンロードします。

### <a name="hyper-v-ports"></a>Hyper-V ポート

Azure Migrate サービスは、Hyper-V の次の接続ポートを使用します。

**ソース** | **ターゲット** | **ポート** | **詳細**
--- | --- | --- | ---
Azure Migrate アプライアンス | Azure Migrate サービス | ターゲット - TCP 443 | メタデータとパフォーマンス データを Azure Migrate に送信します。
Azure Migrate アプライアンス | Hyper-V ホスト/クラスター | ターゲット - 既定の WinRM ポート - HTTP: 5985、HTTPS: 5986 | メタデータとパフォーマンス データ用のホストに接続します。 接続に使用される CIM セッション
RDP クライアント | Azure Migrate アプライアンス | ターゲット - TCP3389 | アプライアンスから検出をトリガーするための RDP 接続。

## <a name="collected-hyper-v-vm-metadata"></a>収集対象の Hyper-V VM メタデータ

アプライアンスは、メタデータとパフォーマンス関連のデータを VM から Azure に送信します。


**アクション** | **詳細**
--- | ---
**収集対象のメタデータ** | VM 名<br/> VM のパス (ホスト/クラスター フォルダー)<br/> IP アドレスと MAC アドレス<br/> オペレーティング システム<br/> コア/ディスク/NIC の数<br/> メモリとディスクのサイズ<br/> ディスク IOPS、ディスク スループット (MBps)、ネットワーク出力 (MBps)
**収集対象のパフォーマンス データ** |  CPU/メモリの使用量<br/> ディスクごとのデータ (ディスク読み取り/書き込みのスループット、1 秒あたりのディスク読み取り/書き込み)<br/> NIC データ (ネットワーク受信、ネットワーク送信)。<br/><br/> パフォーマンス データは、アプライアンスが Hyper-V ホストに接続した後、継続的に収集されます。 履歴データは収集されません。




## <a name="migration-with-azure-migrate-server-migration"></a>Azure Migrate Server Migration による移行

Azure Migrate Server Migration を使用して、次のものを Azure に移行できます。

- オンプレミスの VMware VM
- オンプレミスの Hyper-V VM
- オンプレミスの物理サーバー
- AWS Windows VM インスタンス
- GCP Windows VM インスタンス

移行プロセスは、移行する内容によって若干異なります。


## <a name="migrate-vmware-vms"></a>VMware VM を移行する

Azure Migrate Server Migration では、オンプレミスの VMware VM を移行するために、次の 2 つの方法が提供されています。

- **エージェントレス レプリケーション**:VM に何もインストールする必要なく VM を移行します。
- **エージェントベースのレプリケーション**。 レプリケーションのために VM にエージェントをインストールします。

デプロイの観点からエージェントレス レプリケーションの方が簡単ですが、現時点では多数の制限があります。 これらの制限について[詳細を確認](server-migrate-overview.md)してください。
 

### <a name="agent-based-migration"></a>エージェントベースの移行

VMware VM のエージェントベースの移行では、表で説明されているように、多数のコンポーネントをデプロイする必要があります。

**コンポーネント** | **要件** | **詳細**
--- | --- | ---
**構成サーバー マシン** | ダウンロードした OVF テンプレートからデプロイされる単一のオンプレミス VMware VM。<br/><br/> マシンで、構成サーバー、プロセス サーバーなど、オンプレミスのすべての Site Recovery コンポーネントを実行します。 | **構成サーバー**:オンプレミスと Azure の間の通信を調整し、データのレプリケーションを管理します。<br/><br/> **プロセス サーバー**:構成サーバーに既定でインストールされます。 レプリケーション データを受信し、それをキャッシュ、圧縮、暗号化によって最適化して、Azure に送信します。 また、プロセス サーバーは、VM に Azure Site Recovery モビリティ サービスをインストールし、オンプレミス マシンの自動検出を実行します。
**モビリティ サービス** | レプリケートする各 VMware VM にモビリティ サービスをインストールする必要があります。 | プロセス サーバーから自動的にインストールできるようにすることをお勧めします。 また、サービスを手動でインストールしたり、System Center Configuration Manager などの自動デプロイ方法を使用したりすることができます。





### <a name="agent-based-replication-process"></a>エージェントベースのレプリケーション プロセス

![レプリケーション プロセス](./media/migrate-architecture/v2a-architecture-henry.png)

1. VM のレプリケーションを有効にすると、Azure への初回のレプリケーションが開始されます。 
    - レプリケーションは、VM 上で実行されているモビリティ サービス エージェントを使用して、ブロックレベルでほぼ連続的です。
    - レプリケーション ポリシー設定が適用されます。
        - **[RPO しきい値]** 。 この設定は、レプリケーションには影響しません。 これは監視に役立ちます。 現在の RPO が指定したしきい値の上限を超えた場合、イベントが発生し、必要に応じて電子メールが送信されます。
        - **[復旧ポイントのリテンション期間]** 。 この設定は、中断が発生したときに、どこまで戻るかを時間で指定します。 Premium Storage の最大リテンション期間は 24 時間です。 Standard Storage では 72 時間です。 
        - **[App-consistent snapshots]\(アプリ整合性スナップショット\)** 。 ご利用のアプリのニーズに応じて、アプリ整合性のスナップショットを 1 から 12 時間ごとに取得できます。 スナップショットは標準の Azure BLOB スナップショットです。 VM 上で実行されているモビリティ エージェントでは、この設定に従って VSS スナップショットが要求され、レプリケーション ストリームのアプリケーション整合性ポイントとして特定の時点がブックマークされます。

2. トラフィックは、インターネット経由で Azure Storage のパブリック エンドポイントにレプリケートされます。

    - 別の方法として、Azure ExpressRoute と [Microsoft ピアリング](../expressroute/expressroute-circuit-peerings.md#microsoftpeering)を使用できます。
    - オンプレミス サイトから Azure へのサイト間仮想プライベート ネットワーク (VPN) を介したトラフィックのレプリケートはサポートされていません。
3. 初回のレプリケーションの終了後、Azure への差分変更のレプリケーションが開始されます。 マシンに対する追跡された変更は、プロセス サーバーに送信されます。
2. 通信は、次のように行われます。

    - VM は、レプリケーション管理のために、受信ポート HTTPS 443 でオンプレミスの構成サーバーと通信します。
    - 構成サーバーは、送信ポート HTTPS 443 経由で Azure によるレプリケーションを調整します。
    - VM は、受信ポート HTTPS 9443 でレプリケーション データを (構成サーバー マシン上で実行されている) プロセス サーバーに送信します。 このポートは変更可能です。
    - プロセス サーバーは、レプリケーション データを受信し、データを最適化して暗号化し、送信ポート 443 経由で Azure ストレージに送信します。
5. レプリケーション データ ログは、まず Azure のキャッシュ ストレージ アカウントに記録されます。 これらのログは処理され、データは Azure マネージド ディスク (Azure Site Recovery シード ディスク) に保存されます。 復旧ポイントはこのディスク上に作成されます。







## <a name="next-steps"></a>次の手順

- Azure Migrate について[よく寄せられる質問を確認](resources-faq.md)します。
- コミュニティからのヘルプが必要な場合は、[Azure Migrate MSDN フォーラム](https://social.msdn.microsoft.com/Forums/home?forum=AzureMigrate&filter=alltypes&sort=lastpostdesc)または [Stack Overflow](https://stackoverflow.com/search?q=azure+migrate) にアクセスしてください。

