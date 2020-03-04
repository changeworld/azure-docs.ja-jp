---
title: Azure Migrate での依存関係の視覚化
description: Azure Migrate の Server Assessment Service における評価の計算の概要を説明します
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: f24656d02e19f422ff26e6b06d1631a9128dff43
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587109"
---
# <a name="dependency-visualization"></a>依存関係の視覚化

この記事では Azure Migrate:Server Assessment における依存関係の視覚化について説明します。

## <a name="what-is-dependency-visualization"></a>依存関係の視覚化とは何ですか。

依存関係の視覚化を使用すると、評価して Azure に移行するオンプレミスのマシン間の依存関係を特定できます。 

- Azure Migrate:Server Assessment では、複数のマシンを 1 つのグループにまとめ、そのグループを評価します。 依存関係の視覚化は、コンピューターをより正確にグループ化し、評価の信頼性を高めるために役立ちます。
- 依存関係の視覚化を使用すると、一緒に移行する必要があるマシンを特定できます。 コンピューターが使用中かどうか、または移行せずに使用を停止できるかどうかを特定できます。
- 依存関係を視覚化することにより、残されたものを確実になくして、移行中の突然の停止を回避することができます。
- 視覚化は、マシンが Azure に移行するアプリのデプロイの一部であるかどうかわからない場合に特に役立ちます。


> [!NOTE]
> 依存関係の視覚化は、Azure Government では使用できません。

## <a name="agent-basedagentless-visualization"></a>エージェントベースとエージェントレスの視覚化

依存関係の視覚化をデプロイするには、次の 2 つのオプションがあります。

- **エージェントベース**:エージェントベースの依存関係の視覚化では、分析するオンプレミスの各マシンにエージェントをインストールする必要があります。
- **エージェントレス**:このオプションを使用すると、クロスチェックを行うマシンにエージェントをインストールする必要はありません。 このオプションは現在プレビュー段階であり、VMware VM でのみ使用できます。


## <a name="agent-based-visualization"></a>エージェントベースの視覚化

