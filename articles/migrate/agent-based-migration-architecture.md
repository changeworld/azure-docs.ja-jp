---
title: Azure Migrate Server Migration でのエージェント ベースの移行
description: Azure Migrate でのエージェントベースの VMware VM の移行に関する概要を示します。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: raynew
ms.openlocfilehash: d345d707cbf58f48466c3bd830d93250d13397c6
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425861"
---
# <a name="agent-based-migration-architecture"></a>エージェントベースの移行アーキテクチャ

この記事では、[Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-assessment-tool) ツールを使用したエージェントベースのレプリケーションに対して使用されるアーキテクチャとプロセスの概要を示します。

Azure Migrate: Server Migration を使用して VMware VM をレプリケートするには、次の 2 つの方法があります。

- この記事で説明されているように、エージェントベースのレプリケーションを使用して VM を移行する。
- エージェントレス レプリケーションを使用して VMware VM を移行する。 この場合、VM に何もインストールする必要なく、VM が移行されます。

詳細については、VMware VM の移行方法の[選択と比較](server-migrate-overview.md)に関する記事を確認してください。 


## <a name="agent-based-migration"></a>エージェントベースの移行

エージェントベースの移行は、オンプレミスの VMware VM と物理サーバーを Azure に移行するために使用されます。 また、その他のオンプレミスの仮想サーバーや、AWS インスタンスや GCP VM などのプライベートおよびパブリック クラウド VM の移行にも使用できます。 Azure Migrate でのエージェントベースの移行には、[Azure Site Recovery](../site-recovery/site-recovery-overview.md) サービスからのバックエンド機能が利用されます。


## <a name="architectural-components"></a>アーキテクチャ コンポーネント

次の図に、エージェントベースの移行に関連するコンポーネントを示します。

![Architecture](./media/agent-based-replication-architecture/architecture.png)

次の表で、エージェントベースの移行のために使用されるコンポーネントについて説明します。

**コンポーネント** | **詳細** | **インストール**
--- | --- | ---
**レプリケーション アプライアンス** | レプリケーション アプライアンス (構成サーバー/プロセス サーバー) は、オンプレミス環境と Server Migration 間のブリッジとして機能するオンプレミス マシンです。 Server Migration によってレプリケーションと移行を調整できるように、アプライアンスではオンプレミスのマシン インベントリを検出します。 アプライアンスには、次の 2 つのコンポーネントがあります。<br/><br/> **構成サーバー**: Server Migration に接続し、レプリケーションを調整します。<br/> **プロセス サーバー**:データ レプリケーションを処理します。 プロセス サーバーでは、マシン データを受信し、圧縮と暗号化を行って、Azure に送信します。 Azure 上では、Server Migration によってデータがマネージド ディスクに書き込まれます。 | 既定では、プロセス サーバーは構成サーバーとともにレプリケーション アプライアンスにインストールされます。
**モビリティ サービス** | モビリティ サービスは、レプリケートし、移行する各マシンにインストールされるエージェントです。 マシンからプロセス サーバーにレプリケーション データを送信します。 | さまざまなバージョンの Mobility Service のインストール ファイルが、レプリケーション アプライアンス上に配置されています。 レプリケートするマシンのオペレーティング システムとバージョンに従い、必要なエージェントをダウンロードし、インストールします。

## <a name="mobility-service-installation"></a>モビリティ サービスのインストール

次の方法でモビリティ サービスをデプロイできます。

- **プッシュ インストール**:マシンの保護を有効にすると、プロセス サーバーによってモビリティ サービスがインストールされます。 
- **手動インストール**:UI またはコマンド プロンプトを使用して、各マシンにモビリティ サービスを手動でインストールできます。

モビリティ サービスは、レプリケーション アプライアンスおよびレプリケート対象のマシンと通信します。 レプリケーション アプライアンス、プロセス サーバー、またはレプリケート対象のマシンでウイルス対策ソフトウェアを実行している場合、次のフォルダーをスキャンから除外する必要があります。


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86)\Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent (モビリティ サービスがインストールされている Windows マシン)

## <a name="replication-process"></a>レプリケーション プロセス

1. マシンのレプリケーションを有効にすると、Azure への初回のレプリケーションが開始されます。
2. 初回のレプリケーション中、モビリティ サービスはマシンのディスクからデータを読み取り、それをプロセス サーバーに送信します。
3. このデータは、Azure サブスクリプションでディスクのコピーをシードするために使用されます。 
4. 初回のレプリケーションの終了後、Azure への差分変更のレプリケーションが開始されます。 レプリケーションは、ブロック レベルで、ほぼ連続的です。
4. Mobility Service では、オペレーティング システムのストレージ サブシステムと統合することで、ディスク メモリへの書き込みをインターセプトします。 この方法では、増分レプリケーションに対して、レプリケートするマシン上でのディスク I/O 操作が回避されます。 
5. マシンの追跡対象の変更は、受信ポート HTTPS 9443 でプロセス サーバーに送信されます。 このポートは変更可能です。 プロセス サーバーは、圧縮と暗号化を行い、Azure に送信します。 

## <a name="ports"></a>Port

