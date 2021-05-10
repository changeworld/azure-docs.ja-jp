---
title: Azure Migrate での物理的な検出および評価のサポート
description: Azure Migrate Discovery and Assessment を使用した物理的な検出および評価のサポートについて説明します
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 09110bb15f853f20b0be9d8c4c6f5088e7a55d8b
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382982"
---
# <a name="support-matrix-for-physical-server-discovery-and-assessment"></a>物理サーバーの検出および評価のサポート マトリックス 

この記事では、[Azure Migrate: Discovery and assessment](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) ツールを使用して、Azure に移行するために物理サーバーを評価する場合の前提条件とサポート要件について説明します。 物理サーバーを Azure に移行する場合は、[移行のサポート マトリックス](migrate-support-matrix-physical-migration.md)を確認してください。

物理サーバーを評価するには、プロジェクトを作成し、そのプロジェクトに Azure Migrate: Discovery and assessment ツールを追加します。 ツールを追加した後、[Azure Migrate アプライアンス](migrate-appliance.md)をデプロイします。 アプライアンスでオンプレミス サーバーが継続的に検出され、サーバーのメタデータとパフォーマンス データが Azure に送信されます。 検出が完了したら、検出されたサーバーをグループにまとめ、グループに対して評価を実行します。

## <a name="limitations"></a>制限事項

