---
title: Azure Migrate での依存関係の視覚化
description: Azure Migrate の Server Assessment Service における評価の計算の概要を説明します
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 65a99e230262ae05d34dc8c04e87252c15133fda
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425682"
---
# <a name="dependency-visualization"></a>依存関係の視覚化

この記事では、Azure Migrate の依存関係の視覚化機能について説明します。Server Assessment を使用して作成する方法について説明します。

依存関係の視覚化は、評価および移行するマシン間の依存関係を理解するのに役立ちます。 一般的に依存関係マッピングは、より高い信頼度でマシンを評価したい場合に使用します。

- Azure Migrate:Server Assessment では、評価のためにマシンを集めてグループ化します。 通常、グループは、一緒に移行するマシンで構成されます。依存関係の視覚化は、マシンの依存関係を照合するのに役立つため、マシンを正確にグループ化できます。
- 視覚化を使用することで、一緒に移行する必要がある相互依存システムを検出できます。 実行中のシステムがまだ使用中であるかどうか、またはシステムを移行せずに使用停止できるかどうかを識別できます。
- 依存関係を視覚化することにより、残されたものを確実になくして、移行中の突然の停止を回避することができます。
- この機能は、アプリの一部であるために Azure にまとめて移行する必要があるマシンを完全に認識していない場合に特に便利です。


> [!NOTE]
> 依存関係可視化機能は、Azure Government では使用できません。

## <a name="agent-based-and-agentless"></a>エージェントベースとエージェントレス

依存関係の視覚化をデプロイするには、次の 2 つのオプションがあります。

- **エージェントレスの依存関係の視覚化**:このオプションは現在プレビュー段階であり、VMware VM でのみ使用できます。 マシンにエージェントをインストールする必要はありません。 
    - これは、エージェントレスの視覚化が有効になっているマシンから TCP 接続データをキャプチャすることで機能します。 [詳細については、こちらを参照してください](how-to-create-group-machine-dependencies-agentless.md)。
依存関係の検出が開始された後、アプライアンスでは 5 分間のポーリング間隔でマシンからデータが収集されます。
    - 収集されるデータは次のとおりです。
        - TCP 接続
        - アクティブな接続を含むプロセスの名前
        - 上記のプロセスを実行するインストール済みのアプリケーションの名前
        - いいえ。 ポーリング間隔ごとに検出される接続の数
- **エージェントベースの依存関係の視覚化**:エージェントベースの依存関係の視覚化を使用するには、分析するオンプレミスの各マシンに次のエージェントをダウンロードしてインストールする必要があります。  
    - 各コンピューターに [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) をインストールする必要があります。 MMA エージェントのインストール方法の詳細については、[こちら](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-mma)をご覧ください。
    - 各マシンに [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent) をインストールする必要があります。 依存関係エージェントのインストール方法の詳細については、[こちら](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-dependency-agent)をご覧ください。
    - また、インターネットに接続されていないマシンの場合、それらに Log Analytics ゲートウェイをダウンロードしてインストールする必要があります。

## <a name="agent-based-requirements"></a>エージェントベースの要件

### <a name="what-do-i-need-to-deploy-dependency-visualization"></a>依存関係の視覚化をデプロイするには何が必要ですか?

依存関係の視覚化をデプロイする前に、Azure Migrate:Server Assessment ツールプロジェクトに追加して、Azure Migrate プロジェクトを配置する必要があります。 オンプレミスのマシンを検出するには、Azure Migrate アプライアンスをセットアップした後、依存関係の視覚化をデプロイします。

ツールを追加して、[Hyper-V](how-to-set-up-appliance-hyper-v.md)、[VMware](how-to-set-up-appliance-vmware.md)、または物理サーバーのアプライアンスをデプロイする方法の[詳細](how-to-assess.md)を参照してください。


### <a name="how-does-it-work"></a>それはどのように機能しますか?

Azure Migrate は、依存関係の視覚化のために [Azure Monitor ログ](../log-analytics/log-analytics-overview.md)の [Service Map](../operations-management-suite/operations-management-suite-service-map.md) ソリューションを使用します。

- 依存関係の視覚化を利用するには、Log Analytics ワークスペース (新規または既存) を Azure Migrate プロジェクトに関連付ける必要があります。
- ワークスペースは、Azure Migrate プロジェクトを作成するサブスクリプションと同じサブスクリプションに含まれている必要があります。
- Azure Migrate では、米国東部、東南アジア、および西ヨーロッパの各リージョンにあるワークスペースがサポートされます。 他のリージョンにあるワークスペースをプロジェクトに関連付けることはできません。 また、ワークスペースは、[Service Map がサポートされている](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)リージョンにある必要があることにも注意してください。
- Azure Migrate プロジェクトのワークスペースは、追加後に変更できません。
- Log Analytics では、Azure Migrate に関連付けられたワークスペースは、移行プロジェクト キーとプロジェクト名のタグが付けられます。

    ![Log Analytics ワークスペースを操作する](./media/concepts-dependency-visualization/oms-workspace.png)



### <a name="do-i-need-to-pay-for-it"></a>使用料金が必要になる場合

依存関係の視覚化には、Service Map と、関連付けられた Log Analytics ワークスペースが必要です。 

- Service Map ソリューションには、最初の 180 日間料金が発生しません。 この期間は、Log Analytics ワークスペースを Azure Migrate プロジェクトに関連付けた日から起算されます。
- 180 日が経過すると、Log Analytics の標準の料金が適用されます。
- この関連付けられた Log Analytics ワークスペース内で Service Map 以外のソリューションを使用すると、[標準の Log Analytics ](https://azure.microsoft.com/pricing/details/log-analytics/)料金が発生します。
- Azure Migrate プロジェクトが削除される際、それに伴ってワークスペースが削除されることはありません。 プロジェクトが削除された後は、Service Map を無料で使用することはできず、Log Analytics ワークスペースの有料階層に応じて各ノードの料金が請求されます。

Azure Migrate の価格については、[こちら](https://azure.microsoft.com/pricing/details/azure-migrate/)を参照してください。

> [!NOTE]
> Azure Migrate が 2018 年 2 月 28 日に一般提供される前に作成したプロジェクトがある場合、Service Map の追加料金が発生する可能性があります。 一般提供前に既にあるワークスペースはそのまま課金対象となるため、支払が確実に 180 日経過した後からのみ発生するように、新しいプロジェクトを作成することをお勧めします。



### <a name="how-do-i-manage-the-workspace"></a>ワークスペースの管理方法

- エージェントをワークスペースに登録する場合、Azure Migrate プロジェクトで提供される ID とキーを使用します。
- Azure Migrate 以外で Log Analytics ワークスペースを使用できます。
- 関連付けられた Azure Migrate プロジェクトを削除しても、ワークスペースは自動的に削除されません。 そのため、[手動で削除する](../azure-monitor/platform/manage-access.md)必要があります。
- Azure Migrate プロジェクトを削除しない場合は、Azure Migrate で作成されたワークスペースは削除しないでください。 削除した場合は、依存関係可視化機能は、期待どおりに機能しません。

## <a name="next-steps"></a>次のステップ
- [マシンの依存関係マッピングを使用したマシンのグループ化](how-to-create-group-machine-dependencies.md)
- [依存関係の可視化の詳細については](common-questions-discovery-assessment.md#what-is-dependency-visualization)、よく寄せられる質問を確認します。


