---
title: チュートリアル - Azure 仮想マシンに対する監視を有効にする
description: Azure 仮想マシンを監視するには、Azure Monitor で VM insights を使用した監視を有効にします。
ms.service: azure-monitor
ms.topic: article
ms.custom: subject-monitoring
ms.date: 11/04/2021
ms.openlocfilehash: 4156685d707a6e4fd6319a7130750838434f1efa
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350055"
---
# <a name="tutorial-enable-monitoring-for-azure-virtual-machine"></a>チュートリアル: Azure 仮想マシンに対する監視を有効にする
Azure 仮想マシンの正常性とパフォーマンスを監視するには、そのゲスト オペレーティング システムからデータを収集するエージェントをインストールする必要があります。 VM insights とは Azure Monitor の機能であり、Azure 仮想マシン上で実行されているゲスト オペレーティング システムとワークロードを監視するためのものです。 Azure 仮想マシンに対する監視を有効にすると、必要なエージェントがインストールされ、ゲスト オペレーティング システムからのパフォーマンス、プロセス、および依存関係の情報の収集が開始されます。 

> [!NOTE]
> Azure Monitor を初めて使用する場合は、[チュートリアル: Azure Monitor を使用した Azure リソースの監視](../essentials/monitor-azure-resource.md)に関するページから始めてください。 Azure 仮想マシンでは、プラットフォーム メトリックやアクティビティ ログなどの他の Azure リソースと同様の監視データを生成します。 このチュートリアルでは、仮想マシンに固有の追加の監視を有効にする方法について説明します。

このチュートリアルでは、次の作業を行う方法について説明します。

> [!div class="checklist"]
> * 仮想マシンからパフォーマンスおよびログのデータを収集するための Log Analytics ワークスペースを作成する。
> * 必要なエージェントをインストールしてデータ収集を開始するため VM insights を仮想マシンに対して有効にする。 
> * 仮想マシンから収集されたパフォーマンス データを分析するグラフを検査する。 
> * 仮想マシン上で実行されているプロセスおよび他のシステムとの依存関係を示すマップを検査する。


> [!NOTE]
> VM insights によって、仮想マシンのゲスト オペレーティング システムからパフォーマンス データを収集する Log Analytics エージェントがインストールされます。 ゲスト オペレーティング システムからのログの収集、および Azure Monitor Metrics へのパフォーマンス データの送信が行われることはありません。 この機能については、「[チュートリアル: Azure 仮想マシンからゲスト ログとメトリックを収集する](tutorial-monitor-vm-guest.md)」を参照してください。

## <a name="prerequisites"></a>必須コンポーネント
このチュートリアルを完了するには、以下が必要です。 

- 監視する Azure 仮想マシン。



## <a name="create-a-log-analytics-workspace"></a>Log Analytics ワークスペースを作成する
[!INCLUDE [Create workspace](../../../includes/azure-monitor-tutorial-workspace.md)]


## <a name="enable-monitoring"></a>監視を有効にする
Azure portal で、ご利用の仮想マシンのメニューから **[Insights]** を選択します。 それに対して VM insights がまだ有効になっていない場合は、次のような画面が表示され、監視を有効にすることができます。 **[有効化]** をクリックします。

> [!NOTE]
> 仮想マシンの作成時に、 **[詳細な監視を有効]** オプションを選択した場合、VM insights は既に有効になっている可能性があります。 ご利用のワークスペースを選択し、もう一度 **[有効]** をクリックします。 これは、VM insights によって収集されたデータの送信先となるワークスペースです。

:::image type="content" source="media/tutorial-monitor-vm/enable-vminsights-02.png" lightbox="media/tutorial-monitor-vm/enable-vminsights-02.png" alt-text="ワークスペースに対して VM insights を有効にする":::

監視が有効になっていることを示すメッセージが表示されます。 エージェントがインストールされて、データ収集が開始されるまでには、数分かかる場合があります。 

> [!NOTE]
> VM insights にアップグレードを適用可能であることを知らせるメッセージが表示される場合があります。 その場合は、先に進む前に、アップグレードを実行するオプションを選択してください。

## <a name="view-performance"></a>パフォーマンスを表示する
デプロイが完了すると、VM insights の **[パフォーマンス]** タブに、該当するマシンのパフォーマンス データに関するビューが表示されます。 これにより、時間の経過に伴う主要なゲスト メトリックの値を確認することができます。 

:::image type="content" source="media/tutorial-monitor-vm/performance.png" lightbox="media/tutorial-monitor-vm/performance.png" alt-text="VM Insights のパフォーマンス ビュー":::

## <a name="view-processes-and-dependencies"></a>プロセスと依存関係を表示する
仮想マシンにおけるプロセスと依存関係を表示するには、 **[マップ]** タブを選択します。 現在のコンピューターがビューの中央に表示されます。 **[プロセス]** を展開すると、実行中のプロセスが表示されます。

:::image type="content" source="media/tutorial-monitor-vm/map-processes.png" lightbox="media/tutorial-monitor-vm/map-processes.png" alt-text="プロセスに関する VM insights マップ ビュー":::


## <a name="view-machine-details"></a>マシンの詳細を表示する
**[マップ]** ビューには、仮想マシンについて収集した情報を表示するさまざまなタブがあります。 各タブをクリックすると、利用可能な情報が表示されます。

:::image type="content" source="media/tutorial-monitor-vm/map-details.png" lightbox="media/tutorial-monitor-vm/map-details.png" alt-text="マシンの詳細が表示される VM insights のマップ ビュー":::

## <a name="next-steps"></a>次の手順
仮想マシンからデータが収集されるようになったので、そのデータを使用することにより、問題が検出されると事前に通知するアラートを作成できます。

> [!div class="nextstepaction"]
> [Azure 仮想マシンが使用できないときにアラートを作成する](tutorial-monitor-vm-alert.md)

