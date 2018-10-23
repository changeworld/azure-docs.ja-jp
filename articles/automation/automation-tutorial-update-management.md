---
title: Azure Windows VM の更新プログラムとパッチの管理
description: この記事では、Azure Automation Update Management を使用して、Azure Windows VM の更新プログラムとパッチを管理する方法の概要について説明します。
services: automation
author: zjalexander
ms.service: automation
ms.component: update-management
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: zachal
ms.custom: mvc
ms.openlocfilehash: bc1b52b97897cb1c635fb16e6ef9692de1ca8a44
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167258"
---
# <a name="manage-windows-updates-by-using-azure-automation"></a>Azure Automation を使用して Windows 更新プログラムを管理する

Update Management のソリューションを使用すると、仮想マシンの更新プログラムとパッチを管理できます。 このチュートリアルでは、利用可能な更新プログラムの状態の評価、必要な更新プログラムのインストールのスケジュール設定、展開の結果の確認、更新プログラムが正常に適用されたことを確認するアラートの作成を迅速に行う方法について説明します。

価格情報については、[Update Management の Automation の価格](https://azure.microsoft.com/pricing/details/automation/)に関するページをご覧ください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Update Management のために VM をオンボードする
> * 更新の評価を表示する
> * アラートを構成する
> * 更新プログラムのデプロイをスケジュールする
> * デプロイの結果を表示する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

* Azure サブスクリプション。 まだお持ちでない場合は、[Visual Studio サブスクライバー向けの月単位の Azure クレジットをアクティブにする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* 監視およびアクションの Runbook と監視タスクを保持する、[Azure Automation アカウント](automation-offering-get-started.md)。
* オンボードする[仮想マシン](../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="enable-update-management"></a>Update Management の有効化

まず、このチュートリアル用に VM の Update Management を有効にします。

1. Azure Portal の左側のメニューで **[Virtual Machines]** を選択します。 一覧から VM を選択します。
2. VM のページで、**[操作]**、**[更新の管理]** を選択します。 **[更新管理の有効化]** ウィンドウが開きます。

この VM で Update Management が有効になっているかどうかを確認する検証が行われます。 この検証では、Azure Log Analytics ワークスペースの確認、リンクされた Automation アカウントの確認、Update Management ソリューションがワークスペースにあるかどうかの確認が行われます。

[Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) ワークスペースは、Update Management のような機能およびサービスによって生成されるデータを収集するために使用されます。 ワークスペースには、複数のソースからのデータを確認および分析する場所が 1 つ用意されています。

また、検証プロセスでは、VM が Microsoft Monitoring Agent (MMA) と Automation Hybrid Runbook Worker と共にプロビジョニングされているかどうかが確認されます。 このエージェントは、Azure Automation と通信し、更新の状態に関する情報を取得するために使用されます。 エージェントでは、Azure Automation サービスと通信したり、更新プログラムをダウンロードしたりするために、ポート 443 を開く必要があります。

オンボード中に次の前提条件のいずれかを満たしていないことがわかった場合は、自動的に追加されます。

* [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) ワークスペース
* [Automation アカウント](./automation-offering-get-started.md)
* [Hybrid Runbook Worker](./automation-hybrid-runbook-worker.md) (VM 上で有効になっている)

**Update Management** で、使用する場所、Log Analytics ワークスペース、Automation アカウントを設定します。 その後、**[有効化]** を選択します。 これらのオプションを使用できない場合は、その VM で別の Automation ソリューションが有効になっていることを意味します。 この場合、同じワークスペースおよび Automation アカウントを使用する必要があります。

![[Update Management の有効化] ソリューション ウィンドウ](./media/automation-tutorial-update-management/manageupdates-update-enable.png)

ソリューションの有効化には、最大数分かかることがあります。 この処理中はブラウザーのウィンドウを閉じないでください。 ソリューションが有効になると、VM 上の不足している更新プログラムの情報が Log Analytics に送られます。 データの分析に使用できるようになるまでに、30 分から 6 時間かかる場合があります。

## <a name="view-update-assessment"></a>更新の評価を表示する

Update Management が有効になると、**[更新の管理]** ウィンドウが開きます。 不足している更新プログラムがある場合は、**[不足している更新プログラム]** タブに、足りない更新プログラムの一覧が表示されます。

**[情報リンク]** で更新プログラムのリンクを選択すると、新しいウィンドウに更新プログラムのサポート記事が表示されます。 このウィンドウで、更新プログラムに関する重要情報を確認できます。

![更新ステータスを確認する](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

更新プログラムの別の場所をクリックすると、選択した更新プログラムの **[ログ検索]** ウィンドウが開きます。 その更新プログラムについて、ログ検索のクエリが事前に定義されています。 このクエリを変更するか、独自のクエリを作成して、環境に展開されている更新プログラムまたは不足している更新プログラムに関する詳細情報を確認できます。

![更新ステータスを確認する](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>アラートを構成する

この手順では、Log Analytics クエリ経由または失敗したデプロイについて Update Management のマスター Runbook を追跡することで、更新プログラムが正常にデプロイされたタイミングを通知するアラートの設定方法について説明します。

### <a name="alert-conditions"></a>アラートの条件

アラートの種類ごとに、各種のアラートの条件が定義される必要があります。

#### <a name="log-analytics-query-alert"></a>Log Analytics クエリのアラート

正常なデプロイの場合、Log Analytics クエリに基づいてアラートを作成できます。 失敗したデプロイについては、[Runbook のアラート](#runbook-alert)の手順を使用して、オーケストレーターがデプロイを更新したマスター Runbook がエラーになったときに、アラートで通知できます。 さまざまなシナリオに対応するために、追加のアラート用のカスタム クエリを記述できます。

Azure portal 上で **[監視]** に移動し、**[アラートの作成]** を選択します。

**[1. アラートの条件を定義します]** で、**[ターゲットの選択]** をクリックします。 **[Filter by resource type]\(リソースの種類でフィルター処理\)** で **[Log Analytics]** を選択します。 Log Analytics ワークスペースを選択し、**[完了]** をクリックします。

![アラートを作成する](./media/automation-tutorial-update-management/create-alert.png)

**[条件の追加]** を選択します。

**[シグナル ロジックの構成]** の表で、**[カスタム ログ検索]** を選択します。 **[検索クエリ]** ボックスに次のクエリを入力します。

```loganalytics
UpdateRunProgress
| where InstallationStatus == 'Succeeded'
| where TimeGenerated > now(-10m)
| summarize by UpdateRunName, Computer
```
このクエリでは、コンピューターと、指定された時間枠内に完了した更新実行名が返されます。

**[アラート ロジック]** の **[しきい値]** に「**1**」を入力します。 完了したら、**[完了]** をクリックします。

![シグナル ロジックの構成](./media/automation-tutorial-update-management/signal-logic.png)

#### <a name="runbook-alert"></a>Runbook のアラート

失敗したデプロイについて、マスター Runbook のエラーをアラート通知する必要があります。
Azure portal 上で **[監視]** に移動し、**[アラートの作成]** を選択します。

**[1. アラートの条件を定義します]** で、**[ターゲットの選択]** をクリックします。 **[リソースの種類でフィルター]** で **[Automation アカウント]** を選びます。 お使いの Automation アカウントを選択して、**[完了]** を選択します。

**[Runbook 名]** で、**\+** 記号をクリックして、カスタム名として「**Patch-MicrosoftOMSComputers**」と入力します。 **[ステータス]** で、**[失敗]** を選択するか、または **\+** 記号を選択して「**失敗**」と入力します。

![Runbook のシグナル ロジックの構成](./media/automation-tutorial-update-management/signal-logic-runbook.png)

**[アラート ロジック]** の **[しきい値]** に「**1**」を入力します。 完了したら、**[完了]** をクリックします。

### <a name="alert-details"></a>[アラートの詳細]

**[2. アラートの詳細を定義します]** で、アラートの名前と説明を入力します。 成功した実行の場合 **[重大度]** を **[Informational(Sev 2)]\(情報 (重大度 2)\)** に設定し、失敗した実行の場合 **[Informational(Sev 1)]\(情報 (重大度 1)\)** に設定します。

![シグナル ロジックの構成](./media/automation-tutorial-update-management/define-alert-details.png)

**[3. アクション グループを定義します]** で、**[新しいアクション グループ]** を選択します。 アクション グループとは、複数のアラートで使用できるアクションのグループです。 アクションには、電子メール通知、Runbook、webhook などがありますが、これらに限定されるわけではありません。 アクション グループの詳細については、[アクション グループの作成および管理](../monitoring-and-diagnostics/monitoring-action-groups.md)に関するページを参照してください。

**[アクション グループ名]** ボックスに、アラートの名前と短い名前を入力します。 短い名前は、通知がこのグループを使用して送信されるときに長い名前の代わりに使用されます。

**[アクション]** に、**電子メール通知**などの、アクションの名前を入力します。 **[アクションの種類]** で、**[電子メール/SMS/プッシュ/音声]** を選びます。 **[詳細]** で、**[詳細の編集]** を選択します。

**[電子メール/SMS/プッシュ/音声]** ウィンドウで、名前を入力します。 **[電子メール]** チェック ボックスをオンにして、有効な電子メール アドレスを入力します。

![電子メール アクション グループの構成](./media/automation-tutorial-update-management/configure-email-action-group.png)

**[電子メール/SMS/プッシュ/音声]** ウィンドウで、**[OK]** を選択します。 **[アクション グループの追加]** ウィンドウで、**[OK]** を選択します。

アラートの電子メールの件名をカスタマイズするには、**[ルールの作成]** の **[アクションをカスタマイズする]** で、**[電子メールの件名]** を選択します。 完了したら、**[アラート ルールの作成]** を選択します。 このアラートにより、更新プログラムの展開が成功したことと、その更新プログラムの展開の実行対象となったコンピューターが通知されます。

## <a name="schedule-an-update-deployment"></a>更新プログラムのデプロイをスケジュールする

次に、リリース スケジュールとサービス期間に従って展開をスケジュールし、更新プログラムをインストールします。 デプロイに含める更新の種類を選択できます。 たとえば、緊急更新プログラムやセキュリティ更新プログラムを追加し、更新プログラムのロールアップを除外できます。

VM の新しい更新プログラムの展開をスケジュールするには、**[更新の管理]** に移動し、**[更新プログラムの展開のスケジュール]** を選択します。

**[新しい更新プログラムの展開]** で、次の情報を指定します。

* **[名前]**: 更新プログラムの展開に一意の名前を入力します。

* **[オペレーティング システム]**: 更新プログラムの展開の対象となる OS を選択します。

* **[Groups to update (preview)]\(更新するグループ (プレビュー)\)**: サブスクリプション、リソース グループ、場所、およびタグの組み合わせに基づいてクエリを定義し、デプロイに含める Azure VM の動的グループを構築します。 詳細については、[動的グループ](automation-update-management.md#using-dynamic-groups)に関するページを参照してください。

* **[更新するマシン]**: 保存した検索条件、インポートしたグループを選択するか、ドロップダウンから [マシン] を選択し、個別のマシンを選択します。 **[マシン]** を選択すると、マシンの準備状況が **[エージェントの更新の準備]** 列に示されます。 Log Analytics でコンピューター グループを作成するさまざまな方法については、[Log Analytics のコンピューター グループ](../log-analytics/log-analytics-computer-groups.md)に関するページを参照してください

* **更新プログラムの分類**: 更新プログラムの展開によって展開に追加されたソフトウェアの種類を選択します。 このチュートリアルでは、すべての種類を選択したままにします。

  分類の種類は次のとおりです。

   |OS  |type  |
   |---------|---------|
   |Windows     | 緊急更新プログラム</br>セキュリティ更新プログラム</br>更新プログラムのロールアップ</br>Feature Pack</br>Service Pack</br>定義の更新</br>ツール</br>更新プログラム        |
   |Linux     | 緊急更新プログラムとセキュリティ更新プログラム</br>他の更新プログラム       |

   分類の種類の詳細については、「[Update classifications (更新プログラムの分類)](automation-update-management.md#update-classifications)」をご覧ください。

* **[Updates to include/exclude]\(含める/除外する更新プログラム\)**: **[Include/Exclude]\(含める/除外する\)** ページが開きます。 含めるまたは除外する更新プログラムは別のタブに表示されます。 包含を処理する方法については、[包含の動作](automation-update-management.md#inclusion-behavior)に関するページを参照してください。

* **[スケジュール設定]**: **[スケジュール設定]** ウィンドウが開きます。 既定の開始時刻は、現在の時刻の 30 分後です。 開始時刻は、10 分後以降の将来の任意の時点に設定できます。

   展開を 1 回行うか、定期的なスケジュールを設定するかを指定することもできます。 **[繰り返し]** で **[1 回]** を選択します。 既定値の 1 日のままにし、**[OK]** を選択します。 これで定期的なスケジュールが設定されます。

* **[Pre-scripts + Post-scripts]\(事前スクリプト + 事後スクリプト\)**: デプロイの前後に実行するスクリプトを選択します。 詳細については、[事前および事後スクリプトの管理](pre-post-scripts.md)に関するページを参照してください。
* **[メンテナンス期間 (分)]**: 既定値のままにします。 更新プログラムを展開する時間の範囲を設定できます。 この設定により、定義したサービス期間内は変更が確実に実行されます。

* **[再起動のオプション]**: この設定によって、再起動の処理方法が決まります。 使用できるオプションは次のとおりです。
  * 必要に応じて再起動 (既定値)
  * 常に再起動
  * 再起動しない
  * Only reboot - will not install updates (再起動のみ - 更新プログラムをインストールしない)

スケジュールの構成が完了したら、**[作成]** を選択します。

![更新プログラムのスケジュール設定ウィンドウ](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

状態ダッシュボードに戻ります。 **[スケジュールされた更新プログラムの展開]** を選択して、作成した展開スケジュールを表示します。

## <a name="view-results-of-an-update-deployment"></a>更新プログラムのデプロイの結果を表示する

スケジュールされた展開の開始後、**[更新の管理]** の **[更新プログラムの展開]** タブに、展開の状態が表示されます。 展開が現在実行中の場合、状態は **[処理中]** と表示されます。 展開が完了すると、成功した場合は、状態が **[成功]** に変わります。 展開時に 1 つ以上の更新プログラムでエラーが発生した場合、状態は **[部分的に失敗]** になります。

完了した更新プログラムのデプロイを選択すると、その更新プログラムのデプロイ用のダッシュボードが表示されます。

![特定の展開に関する更新プログラムの展開ステータスのダッシュボード](./media/automation-tutorial-update-management/manageupdates-view-results.png)

**[更新プログラムを実行した結果]** には、VM 上の更新プログラムの合計数と展開結果が表示されます。 右側の表には、各更新プログラムとインストールの結果の詳細が示されます。

使用できる値の一覧を次に示します。

* **試行されていません**: メンテナンス期間として定義した時間が十分ではなかったため、更新プログラムがインストールされませんでした。
* **成功**: 更新できました。
* **失敗**: 更新できませんでした。

展開によって作成されたログ エントリをすべて表示するには、**[すべてのログ]** を選択します。

ターゲット VM での更新プログラムの展開を管理する Runbook のジョブ ストリームを確認するには、**[出力]** を選択します。

展開で発生したエラーの詳細情報を確認するには、**[エラー]** を選択します。

更新プログラムの展開が成功すると、次の例のような電子メールが送信され、展開が成功したことが示されます。

![電子メール アクション グループの構成](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Update Management のために VM をオンボードする
> * 更新の評価を表示する
> * アラートを構成する
> * 更新プログラムのデプロイをスケジュールする
> * デプロイの結果を表示する

更新の管理ソリューションの概要に進みます。

> [!div class="nextstepaction"]
> [更新の管理ソリューション](../operations-management-suite/oms-solution-update-management.md?toc=%2fazure%2fautomation%2ftoc.json)