**要件** | **詳細** | **詳細情報**
--- | --- | ---
**デプロイ前** | Azure Migrate を使用して、Azure Migrate プロジェクトを配置しておく必要があります。Server Assessment ツールプロジェクトに追加して、Azure Migrate プロジェクトを配置する必要があります。<br/><br/>  オンプレミスのマシンを検出するには、Azure Migrate アプライアンスをセットアップした後、依存関係の視覚化をデプロイします。 | 初めてプロジェクトを作成する方法については[こちら](create-manage-projects.md)を参照してください。<br/><br/> 既存のプロジェクトに評価ツールを追加方法については[こちら](how-to-assess.md)を参照してください。<br/><br/> [Hyper-V](how-to-set-up-appliance-hyper-v.md)、[VMware](how-to-set-up-appliance-vmware.md)、または物理サーバーの評価のために Azure Migrate アプライアンスを設定する方法について説明します。
**必要なエージェント** | 分析する各マシンに次のエージェントをインストールします。<br/><br/> [Microsoft Monitoring エージェント (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)。<br/><br/> [依存関係エージェント](../azure-monitor/platform/agents-overview.md#dependency-agent)。<br/><br/> オンプレミス マシンがインターネットに接続されていない場合、Log Analytics ゲートウェイをダウンロードしてインストールする必要があります。 | [依存関係エージェント](how-to-create-group-machine-dependencies.md#install-the-dependency-agent)と [MMA](how-to-create-group-machine-dependencies.md#install-the-mma) のインストールの詳細を確認してください。
**Log Analytics** | Azure Migrate は、依存関係の視覚化のために [Azure Monitor ログ](../log-analytics/log-analytics-overview.md)の [Service Map](../operations-management-suite/operations-management-suite-service-map.md) ソリューションを使用します。<br/><br/> 新規または既存の Log Analytics ワークスペースを Azure Migrate プロジェクトに関連付けます。 Azure Migrate プロジェクトのワークスペースは、追加後に変更できません。 <br/><br/> このワークスペースは、Azure Migrate プロジェクトと同じサブスクリプションに含まれている必要があります。<br/><br/> ワークスペースは、米国東部リージョン、東南アジア リージョン、または西ヨーロッパ リージョンに存在する必要があります。 他のリージョンにあるワークスペースをプロジェクトに関連付けることはできません。<br/><br/> ワークスペースは、[Service Map がサポートされている](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)リージョンに存在する必要があります。<br/><br/> Log Analytics では、Azure Migrate に関連付けられたワークスペースは、移行プロジェクト キーとプロジェクト名のタグが付けられます。
**コスト** | Service Map ソリューションでは、(Log Analytics ワークスペースを Azure Migrate プロジェクトに関連付けた日から) 最初の 180 日間は料金が発生しません。<br/><br/> 180 日が経過すると、Log Analytics の標準の料金が適用されます。<br/><br/> この関連付けられた Log Analytics ワークスペース内で Service Map 以外のソリューションを使用すると、Log Analytics の[標準の料金](https://azure.microsoft.com/pricing/details/log-analytics/)が発生します。<br/><br/> Azure Migrate プロジェクトが削除される際、それに伴ってワークスペースが削除されることはありません。 プロジェクトが削除された後は、Service Map を無料で使用することはできず、Log Analytics ワークスペースの有料階層に応じて各ノードの料金が請求されます。<br/><br/>Azure Migrate の一般提供 (GA - 2018 年 2 月 28 日) 前に作成したプロジェクトがある場合、Service Map の追加料金が発生する可能性があります。 GA の前の既存のワークスペースには引き続き課金されるため、180 日後にのみ支払いが発生するように、新しいプロジェクトを作成することをお勧めします。
**管理** | エージェントをワークスペースに登録する場合、Azure Migrate プロジェクトで提供される ID とキーを使用します。<br/><br/> Azure Migrate 以外で Log Analytics ワークスペースを使用できます。<br/><br/> 関連付けられた Azure Migrate プロジェクトを削除しても、ワークスペースは自動的に削除されません。 [手動で削除してください](../azure-monitor/platform/manage-access.md)。<br/><br/> Azure Migrate プロジェクトを削除しない場合は、Azure Migrate で作成されたワークスペースは削除しないでください。 削除した場合は、依存関係可視化機能は、期待どおりに機能しません。

## <a name="agentless-visualization"></a>エージェントレスの視覚化


**要件** | **詳細** | **詳細情報**
--- | --- | ---
**デプロイ前** | Azure Migrate を使用して、Azure Migrate プロジェクトを配置しておく必要があります。Server Assessment ツールプロジェクトに追加して、Azure Migrate プロジェクトを配置する必要があります。<br/><br/>  オンプレミスの VMWare マシンを検出するには、Azure Migrate アプライアンスをセットアップした後、依存関係の視覚化をデプロイします。 | 初めてプロジェクトを作成する方法については[こちら](create-manage-projects.md)を参照してください。<br/><br/> 既存のプロジェクトに評価ツールを追加方法については[こちら](how-to-assess.md)を参照してください。<br/><br/> [VMware](how-to-set-up-appliance-vmware.md) VM の評価用に Azure Migrate アプライアンスを設定する方法を参照してください。
**必要なエージェント** | 分析するマシンにエージェントは必要ありません。
**サポートされているオペレーティング システム** | エージェントレスの視覚化でサポートされている[オペレーティング システム](migrate-support-matrix-vmware.md#agentless-dependency-visualization)を確認します。
**VM** | **VMware ツール**:分析する VM に VMware ツールがインストールされ、実行されている必要があります。<br/><br/> **アカウント**:Azure Migrate アプライアンスでは、分析のために VM へのアクセスに使用できるユーザー アカウントを追加する必要があります。<br/><br/> **Windows VM**:ユーザー アカウントは、マシン上のローカル管理者またはドメイン管理者である必要があります。<br/><br/> **Linux VM**:アカウントには root 特権が必要です。 また、ユーザー アカウントには /bin/netstat ファイルと /bin/ls ファイルに対する次の 2 つの機能が必要です。CAP_DAC_READ_SEARCH と CAP_SYS_PTRACE。 | Azure Migrate アプライアンスの[詳細を確認](migrate-appliance.md)してください。
**VMware** | **vCenter**:アプライアンスには、読み取り専用アクセス権を持ち、[仮想マシン] > [ゲスト操作] の権限が有効な vCenter Server アカウントが必要です。<br/><br/> **ESXi ホスト**:分析する VM を実行している ESXi ホストでは、Azure Migrate アプライアンスが TCP ポート 443 に接続できる必要があります。
**収集されるデータ** |  エージェントレスの依存関係の視覚化は、有効なマシンから TCP 接続データをキャプチャすることで機能します。 依存関係の検出が開始されると、アプライアンスでは 5 分ごとにポーリングが行われ、マシンからこのデータが収集されます。<br/> - TCP 接続。<br/> - アクティブな接続を含むプロセスの名前。<br/> - アクティブな接続を使用してプロセスを実行する、インストールされているアプリケーションの名前。<br/> - ポーリング間隔ごとに検出される接続の数。


## <a name="next-steps"></a>次のステップ
- [依存関係の視覚化を設定する](how-to-create-group-machine-dependencies.md)
- VMware VM の[エージェントレスの依存関係の視覚化を試してみる](how-to-create-group-machine-dependencies-agentless.md)。
- 依存関係の視覚化について[よく寄せられる質問](common-questions-discovery-assessment.md#what-is-dependency-visualization)を確認する。


