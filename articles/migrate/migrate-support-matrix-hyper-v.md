---
title: Azure Migrate での Hyper-V の評価のサポート
description: Azure Migrate を使用した Hyper-V の評価のサポートについて説明します。
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 1a036e2f22bb1fd9dac65a3cc643224ecbea3c69
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2020
ms.locfileid: "76154807"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Hyper-V の評価のサポート マトリックス

この記事では、[Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) を使用して Hyper-V VM を評価するためのサポート設定と制限事項についてまとめています。 Hyper-V VM を Azure に移行する方法については、[移行のサポート マトリックス](migrate-support-matrix-hyper-v-migration.md)に関するページを参照してください。

## <a name="overview"></a>概要

この記事に従って Azure への移行についてオンプレミスのマシンを評価するには、Azure Migrate: Server Assessment ツールを Azure Migrate プロジェクトに追加します。 [Azure Migrate アプライアンス](migrate-appliance.md)をデプロイします。 アプライアンスは、オンプレミス マシンを継続的に検出し、構成データとパフォーマンスデータを Azure に送信します。 マシンの検出後、検出されたマシンをグループにまとめ、グループの評価を実行します。


## <a name="limitations"></a>制限事項

**サポート** | **詳細**
--- | ---
**評価の上限**| 1 つの[プロジェクト](migrate-support-matrix.md#azure-migrate-projects)で最大 35,000 台の Hyper-V VM を検出および評価します。
**プロジェクトの制限** | 1 つの Azure サブスクリプションで複数のプロジェクトを作成できます。 評価の上限に達するまで、1 つのプロジェクトに VMware VM、Hyper-V VM、および物理サーバーを含めることができます。
**検出** | Azure Migrate アプライアンスでは、最大 5,000 台の Hyper-V VM を検出できます。<br/><br/> アプライアンスは、最大 300 台の Hyper-V ホストに接続できます。
**評価** | 1 つのグループに最大 35,000 個のマシンを追加できます。<br/><br/> 1 回の評価で最大 35,000 個の VM を評価できます。

評価の詳細については[こちら](concepts-assessment-calculation.md)をご覧ください。



## <a name="hyper-v-host-requirements"></a>Hyper-V ホストの要件

| **サポート**                | **詳細**               
| :-------------------       | :------------------- |
| **ホストのデプロイ**       | Hyper-V ホストは、スタンドアロンにすることも、クラスターにデプロイすることもできます。 |
| **アクセス許可**           | Hyper-V ホストに対する管理者のアクセス許可が必要です。 <br/> 管理者のアクセス許可を割り当てたくない場合には、代わりにローカルまたはドメインのユーザー アカウントを作成し、そのユーザーを Remote Management Users、Hyper-V Administrators、Performance Monitor Users のグループに追加します。 |
| **ホスト オペレーティング システム** | Windows Server 2019、Windows Server 2016、または Windows Server 2012 R2。<br/> Windows Server 2012 を実行している Hyper-V ホスト上にある VM を評価することはできません。 |
| **PowerShell リモート処理**   | 各ホストで有効にする必要があります。 |
| **Hyper-V レプリカ**       | Hyper-V レプリカを使用する (または、同じ VM 識別子を持つ複数の VM がある) 場合、Azure Migrate を使用して元の VM とレプリケートされた VM の両方を検出すると、Azure Migrate によって生成される評価が正確ではない可能性があります。 |


## <a name="hyper-v-vm-requirements"></a>Hyper-V VM の要件

| **サポート**                  | **詳細**               
| :----------------------------- | :------------------- |
| **オペレーティング システム** | Azure でサポートされているすべての [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) および [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) オペレーティング システム。 |
| **統合サービス**       | オペレーティング システム情報をキャプチャするには、評価する VM で [Hyper-V 統合サービス](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services)が実行されている必要があります。 |


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate アプライアンスの要件

Azure Migrate では、[Azure Migrate アプライアンス](migrate-appliance.md)を使用して検出と評価を行います。 Hyper-V 用のアプライアンスは、Hyper-V VM 上で実行され、Azure portal からダウンロードした圧縮された Hyper-V VHD を使用してデプロイされます。 

- Hyper-V の[アプライアンスの要件](migrate-appliance.md#appliance---hyper-v)を確認する。
- アプライアンスがアクセスする必要のある [URL](migrate-appliance.md#url-access) を確認する。

## <a name="port-access"></a>ポート アクセス

次の表は、評価のためのポート要件をまとめたものです。

**[デバイス]** | **[接続]**
--- | ---
**アプライアンス** | TCP ポート 3389 で、アプライアンスへのリモート デスクトップ接続を許可するための受信接続。<br/> ポート 44368 で、次の URL を使用してアプライアンス管理アプリにリモートでアクセスするためのインバウンド接続: ``` https://<appliance-ip-or-name>:44368 ```<br/> ポート 443、5671、5672 で検出とパフォーマンスのメタデータを Azure Migrate に送信するためのアウトバウンド接続。
**Hyper-V ホスト/クラスター** | Common Information Model (CIM) セッションを使用し、WinRM ポート 5985 (HTTP) と 5986 (HTTPS) で、Hyper-V VM の構成とパフォーマンスのメタデータをプルするための受信接続。

## <a name="agent-based-dependency-visualization"></a>エージェントベースの依存関係の視覚化

[依存関係の視覚化](concepts-dependency-visualization.md)は、評価および移行するマシン間の依存関係を視覚化するのに役立ちます。 エージェントベースの視覚化に関する要件と制限は、以下の表の通りです


**要件** | **詳細**
--- | ---
**デプロイ** | 依存関係の視覚化をデプロイする前に、Azure Migrate:Server Assessment ツールプロジェクトに追加して、Azure Migrate プロジェクトを配置する必要があります。 オンプレミスのマシンを検出するには、Azure Migrate アプライアンスをセットアップした後、依存関係の視覚化をデプロイします。<br/><br/> 依存関係の視覚化は、Azure Government では使用できません。
**サービス マップ** | エージェントベースの依存関係の視覚化では、[Azure Monitor ログ[で ](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map)Service Map](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) ソリューションが使用されます。<br/><br/> デプロイするには、新規または既存の Log Analytics ワークスペースを Azure Migrate プロジェクトに関連付けます。
**Log Analytics ワークスペース** | ワークスペースは、Azure Migrate プロジェクトのサブスクリプションと同じサブスクリプションに含まれている必要があります。<br/><br/> Azure Migrate では、米国東部、東南アジア、および西ヨーロッパの各リージョンにあるワークスペースがサポートされます。<br/><br/>  また、ワークスペースは、[Service Map がサポートされている](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)リージョンにある必要があります。<br/><br/> Azure Migrate プロジェクトのワークスペースは、追加後に変更できません。
**料金** | Service Map ソリューションでは、最初の 180 日 (Log Analytics ワークスペースを Azure Migrate プロジェクトに関連付けた日から) に対して料金は発生しません。<br/><br/> 180 日が経過すると、Log Analytics の標準の料金が適用されます。<br/><br/> この関連付けられた Log Analytics ワークスペース内で Service Map 以外のソリューションを使用すると、標準の Log Analytics 料金が発生します。<br/><br/> Azure Migrate プロジェクトを削除しても、ワークスペースが一緒に削除されることはありません。 プロジェクトが削除された後は、Service Map を無料で使用することはできず、Log Analytics ワークスペースの有料階層に応じて各ノードの料金が請求されます。
**[エージェント]** | エージェントベースの依存関係の視覚化では、分析する各マシンに 2 つのエージェントをインストールする必要があります。<br/><br/> - [Microsoft Monitoring agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> - [依存関係エージェント](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent)。 
**インターネット接続** | コンピューターがインターネットに接続されていない場合は、そのコンピューターに Log Analytics ゲートウェイをインストールする必要があります。


## <a name="next-steps"></a>次のステップ

[Hyper-V VM の評価を準備する](tutorial-prepare-hyper-v.md)
