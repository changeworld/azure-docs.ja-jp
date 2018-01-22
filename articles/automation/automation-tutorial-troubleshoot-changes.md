---
title: "Azure 仮想マシンの変更に関する問題を解決する | Microsoft Docs"
description: "変更履歴を使用して、Azure 仮想マシンの変更に関する問題を解決します。"
services: automation
keywords: "変更, 追跡, オートメーション"
author: jennyhunter-msft
ms.author: jehunte
ms.date: 12/14/2017
ms.topic: tutorial
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 0aefa175d676bd7e98841d3a1e9ff5a8c90b7deb
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2018
---
# <a name="troubleshoot-changes-in-your-environment"></a>環境の変更に関する問題を解決する

このチュートリアルでは、Azure 仮想マシンの変更に関する問題を解決する方法について説明します。 変更履歴を有効にして、コンピューター上のソフトウェア、ファイル、Linux デーモン、Windows サービス、Windows レジストリ キーの変更を追跡することができます。
このような構成の変更を識別することで、環境全体の運用上の問題を特定できるようになります。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 変更履歴とインベントリのために VM をオンボードする
> * 停止したサービスの変更ログを検索する
> * 変更の追跡を構成する
> * アクティビティ ログの接続を有効にする
> * イベントをトリガーする
> * 変更を表示する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

* Azure サブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* 監視およびアクションの Runbook と監視タスクを保持する、[Automation アカウント](automation-offering-get-started.md)。
* オンボードする[仮想マシン](../virtual-machines/windows/quick-create-portal.md)。

## <a name="log-in-to-azure"></a>Azure にログインする

