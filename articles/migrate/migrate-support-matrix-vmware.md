---
title: Azure Migrate での VMware 評価サポート
description: Azure Migrate での VMware 評価サポートの詳細を知る
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 74dae71404fe827c9e19d5e3042afd2f98a7a5dd
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2020
ms.locfileid: "76154688"
---
# <a name="support-matrix-for-vmware-assessment"></a>VMware 評価のサポートマトリックス 

この記事では、[Azure Migrate を使用した VMware Vm 評価サポートの設定と制限事項について説明します。Server Assessment](migrate-services-overview.md#azure-migrate-server-migration-tool) でクリックします。 VMware Vm を Azure に移行する方法については、「[移行サポート マトリックス](migrate-support-matrix-vmware-migration.md)」を参照してください。

## <a name="overview"></a>概要

この記事を使用して オンプレミス マシンの Azure への移行を評価するには、Azure Migrate の追加が必要です。Azure Migrate プロジェクトのサーバー評価ツール [Azure Migrate アプライアンス](migrate-appliance.md)をデプロイします。 アプライアンスは、オンプレミス マシンを継続的に検出し、構成データとパフォーマンスデータを Azure に送信します。 マシンの検出後、検出されたマシンをグループにまとめ、グループの評価を実行します。


## <a name="limitations"></a>制限事項

**サポート** | **詳細**
--- | ---
**評価の上限**| 1 つの[プロジェクト](migrate-support-matrix.md#azure-migrate-projects)で最大 35,000 台の VMware VM を検出・評価できます。
**プロジェクトの制限** | 1 つの Azure サブスクリプションで複数のプロジェクトを作成できます。 評価の上限に達するまで、1 つのプロジェクトに VMware VM、Hyper-V VM、および物理サーバーを含めることができます。
**検出** | Azure Migrate アプライアンスで、vCenter Server 上の VMware VM を最大 10,000 台検出できます。
**評価** | 1 つのグループに最大 35,000 個のマシンを追加できます。<br/><br/> 1 回の評価で最大 35,000 個の VM を評価できます。

評価の詳細については[こちら](concepts-assessment-calculation.md)をご覧ください。


## <a name="application-discovery"></a>アプリケーションの検出

マシンの検出に加えて、Azure Migrate は以下の内容を実行できます。Server Assessment で、アプリ、ロール、フィーチャーを検出できます。 アプリ インベントリを検出することで、オンプレミスのワークロードに合わせて調整された移行パスを特定し、計画することができます。 

**サポート** | **詳細**
--- | ---
**検出** | 検出はエージェントレスで、マシンのゲスト資格情報を使用し、WMI と SSH 呼び出しでマシンにリモートアクセスします。
**サポートされているマシン** | オンプレミス VMware VM
**マシンのオペレーティング システム** | Windows と Linux のすべてのバージョン。
**vCenter の資格情報** | 読み取り専用アクセス権を持ち、[仮想マシン] > [ゲスト操作] の権限が有効な vCenter Server アカウント。
**VM の資格情報** | 現在、すべての Windows サーバーに対して 1 つの資格情報と、すべての Linux サーバーに対して 1 つの資格情報を使用することがサポートされています。<br/><br/> Windows VM 用にゲスト ユーザー アカウントを作成し、すべての Linux VM 用に通常/標準ユーザー アカウント (非 sudo アクセス) を作成します。
**VMware ツール** | 検出する VM に VMware ツールがインストールされ、実行されている必要があります。
**ポート アクセス** | 検出する VM を実行している ESXi ホストでは、Azure Migrate アプライアンスが TCP ポート 443 に接続できる必要があります。
**制限** | アプリ検出に関しては、1アプライアンスにつき最大 10,000 個を検出できます。 

## <a name="vmware-requirements"></a>VMware の要件

**VMware** | **詳細**
--- | ---
**vCenter Server** | 検出・評価するマシンは、 vCenter Server バージョン 5.5、6.0、6.5、または 6.7 でマネージドされている必要があります。
**アクセス許可 (評価)** | vCenter Server の読み取り専用アカウント。
**アクセス許可 (アプリ検出)** | 読み取り専用アクセス権を持つ vCenter Server アカウントと、仮想マシン > ゲスト操作 の権限が有効。
**アクセス許可 (依存関係の視覚化)** | 読み取り専用アクセス権を持つ Center Server アカウントと、**仮想マシン** > **ゲスト操作**.の権限が有効。


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate アプライアンスの要件

Azure Migrate では、[Azure Migrate アプライアンス](migrate-appliance.md)を使用して検出と評価を行います。 VMware 用アプライアンスは、vCenter Server にインポートされた OVA テンプレートを使用してデプロイされます。 

- VMware の[アプライアンスの要件](migrate-appliance.md#appliance---vmware)を確認してください。
- アプライアンスがアクセスする必要のある [URL](migrate-appliance.md#url-access) を確認してください。

## <a name="port-access"></a>ポート アクセス

**[デバイス]** | **[接続]**
--- | ---
アプライアンス | TCP ポート 3389 で、アプライアンスへのリモート デスクトップ接続を許可するための受信接続。<br/><br/> ポート 44368 で、次の URL を使用してアプライアンス管理アプリにリモートでアクセスするためのインバウンド接続: ```https://<appliance-ip-or-name>:44368``` <br/><br/>ポート 443、5671、5672 で、検出とパフォーマンスのメタデータを Azure Migrate に送信するための送信接続。
vCenter サーバー | TCP ポート 443 で、アプライアンスが評価用に構成およびパフォーマンスのメタデータを収集できるようにするインバウンド接続。 <br/><br/> 既定では、アプライアンスはポート 443 で vCenter に接続します。 vCenter Server が別のポートでリッスンする場合、検出の設定時にポートを変更できます。

## <a name="agent-based-dependency-visualization"></a>エージェントベースの依存関係の視覚化

[依存関係の視覚化](concepts-dependency-visualization.md)は、評価および移行するマシン間の依存関係を視覚化するのに役立ちます。 エージェントベースの視覚化に関する要件と制限事項は、以下の表の通りです


**要件** | **詳細**
--- | ---
**デプロイ** | 依存関係の視覚化をデプロイする前に、Azure Migrate:Server Assessment ツールプロジェクトに追加して、Azure Migrate プロジェクトを配置する必要があります。 オンプレミスのマシンを検出するには、Azure Migrate アプライアンスをセットアップした後、依存関係の視覚化をデプロイします。<br/><br/> 依存関係の視覚化は、Azure Government では使用できません。
**サービス マップ** | エージェントベースの依存関係の視覚化では、[Azure Monitor ログ[で ](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map)Service Map](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) ソリューションが使用されます。<br/><br/> デプロイするには、新規または既存の Log Analytics ワークスペースを Azure Migrate プロジェクトに関連付けます。
**Log Analytics ワークスペース** | このワークスペースは、Azure Migrate プロジェクトと同じサブスクリプションに含まれている必要があります。<br/><br/> Azure Migrate では、米国東部、東南アジア、および西ヨーロッパの各リージョンにあるワークスペースがサポートされます。<br/><br/>  ワークスペースは、[Service Map がサポートされている](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)リージョンに存在する必要があります。<br/><br/> Azure Migrate プロジェクトのワークスペースは、追加後に変更できません。
**料金** | Service Map ソリューションでは、(Log Analytics ワークスペースを Azure Migrate プロジェクトに関連付けた日から) 最初の 180 日は料金が発生しません。<br/><br/> 180 日が経過すると、Log Analytics の標準の料金が適用されます。<br/><br/> この関連付けられた Log Analytics ワークスペース内で Service Map 以外のソリューションを使用すると、標準の Log Analytics 料金が発生します。<br/><br/> Azure Migrate プロジェクトを削除しても、ワークスペースが一緒に削除されることはありません。 プロジェクトの削除後、Service Map は無料にならず、Log Analytics ワークスペースの有料レベルに応じて各ノードの料金が請求されます。
**[エージェント]** | エージェントベースの依存関係の視覚化では、分析する各マシンに 2 つのエージェントをインストールする必要があります。<br/><br/> - [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> - [依存関係エージェント](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent)。 
**インターネット接続** | マシンがインターネットに接続されていない場合は、それらに Log Analytics ゲートウェイをインストールする必要があります。


## <a name="agentless-dependency-visualization"></a>エージェントレスの依存関係の視覚化

このオプションは現在プレビューの段階です。 [詳細については、こちらを参照してください](how-to-create-group-machine-dependencies-agentless.md)。 要件の概要については、次の表で説明しています。

**要件** | **詳細**
--- | ---
**デプロイ** | 依存関係の視覚化をデプロイする前に、Azure Migrate:Server Assessment ツールプロジェクトに追加して、Azure Migrate プロジェクトを配置する必要があります。 オンプレミスのマシンを検出するには、Azure Migrate アプライアンスをセットアップした後、依存関係の視覚化をデプロイします。
**VM のサポート** | 現在、VMware VM のみでサポートされています。
**Windows VM** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64 ビット)
**Linux VM** | Red Hat Enterprise Linux 7、6、5<br/> Ubuntu Linux 14.04、16.04<br/> Debian 7、8<br/> Oracle Linux 6、7<br/> CentOS 5、6、7。
**Windows アカウント** |  視覚化には、ゲスト アクセス権を持つユーザー アカウントが必要です。
**Linux アカウント** | 視覚化には、ルート特権を持つユーザー アカウントが必要です。<br/><br/> また、ユーザー アカウントには /bin/netstat および /bin/ls ファイルに対する次の権限が必要です。CAP_DAC_READ_SEARCH と CAP_SYS_PTRACE。
**VM エージェント** | VM にエージェントは必要ありません。
**VMware ツール** | 分析する VM に VMware ツールがインストールされ、実行されている必要があります。
**vCenter の資格情報** | 読み取り専用アクセス権を持ち、[仮想マシン] > [ゲスト操作] の権限が有効な vCenter Server アカウント。
**ポート アクセス** | 分析する VM を実行している ESXi ホストでは、Azure Migrate アプライアンスが TCP ポート 443 に接続できる必要があります。



## <a name="next-steps"></a>次のステップ

- 評価作成のベストプラクティスを[確認](best-practices-assessment.md)します。
- VMware 評価の[準備](tutorial-prepare-vmware.md)
