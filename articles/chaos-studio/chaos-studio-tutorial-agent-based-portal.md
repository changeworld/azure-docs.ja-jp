---
title: ポータルの Azure Chaos Studio でエージェントベースの障害を使用する実験を作成する
description: ポータルでエージェントベースの障害を使用する実験を作成し、カオス エージェントを構成する
author: johnkemnetz
ms.topic: how-to
ms.date: 11/01/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: f83f43b92573aa575b432a7dd75e6f0ec060e475
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371364"
---
# <a name="create-a-chaos-experiment-that-uses-an-agent-based-fault-to-add-cpu-pressure-to-a-linux-vm-with-the-azure-portal"></a>Azure portal でエージェントベースの障害を使用して Linux VM に CPU 負荷を追加するカオス実験を作成する

カオス実験を使用すると、制御された環境でこれらの障害を発生させて、ご自分のアプリケーションが障害に対して回復力を持っていることを検証できます。 このガイドでは、カオス実験と Azure Chaos Studio を使用して、Linux 仮想マシンで高 CPU イベントを発生します。 この実験を実行することで、アプリケーションがリソース不足になることを防ぐことができます。

これらの同じ手順をエージェントベースのあらゆる障害に使用して、実験を設定し、実行することができます。 Azure リソースに対して直接実行し、インストルメンテーションを必要としないサービスダイレクト障害とは異なり、**エージェントベース** の障害には、カオス エージェントのセットアップとインストールが必要です。


## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- Linux 仮想マシン。 仮想マシンがない場合は、[次の手順に従って作成](../virtual-machines/linux/quick-create-portal.md)できます。
- [お使いの仮想マシンへの SSH 接続](../virtual-machines/ssh-keys-portal.md)を許可するネットワーク セットアップ
- ユーザー割り当てマネージド ID が **ターゲットの仮想マシンまたは仮想マシン スケール セットに割り当てられていること**。 ユーザー割り当てマネージド ID をお持ちではない場合は、[次の手順に従って作成](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md)できます


## <a name="enable-chaos-studio-on-your-virtual-machine"></a>仮想マシンで Chaos Studio を有効にする

最初に仮想マシンが Chaos Studio にオンボードされていない限り、Chaos Studio でその仮想マシンに対して障害を挿入することはできません。 仮想マシンを Chaos Studio にオンボードするには、リソースで[ターゲットと機能](chaos-studio-targets-capabilities.md)を作成してから、カオス エージェントをインストールします。 仮想マシン内のターゲットには、サービスダイレクト障害 (エージェントは不要) を可能にするものと、エージェントベース障害 (エージェントのインストールが必要) を可能にするものの 2 種類があります。 カオス エージェントとは、[仮想マシン拡張機能](../virtual-machines/extensions/overview.md)として仮想マシンにインストールするアプリケーションであり、これにより、ゲスト オペレーティング システムに障害を挿入することができます。

### <a name="install-stress-ng"></a>stress-ng をインストールする

Linux 用の Chaos Studio エージェントには、仮想マシン上でさまざまなストレス イベントを発生させることができるオープンソース アプリケーションの stress-ng が必要です。 stress-ng をインストールするには、[Linux 仮想マシンに接続](../virtual-machines/ssh-keys-portal.md)し、お使いのパッケージ マネージャーに適したインストール コマンドを実行します。次に例を示します。

```bash
sudo apt-get update && sudo apt-get -y install unzip && sudo apt-get -y install stress-ng
```

or

```bash
sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo yum -y install stress-ng
```

### <a name="enable-chaos-target-capabilities-and-agent"></a>カオス ターゲット、機能、エージェントを有効にする

> [!IMPORTANT]
> 以下の手順を完了する前に、[ユーザー割り当てマネージド ID を作成](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md)し、ターゲットの仮想マシンまたは仮想マシン スケール セットに割り当てる必要があります。