Azure Portal (http://portal.azure.com) にログインします。

## <a name="enable-change-tracking-and-inventory"></a>変更履歴とインベントリを有効にする

このチュートリアルでは、まず VM の変更履歴とインベントリを有効にする必要があります。 VM の別の Automation ソリューションで有効にした場合、この手順は不要です。

1. 左側のメニューで **[仮想マシン]** を選択し､一覧から VM を選択します。
1. 左側のメニューの **[操作]** セクションで、**[インベントリ]** をクリックします。 **[変更履歴とインベントリを有効化]** ページが開きます。

この VM で変更履歴とインベントリが有効になっているかどうかを確認する検証が行われます。
この検証では、Log Analytics ワークスペースの確認、リンクされた Automation アカウントの確認、ソリューションがワークスペースにあるかどうかの確認が行われます。

[Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) ワークスペースは、インベントリのような機能およびサービスによって生成されるデータを収集するために使用されます。
ワークスペースには、複数のソースからのデータを確認および分析する場所が 1 つ用意されています。

また、検証プロセスでは、VM が Microsoft Monitoring Agent (MMA) とハイブリッド worker でプロビジョニングされているかどうかが確認されます。
このエージェントは VM との通信に使用され、インストールされているソフトウェアに関する情報を取得します。
また、検証プロセスでは、VM が Microsoft Monitoring Agent (MMA) と Automation ハイブリッド Runbook worker でプロビジョニングされているかどうかが確認されます。

これらの前提条件が満たされていない場合、バナーが表示され、ソリューションを有効にするオプションを選択できます。

![変更履歴とインベントリのオンボード構成バナー](./media/automation-tutorial-troubleshoot-changes/enableinventory.png)

ソリューションを有効にするには、バナーをクリックします。
検証後、次の前提条件のいずれかを満たしていないことがわかった場合は、自動的に追加されます。

* [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) ワークスペース
* [Automation](./automation-offering-get-started.md)
* [Hybrid Runbook Worker](./automation-hybrid-runbook-worker.md) が VM で有効になっている

**[変更履歴とインベントリ]** 画面が開きます。 使用する場所、Log Analytics ワークスペース、Automation アカウントを構成し、**[有効にする]** をクリックします。 フィールドが淡色表示されている場合は、その VM で別の Automation ソリューションが有効になっているため、同じワークスペースと Automation アカウントを使用する必要があることを示します。

![変更履歴ソリューションを有効にするウィンドウ](./media/automation-tutorial-troubleshoot-changes/installed-software-enable.png)

ソリューションを有効にするには最大 15 分かかります。 この処理中はブラウザーのウィンドウは閉じないでください。
ソリューションが有効になると、VM にインストールされているソフトウェアと変更に関する情報が Log Analytics に送られます。
データの分析に使用できるようになるまでに、30 分から 6 時間かかる場合があります。


## <a name="using-change-tracking-in-log-analytics"></a>Log Analytics で変更履歴を使用する

変更の追跡は、Log Analytics に送信されるログ データを生成します。 クエリを実行してログを検索するには、**[変更の追跡]** ウィンドウの上部にある **[Log Analytics]** ウィンドウを選択します。
変更履歴データは、型 **ConfigurationChange** に格納されます。 次のサンプル Log Analytics クエリは、停止しているすべての Windows サービスを返します。

```
ConfigurationChange
| where ConfigChangeType == "WindowsServices" and SvcState == "Stopped"
```

Log Analytics でのログ ファイルの実行と検索については、[Azure Log Analytics ](https://docs.loganalytics.io/index) に関するページを参照してください。

## <a name="configure-change-tracking"></a>変更履歴を構成する

変更履歴を使用すると、VM の構成の変更を追跡できます。 次にレジストリ キーとファイルの追跡を構成する手順について説明します。
 
収集および追跡するファイルとレジストリ キーを選択するには、**[変更履歴]** ページの上部にある **[設定の編集]** を選択します。

> [!NOTE]
> インベントリと変更履歴で同じ収集設定を使用し、ワークスペース レベルの構成を設定します。

**[ワークスペースの構成]** ウィンドウでは、次の 3 つのセクションで説明するように、追跡する Windows レジストリ キー、Windows ファイル、または Linux ファイルを追加します。

### <a name="add-a-windows-registry-key"></a>Windows レジストリ キーを追加する

1. **[Windows レジストリ]** タブで **[追加]** を選択します。
    **[変更履歴用の Windows レジストリを追加する]** ウィンドウが開きます。

   ![変更履歴のレジストリの追加](./media/automation-vm-change-tracking/change-add-registry.png)

2. **[有効化]** で **[True]** を選択します。
3. **[項目名]** ボックスに、フレンドリ名を入力します。
4. (省略可能) **[グループ]** ボックスに、グループ名を入力します。
5. **[Windows レジストリ キー]** ボックスに、追跡するレジストリ キーの名前を入力します。
6. **[保存]** を選択します。

### <a name="add-a-windows-file"></a>Windows ファイルを追加する

1. **[Windows ファイル]** タブで **[追加]** を選択します。 **[変更履歴用の Windows ファイルを追加する]** ウィンドウが開きます。

   ![変更履歴の Windows ファイルの追加](./media/automation-vm-change-tracking/change-add-win-file.png)

2. **[有効化]** で **[True]** を選択します。
3. **[項目名]** ボックスに、フレンドリ名を入力します。
4. (省略可能) **[グループ]** ボックスに、グループ名を入力します。
5. **[パスの入力]** ボックスに、追跡するファイルの完全なパスとファイル名を入力します。
6. **[保存]** を選択します。

### <a name="add-a-linux-file"></a>Linux ファイルを追加する

1. **[Linux ファイル]** タブで **[追加]** を選択します。 **[変更履歴用の Linux ファイルを追加する]** ウィンドウが開きます。

   ![変更履歴の Linux ファイルの追加](./media/automation-vm-change-tracking/change-add-linux-file.png)

2. **[有効化]** で **[True]** を選択します。
3. **[項目名]** ボックスに、フレンドリ名を入力します。
4. (省略可能) **[グループ]** ボックスに、グループ名を入力します。
5. **[パスの入力]** ボックスに、追跡するファイルの完全なパスとファイル名を入力します。
6. **[パスの種類]** ボックスで、**[ファイル]** または **[ディレクトリ]** を選択します。
7. **[再帰]** で、指定したパスとその下にあるすべてのファイルとパスの変更を追跡するには、**[オン]** を選択します。 選択したパスまたはファイルのみを追跡するには、**[オフ]** を選択します。
8. **[Sudo の使用]** で、`sudo` コマンドを使用してアクセスする必要があるファイルを追跡するには、で、**[オン]** を選択します。 それ以外の場合は、**[オフ]** を選択します。
9. **[保存]** を選択します。

## <a name="enable-activity-log-connection"></a>アクティビティ ログの接続を有効にする

VM の **[変更履歴]** ページから **[アクティビティ ログ接続の管理]** を選択します。 このタスクで、**[Azure アクティビティ ログ]** ページが開きます。 **[接続]** を選択して、変更履歴を VM の Azure アクティビティ ログに接続します。

この設定を有効にして、VM の **[概要]** ページに移動し、**[停止]** を選択して VM を停止します。 メッセージが表示されたら、**[はい]** を選択して VM を停止します。 割り当てが解除されたら、**[スタート]** を選択して VM を再起動します。

VM の起動時と停止時には、イベントがアクティビティ ログに記録されます。 **[変更履歴]** ページに戻ります。 ページの下部にある **[イベント]** タブを選択します。 しばらくすると、グラフと表にイベントが表示されます。 前の手順と同様に、各イベントを選択してイベントの詳細情報を表示することができます。

![ポータルで変更の詳細を表示する](./media/automation-tutorial-troubleshoot-changes/viewevents.png)

## <a name="view-changes"></a>変更を表示する

変更履歴とインベントリ ソリューションが有効になると、**[変更履歴]** ページで結果を表示できます。

VM 内から **[操作]** の **[変更履歴]** を選択します。

![VM に加えられた変更の一覧を表示するスクリーン ショット](./media/automation-tutorial-troubleshoot-changes/change-tracking-list.png)

グラフには、時間の経過とともに発生した変更が表示されます。
アクティビティ ログ接続を追加すると、上部の折れ線グラフに Azure アクティビティ ログ イベントが表示されます。
棒グラフの各行は、さまざまな追跡可能な変更の種類を表します。
具体的には、Linux デーモン、ファイル、Windows レジストリ キー、ソフトウェア、および Windows サービスです。
[変更] タブには、変更が発生した時刻の降順 (最新のものから順に) で、変更の詳細が図示されます。
**[イベント]** タブの表には、接続されたアクティビティ ログ イベントとそれに対応する詳細が最新の情報から表示されます。

結果を見ると、サービスとソフトウェアの変更を含め、システムに複数の変更があったことがわかります。 ページ上部にあるフィルターを使用し、**[型の変更]** または期間を指定して結果を絞り込むことができます。

**WindowsServices** の変更を選択すると、**[変更の詳細]** ウィンドウが開きます。 [変更の詳細] ウィンドウには、変更の詳細と、変更前の値と変更後の値が表示されます。 この例では、Software Protection サービスが停止されました。

![ポータルで変更の詳細を表示する](./media/automation-tutorial-troubleshoot-changes/change-details.png)

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * 変更履歴とインベントリのために VM をオンボードする
> * 停止したサービスの変更ログを検索する
> * 変更の追跡を構成する
> * アクティビティ ログの接続を有効にする
> * イベントをトリガーする
> * 変更を表示する

さらに詳しく学ぶには、変更履歴とインベントリ ソリューションの概要に進んでください。

> [!div class="nextstepaction"]
> [変更管理とインベントリ ソリューション](../log-analytics/log-analytics-change-tracking.md?toc=%2fazure%2fautomation%2ftoc.json)
