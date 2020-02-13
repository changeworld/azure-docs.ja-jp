---
title: Azure Migrate での物理サーバーの評価のサポート
description: Azure Migrate での物理サーバーの評価のサポートについて説明します。
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: f2698d0ff046147599a8c5c791a0980a54090932
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990336"
---
# <a name="support-matrix-for-physical-server-assessment"></a>物理サーバーの評価のサポート マトリックス 

[Azure Migrate サービス](migrate-overview.md)を使用すると、コンピューターを評価したり、Microsoft Azure クラウドに移行したりできます。 この記事では、オンプレミスの物理サーバーを評価して移行する際にサポートされる設定と制限について概要を説明します。


## <a name="overview"></a>概要

この記事に従って Azure への移行についてオンプレミスのマシンを評価するには、Azure Migrate: Server Assessment ツールを Azure Migrate プロジェクトに追加します。 [Azure Migrate アプライアンス](migrate-appliance.md)をデプロイします。 アプライアンスは、オンプレミス マシンを継続的に検出し、構成データとパフォーマンス データを Azure に送信します。 マシンの検出後、検出されたマシンをグループにまとめ、グループの評価を実行します

## <a name="limitations"></a>制限事項

**サポート** | **詳細**
--- | ---
**評価の上限**| 1 つの[プロジェクト](migrate-support-matrix.md#azure-migrate-projects)で最大 35,000 個の物理サーバーを検出して評価します。
**プロジェクトの制限** | 1 つの Azure サブスクリプションで複数のプロジェクトを作成できます。 評価の上限に達するまで、1 つのプロジェクトに VMware VM、Hyper-V VM、および物理サーバーを含めることができます。
**検出** | Azure Migrate アプライアンスでは、最大 250 台の物理サーバーを検出できます。
**評価** | 1 つのグループに最大 35,000 個のマシンを追加できます。<br/><br/> 1 回の評価で最大 35,000 個のマシンを評価できます。

評価の詳細については[こちら](concepts-assessment-calculation.md)をご覧ください。




## <a name="physical-server-requirements"></a>物理サーバーの要件

| **サポート**                | **詳細**               
| :-------------------       | :------------------- |
| **物理サーバーの展開**       | 物理サーバーは、スタンドアロンにすることも、クラスターにデプロイすることもできます。 |
| **アクセス許可**           | **Windows:** 検出に含めるすべての Windows サーバー上にローカルまたはドメイン ユーザー アカウントを設定します。 ユーザー アカウントは、これらのグループ (リモート デスクトップ ユーザー、パフォーマンス モニター ユーザー、パフォーマンス ログ ユーザー) に追加する必要があります。 <br/> **Linux:** 検出する Linux サーバーのルート アカウントが必要です。 |
| **オペレーティング システム** | 次を除くすべての [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) および [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) オペレーティング システムがサポートされています。<br/> Windows Server 2003 <br/> SUSE Linux|


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate アプライアンスの要件

Azure Migrate では、[Azure Migrate アプライアンス](migrate-appliance.md)を使用して検出と評価を行います。 物理サーバーのアプライアンスは、VM または物理マシン上で実行できます。 これは、Azure portal からダウンロードした PowerShell スクリプトを使用して設定します。

- 物理サーバーの[アプライアンスの要件](migrate-appliance.md#appliance---physical)を確認します。
- アプライアンスがアクセスする必要のある [URL](migrate-appliance.md#url-access) を確認します。

## <a name="port-access"></a>ポート アクセス

次の表は、評価のためのポート要件をまとめたものです。

**[デバイス]** | **[接続]**
--- | ---
**アプライアンス** | TCP ポート 3389 で、アプライアンスへのリモート デスクトップ接続を許可するための受信接続。<br/> ポート 44368 で、次の URL を使用してアプライアンス管理アプリにリモートでアクセスするためのインバウンド接続: ``` https://<appliance-ip-or-name>:44368 ```<br/> ポート 443 (HTTPS)、5671、5672 (AMQP) で検出とパフォーマンスのメタデータを Azure Migrate に送信するためのアウトバウンド接続。
**物理サーバー** | **Windows:** WinRM ポート 5985 (HTTP) および 5986 (HTTPS) で Windows サーバーから構成とパフォーマンスのメタデータをプルするためのインバウンド接続。 <br/> **Linux:** ポート 22 (UDP) で Linux サーバーから構成とパフォーマンスのメタデータをプルするための受信接続。 |

## <a name="agent-based-dependency-visualization"></a>エージェントベースの依存関係の視覚化

[依存関係の視覚化](concepts-dependency-visualization.md)は、評価および移行するマシン間の依存関係を視覚化するのに役立ちます。 エージェントベースの視覚化に関する要件と制限事項は、以下の表のとおりです。


**要件** | **詳細**
--- | ---
**デプロイ** | 依存関係の視覚化をデプロイする前に、Azure Migrate:Server Assessment ツールプロジェクトに追加して、Azure Migrate プロジェクトを配置する必要があります。 オンプレミスのマシンを検出するには、Azure Migrate アプライアンスをセットアップした後、依存関係の視覚化をデプロイします。<br/><br/> 依存関係の視覚化は、Azure Government では使用できません。
**サービス マップ** | エージェントベースの依存関係の視覚化では、[Azure Monitor ログ[で ](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map)Service Map](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) ソリューションが使用されます。<br/><br/> デプロイするには、新規または既存の Log Analytics ワークスペースを Azure Migrate プロジェクトに関連付けます。
**Log Analytics ワークスペース** | このワークスペースは、Azure Migrate プロジェクトと同じサブスクリプションに含まれている必要があります。<br/><br/> Azure Migrate では、米国東部、東南アジア、および西ヨーロッパの各リージョンにあるワークスペースがサポートされます。<br/><br/>  ワークスペースは、[Service Map がサポートされている](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)リージョンに存在する必要があります。<br/><br/> Azure Migrate プロジェクトのワークスペースは、追加後に変更できません。
**料金** | Service Map ソリューションでは、(Log Analytics ワークスペースを Azure Migrate プロジェクトに関連付けた日から) 最初の 180 日間は料金が発生しません。<br/><br/> 180 日が経過すると、Log Analytics の標準の料金が適用されます。<br/><br/> この関連付けられた Log Analytics ワークスペース内で Service Map 以外のソリューションを使用すると、標準の Log Analytics 料金が発生します。<br/><br/> Azure Migrate プロジェクトを削除しても、ワークスペースが一緒に削除されることはありません。 プロジェクトの削除後、Service Map は無料にならず、Log Analytics ワークスペースの有料レベルに応じて各ノードの料金が請求されます。
**[エージェント]** | エージェントベースの依存関係の視覚化では、分析する各マシンに 2 つのエージェントをインストールする必要があります。<br/><br/> - [Microsoft Monitoring agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> - [依存関係エージェント](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent)。 
**インターネット接続** | マシンがインターネットに接続されていない場合は、それらに Log Analytics ゲートウェイをインストールする必要があります。

## <a name="next-steps"></a>次のステップ

[物理サーバーの評価を準備します](tutorial-prepare-physical.md)。