1. [Azure Portal](https://portal.azure.com)を開きます。
2. 検索バーで「**Chaos Studio (preview)** 」を検索します。
3. **[Targets]\(ターゲット\)** をクリックし、お使いの仮想マシンに移動します。
![Azure portal 内の [Targets]\(ターゲット\) ビュー](images/tutorial-agent-based-targets.png)
4. お使いの仮想マシンの横にあるチェック ボックスをオンにし、ドロップダウン メニューから **[Enable targets]\(ターゲットを有効にする\)** 、 **[Enable agent-based targets]\(エージェントベースのターゲットを有効にする\)** の順にクリックします。
![Azure portal でターゲットを有効にする](images/tutorial-agent-based-targets-enable.png)
5. カオス エージェントの認証に使用する **マネージド ID** を選択し、必要に応じて Application Insights を有効にして、実験イベントとエージェント ログを表示します。
![マネージド ID を選択する](images/tutorial-agent-based-targets-enable-options.png)
6. **[Review + Enable]\(確認と有効化\)** をクリックし、 **[Enable]\(有効化\)** をクリックします。
![エージェントベースのターゲットの有効化を確認する](images/tutorial-agent-based-targets-enable-review.png)
7. 数分後に、選択したリソースが正常に有効化されたことを示す通知が表示されます。 Azure portal で、ユーザー割り当て ID が仮想マシンに追加され、エージェントのターゲットと機能が有効にされ、カオス エージェントが仮想マシン拡張機能としてインストールされます。
![ターゲットが正常に有効化されたことを示す通知](images/tutorial-agent-based-targets-enable-confirm.png)

これで、お使いの Linux 仮想マシンが Chaos Studio に正常にオンボードされました。 **[Targets]\(ターゲット\)** ビューでは、このリソースで有効になっている機能を管理することもできます。 リソースの横にある **[アクションの管理]** リンクをクリックすると、そのリソースに対して有効になっている機能が表示されます。

## <a name="create-an-experiment"></a>実験の作成
仮想マシンがオンボードされ、実験を作成できるようになりました。 カオス実験には、ターゲット リソースに対して実行するアクションを、順番に実行するステップと、並行して実行する分岐に分けて定義します。

1. Chaos Studio ナビゲーションの **[実験]** タブをクリックします。 このビューでは、すべてのカオス実験を表示および管理できます。 **[実験の追加]** 
![Azure portal の実験ビュー](images/tutorial-agent-based-add.png) をクリックします
2. **[サブスクリプション]** 、 **[リソース グループ]** 、カオス実験をデプロイする **[場所]** に情報を入力します。 実験に **名前** を付けます。 **[次へ: 実験デザイナー >]** 
![基本的な実験の詳細の追加](images/tutorial-agent-based-add-basics.png) をクリックします
3. これで、Chaos Studio 実験デザイナーが表示されます。 実験デザイナーでは、ステップ、分岐、障害などを追加して実験を構築することができます。 **ステップ** と **分岐** にフレンドリ名を付け、 **[障害の追加]** をクリックします。
![実験デザイナー](images/tutorial-agent-based-add-designer.png)
4. ドロップダウンから **[CPU Pressure]\(CPU 負荷\)** を選択し、負荷を適用する分数を **[Duration]\(期間\)** に、適用する CPU 負荷の量を **[pressureLevel]** に入力します。 **virtualMachineScaleSetInstances** は空白のままにします。 **[Next: Target resources >]\(次へ: ターゲット リソース >\)** をクリックします
![障害プロパティ](images/tutorial-agent-based-add-fault.png)
5. お使いの仮想マシンを選択し、 **[次へ]** をクリックします
![ターゲットを追加する](images/tutorial-agent-based-add-targets.png)
6. 実験が正しく表示されていることを確認し、 **[Review + create]\(確認と作成\)** 、 **[作成]** の順にクリックします。
![実験を確認して作成する](images/tutorial-agent-based-add-review.png)

## <a name="give-experiment-permission-to-your-virtual-machine"></a>仮想マシンに実験アクセス許可を付与する
カオス実験を作成すると、ターゲット リソースに対して障害を実行するシステム割り当てマネージド ID が Chaos Studio によって作成されます。 実験を正常に実行するには、この ID にターゲット リソースへの[適切なアクセス許可](chaos-studio-fault-providers.md)を付与する必要があります。

1. お使いの仮想マシンに移動し、 **[アクセス制御 (IAM)]** をクリックします。
![仮想マシンの概要ページ](images/tutorial-agent-based-access-resource.png)
2. **[追加]** 、 **[ロールの割り当ての追加]** の順にクリックします。
![アクセス制御の概要](images/tutorial-agent-based-access-iam.png)
3. 「**閲覧者**」を検索し、このロールを選択します。 **[次へ]** をクリックします
![仮想マシン共同作成者ロールを割り当てる](images/tutorial-agent-based-access-role.png)
4. **[メンバーの選択]** をクリックし、実験名を検索します。 実験を選択し、 **[選択]** をクリックします。 同じテナント内に同じ名前の実験が複数ある場合、実験名はランダムな文字が追加されて切り詰められます。
![ロールへの実験の追加](images/tutorial-agent-based-access-experiment.png)
5. **[レビューと割り当て]** をクリックし、もう一度 **[レビューと割り当て]** をクリックします。

## <a name="run-your-experiment"></a>実験を実行する
これで、実験を実行する準備が整いました。 影響を確認するため、[Azure Monitor メトリック グラフ](../azure-monitor/essentials/tutorial-metrics.md)を別のブラウザー タブで開いて、お使いの仮想マシンの CPU 負荷を表示することをお勧めします。

1. **[実験]** ビューで、ご自分の実験をクリックし、 **[開始]** 、 **[OK]** の順にクリックします。
![実験を開始する](images/tutorial-agent-based-start.png)
2. **[状態]** が **[実行中]** に変わったら、 **[履歴]** の下にある最新の実行の **[詳細]** をクリックして、実行中の実験の詳細を確認します。

## <a name="next-steps"></a>次のステップ
エージェントベースの実験を実行したので、次の準備が整いました。
- [サービスダイレクト障害を使用する実験を作成する](chaos-studio-tutorial-service-direct-portal.md)
- [実験を管理する](chaos-studio-run-experiment.md)