**[デバイス]** | **[接続]**
--- | --- 
**マシンのレプリケート** | VM で実行されるモビリティ サービスは、レプリケーション管理のために、受信ポート HTTPS 443 でオンプレミスのレプリケーション アプライアンスと通信します。<br/><br/> マシンでは、受信ポート HTTPS 9443 でレプリケーション データをプロセス サーバーに送信します。 このポートは変更可能です。
**レプリケーション アプライアンス** | レプリケーション アプライアンスは、アウトバウンド ポート HTTPS 443 経由で Azure によるレプリケーションを調整します。
**プロセス サーバー** | プロセス サーバーは、レプリケーション データを受信し、データを最適化して暗号化し、送信ポート 443 経由で Azure ストレージに送信します。


## <a name="performance-and-scaling"></a>パフォーマンスとスケーリング

既定では、構成サーバーとプロセス サーバーの両方を実行する単一のレプリケーション アプライアンスをデプロイします。 少数のマシンのみをレプリケートする場合は、このデプロイで十分です。 ただし、数百台のマシンをレプリケートして移行する場合、単一のプロセス サーバーでは、すべてのレプリケーション トラフィックを処理できない可能性があります。 この場合、追加のスケールアウト プロセス サーバーをデプロイできます。

### <a name="plan-vmware-deployment"></a>VMware のデプロイを計画する

VMware VM をレプリケートする場合、[VMware 用の Site Recovery Deployment Planner](../site-recovery/site-recovery-deployment-planner.md) を使用して、日次データ変化率や必要なプロセス サーバーなどのパフォーマンス要件を特定できます。

### <a name="replication-appliance-capacity"></a>レプリケーション アプライアンスのキャパシティ

デプロイに追加のプロセス サーバーが必要かどうかを判断するために、この表の値を参考にしてください。

- 日次変化率 (チャーン レート) が 2 TB を超える場合、追加のプロセス サーバーをデプロイします。
- 200 台を超えるマシンをレプリケートする場合、追加のレプリケーション アプライアンスをデプロイします。

**CPU** | **[メモリ]** | **空き領域 (データ キャッシュ)** | **チャーン レート** | **レプリケーションの制限**
--- | --- | --- | --- | ---
8 vCPU (2 ソケット * 4 コア \@ 2.5 GHz) | 16 GB | 300 GB | 500 GB 以下 | 100 台未満のマシン 
12 vCPU (2 ソケット * 6 コア \@ 2.5 GHz) | 18 GB | 600 GB | 501 GB ～ 1 TB | 100 台 ～ 150 台のマシン
16 vCPU (2 ソケット * 8 コア \@ 2.5 GHz) | 32 G1 |  1 TB (テラバイト) | 1 TB ～ 2 TB | 151 台 ～ 200 台のマシン

### <a name="sizing-scale-out-process-servers"></a>スケールアウト プロセス サーバーのサイズ設定

スケールアウト プロセス サーバーをデプロイする必要がある場合、この表を参考にして、サーバーのサイズを判断してください。

**プロセス サーバー** | **データ キャッシュ用の空き領域** | **チャーン レート** | **レプリケーションの制限**
--- | --- | --- | --- 
4 vCPU (2 ソケット * 2 コア \@ 2.5 GHz)、8 GB メモリ | 300 GB | 250 GB 以下 | 最大 85 台のマシン 
8 vCPU (2 ソケット * 4 コア \@ 2.5 GHz)、12 GB メモリ | 600 GB | 251 GB ～ 1 TB    | 86 台 ～ 150 台のマシン
12 vCPU (2 ソケット * 6 コア \@ 2.5 GHz)、24 GB メモリ | 1 TB (テラバイト) | 1 ～ 2 TB | 151 台 ～ 225 台のマシン

## <a name="throttle-upload-bandwidth"></a>アップロード帯域幅を調整します。

Azure にレプリケートされる VMware トラフィックは、特定のプロセス サーバーを経由します。 プロセス サーバーとして実行されているマシンで帯域幅を調整することにより、アップロードのスループットを制限できます。 次のレジストリ キーを使用して、帯域幅に影響を与えることができます。

- HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM レジストリ値では、ディスクのデータ転送 (初期レプリケーションまたは差分レプリケーション) に使用されるスレッドの数を指定します。 値を大きくすると、レプリケーションに使用されるネットワーク帯域幅が増加します。 既定値は 4 です。 最大値は 32 です。 トラフィックを監視して値を最適化できます。
- さらに、プロセス サーバー コンピューターの帯域幅を次のようにして調整できます。

    1. プロセス サーバー コンピューターで、Azure Backup MMC スナップインを開きます。 デスクトップまたはフォルダー C:\Program Files\Microsoft Azure Recovery Services Agent\bin に、ショートカットがあります。 
    2. スナップインで **[プロパティの変更]** を選択します。
    3. **[調整]** で **[バックアップ操作用のインターネット使用帯域幅の調整を有効にする]** を選択します。 勤務時間用と勤務時間外用の制限値を設定します。 有効な範囲は、512 Kbps から 1,023 Mbps です。


## <a name="next-steps"></a>次のステップ

[VMware](tutorial-migrate-vmware-agent.md) または[物理サーバー](tutorial-migrate-physical-virtual-machines.md)に対して、[エージェントベースの移行](tutorial-migrate-vmware-agent.md)を試行します。