**サポート** | **詳細**
--- | ---
**評価の上限** | 1 つの[プロジェクト](migrate-support-matrix.md#project)で最大 35,000 個の物理サーバーを検出して評価できます。
**プロジェクトの制限** | 1 つの Azure サブスクリプションで複数のプロジェクトを作成できます。 物理サーバーに加えて、それぞれの評価の上限に達するまで、1 つのプロジェクトに VMware 上および Hyper-V 上のサーバーを含めることができます。
**検出** | Azure Migrate アプライアンスでは、最大 1000 台の物理サーバーを検出できます。
**評価** | 1 つのグループに最大 35,000 台のサーバーを追加できます。<br/><br/> 1 回の評価で最大 35,000 台のサーバーを評価できます。

評価の詳細については[こちら](concepts-assessment-calculation.md)をご覧ください。

## <a name="physical-server-requirements"></a>物理サーバーの要件

**物理サーバーの展開:** 物理サーバーは、スタンドアロンにすることも、クラスターにデプロイすることもできます。

**オペレーティング システム:** すべての Windows および Linux オペレーティング システムを、移行のために評価することができます。

**アクセス許可:**

- Windows サーバーの場合、ドメイン参加済みのサーバーにはドメイン アカウントを、ドメインに参加していないサーバーにはローカル アカウントを使用します。 次のグループにユーザー アカウントを追加する必要があります:リモート管理ユーザー、パフォーマンス モニター ユーザー、パフォーマンス ログ ユーザー。
    > [!Note]
    > Windows Server 2008 および 2008 R2 の場合は、サーバーに WMF 3.0 がインストールされていることと、サーバーにアクセスするために使用されるドメインおよびローカル アカウントが、Performance Monitor Users、Performance Log Users、および WinRMRemoteWMIUsers グループに追加されていることを確認してください。
- Linux サーバーの場合は、検出する Linux サーバーのルート アカウントが必要です。 または、次のコマンドを使用して、必要な機能を持つ非ルート アカウントを設定することもできます。

**コマンド** | **目的**
--- | --- |
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/fdisk <br></br> setcap CAP_DAC_READ_SEARCH+eip /sbin/fdisk _(/usr/sbin/fdisk が存在しない場合)_ | ディスク構成データを収集するため
setcap "cap_dac_override,cap_dac_read_search,cap_fowner,cap_fsetid,cap_setuid,<br>cap_setpcap,cap_net_bind_service,cap_net_admin,cap_sys_chroot,cap_sys_admin,<br>cap_sys_resource,cap_audit_control,cap_setfcap=+eip" /sbin/lvm | ディスクのパフォーマンス データを収集するため
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/dmidecode | BIOS のシリアル番号を収集するため
chmod a+r /sys/class/dmi/id/product_uuid | BIOS の GUID を収集するため

## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate アプライアンスの要件

Azure Migrate では、[Azure Migrate アプライアンス](migrate-appliance.md)を使用して検出と評価を行います。 物理サーバーのアプライアンスは、VM または物理サーバー上で実行できます。

- 物理サーバーの[アプライアンスの要件](migrate-appliance.md#appliance---physical)を確認します。
- アプライアンスが[パブリック](migrate-appliance.md#public-cloud-urls)および [Government](migrate-appliance.md#government-cloud-urls) クラウドでアクセスする必要がある URL について確認します。
- アプライアンスは、Azure portal からダウンロードした [PowerShell スクリプト](how-to-set-up-appliance-physical.md)を使用して設定します。
Azure Government で、[このスクリプトを使用して](deploy-appliance-script-government.md)アプライアンスをデプロイします。

## <a name="port-access"></a>ポート アクセス

次の表は、評価のためのポート要件をまとめたものです。

**[デバイス]** | **接続**
--- | ---
**アプライアンス** | TCP ポート 3389 で、アプライアンスへのリモート デスクトップ接続を許可するための受信接続。<br/><br/> ポート 44368 で、次の URL を使用してアプライアンス管理アプリにリモートでアクセスするためのインバウンド接続: ``` https://<appliance-ip-or-name>:44368 ```<br/><br/> ポート 443 (HTTPS) で検出とパフォーマンスのメタデータを Azure Migrate に送信するためのアウトバウンド接続。
**物理サーバー** | **Windows:** WinRM ポート 5985 (HTTP) または 5986 (HTTPS) で Windows サーバーから構成とパフォーマンスのメタデータをプルするためのインバウンド接続。 <br/><br/> **Linux:** ポート 22 (TCP) で Linux サーバーから構成とパフォーマンスのメタデータをプルするための受信接続。 |

## <a name="agent-based-dependency-analysis-requirements"></a>エージェント ベースの依存関係の分析の要件

[依存関係の分析](concepts-dependency-visualization.md)を使用すると、評価して Azure に移行するオンプレミスのサーバー間の依存関係を特定できます。 この表は、エージェント ベースの依存関係の分析を設定するための要件をまとめたものです。 現在、物理サーバーではエージェント ベースの依存関係の分析のみがサポートされています。

**要件** | **詳細**
--- | ---
**デプロイ前** | Azure Migrate: Discovery and Assessment ツールがプロジェクトに追加された状態で、プロジェクトを準備する必要があります。<br/><br/>  オンプレミスのサーバーを検出するには、Azure Migrate アプライアンスをセットアップした後、依存関係の視覚化をデプロイします<br/><br/> 初めてプロジェクトを作成する方法については[こちら](create-manage-projects.md)を参照してください。<br/> 既存のプロジェクトに評価ツールを追加方法については[こちら](how-to-assess.md)を参照してください。<br/> [Hyper-V](how-to-set-up-appliance-hyper-v.md)、[VMware](how-to-set-up-appliance-vmware.md)、または物理サーバーの評価のために Azure Migrate アプライアンスを設定する方法について説明します。
**Azure Government** | 依存関係の視覚化は、Azure Government では使用できません。
**Log Analytics** | Azure Migrate は、依存関係の視覚化のために [Azure Monitor ログ](../azure-monitor/logs/log-query-overview.md)の [Service Map](../azure-monitor/vm/service-map.md) ソリューションを使用します。<br/><br/> 新規または既存の Log Analytics ワークスペースをプロジェクトに関連付けます。 プロジェクトのワークスペースは、追加後に変更することはできません。 <br/><br/> ワークスペースは、プロジェクトと同じサブスクリプションに含まれている必要があります。<br/><br/> ワークスペースは、米国東部リージョン、東南アジア リージョン、または西ヨーロッパ リージョンに存在する必要があります。 他のリージョンにあるワークスペースをプロジェクトに関連付けることはできません。<br/><br/> ワークスペースは、[Service Map がサポートされている](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions)リージョンに存在する必要があります。<br/><br/> Log Analytics では、Azure Migrate に関連付けられたワークスペースは、移行プロジェクト キーとプロジェクト名のタグが付けられます。
**必要なエージェント** | 分析する各サーバーに次のエージェントをインストールします。<br/><br/> [Microsoft Monitoring エージェント (MMA)](../azure-monitor/agents/agent-windows.md)。<br/> [依存関係エージェント](../azure-monitor/agents/agents-overview.md#dependency-agent)。<br/><br/> オンプレミス サーバーがインターネットに接続されていない場合、Log Analytics ゲートウェイをダウンロードしてインストールする必要があります。<br/><br/> [依存関係エージェント](how-to-create-group-machine-dependencies.md#install-the-dependency-agent)と [MMA](how-to-create-group-machine-dependencies.md#install-the-mma) のインストールの詳細を確認してください。
**Log Analytics ワークスペース** | ワークスペースは、プロジェクトと同じサブスクリプションに含まれている必要があります。<br/><br/> Azure Migrate では、米国東部、東南アジア、および西ヨーロッパの各リージョンにあるワークスペースがサポートされます。<br/><br/>  ワークスペースは、[Service Map がサポートされている](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions)リージョンに存在する必要があります。<br/><br/> プロジェクトのワークスペースは、追加後に変更することはできません。
**コスト** | Service Map ソリューションでは、(Log Analytics ワークスペースをプロジェクトに関連付けた日から) 最初の 180 日間は料金が発生しません。<br/><br/> 180 日が経過すると、Log Analytics の標準の料金が適用されます。<br/><br/> この関連付けられた Log Analytics ワークスペース内で Service Map 以外のソリューションを使用すると、Log Analytics の[標準の料金](https://azure.microsoft.com/pricing/details/log-analytics/)が発生します。<br/><br/> プロジェクトが削除される際、それに伴ってワークスペースが削除されることはありません。 プロジェクトが削除された後は、Service Map を無料で使用することはできず、Log Analytics ワークスペースの有料階層に応じて各ノードの料金が請求されます。<br/><br/>Azure Migrate の一般提供 (GA - 2018 年 2 月 28 日) 前に作成したプロジェクトがある場合、Service Map の追加料金が発生する可能性があります。 GA の前の既存のワークスペースには引き続き課金されるため、180 日後にのみ支払いが発生するように、新しいプロジェクトを作成することをお勧めします。
**管理** | エージェントをワークスペースに登録する場合、プロジェクトで提供される ID とキーを使用します。<br/><br/> Azure Migrate 以外で Log Analytics ワークスペースを使用できます。<br/><br/> 関連付けられたプロジェクトを削除しても、ワークスペースは自動的に削除されません。 [手動で削除してください](../azure-monitor/logs/manage-access.md)。<br/><br/> プロジェクトを削除する場合を除き、Azure Migrate で作成されたワークスペースは削除しないでください。 削除した場合は、依存関係可視化機能は、期待どおりに機能しません。
**インターネット接続** | サーバーがインターネットに接続されていない場合は、それらに Log Analytics ゲートウェイをインストールする必要があります。
**Azure Government** | エージェントベースの依存関係の分析はサポートされていません。

## <a name="next-steps"></a>次のステップ

[物理的な検出および評価を準備します](./tutorial-discover-physical.md)。