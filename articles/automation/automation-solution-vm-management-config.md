---
title: Azure Automation の Start/Stop VMs during off hours ソリューションを構成する
description: この記事では、さまざまなユース ケースまたはシナリオをサポートするように Start/Stop VMs during off-hours ソリューションを構成する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 241866129aa36d67af18fab86a26d9cbf7ce42d6
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582457"
---
# <a name="how-to-configure-startstop-vms-during-off-hours-solution"></a>Start/Stop VMs during off hours ソリューションの構成方法

**Start/Stop VMs during off hours** ソリューションを使用して、以下のことを行うことができます。

- [VM の起動と停止をスケジュール](#schedule)する。
- [Azure タグ](#tags)を使用して昇順で VM の起動および停止をスケジュールする (クラシック VM ではサポートされません)。
- [CPU 使用率の低さ](#cpuutil)に基づいて VM を自動停止する。

この記事では、これらのシナリオをサポートするようにソリューションを適切に構成する方法について説明します。 次のようなソリューションの他の一般的な構成設定を実行する方法も説明します。

* [電子メール通知を構成する](#configure-email-notifications)

* [VM を追加する](#add-a-vm)

* [VM を除外する](#exclude-a-vm)

* [起動および停止スケジュールを変更する](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>シナリオ 1:スケジュールに従って VM を開始/停止する

このシナリオは、ソリューションを最初にデプロイするときの既定の構成です。 たとえば、夕方退社するときにサブスクリプション全体のすべての VM を停止し、朝の出社時に VM を起動するように構成できます。 デプロイ中にスケジュール **Scheduled-StartVM** と **Scheduled-StopVM** を構成すると、対象の VM が起動および停止します。 VM の停止のみを行うようにこのソリューションを構成できます。カスタム スケジュールを構成する方法については、「[起動および停止スケジュールを変更する](#modify-the-startup-and-shutdown-schedules)」を参照してください。

> [!NOTE]
> スケジュール タイム パラメーターを構成するとき、タイム ゾーンは現在のタイム ゾーンになります。 ただし、Azure Automation では UTC 形式で格納されます。 これはデプロイ中に処理されるので、タイム ゾーンの変換を行う必要はありません。

次の変数を構成することで、どの VM を対象にするかを制御します: **External_Start_ResourceGroupNames**、**External_Stop_ResourceGroupNames**、および **External_ExcludeVMNames**。

サブスクリプションおよびリソース グループに対するアクション、または特定の VM リストのいずれかを対象にできますが、両方を有効にすることはできません。

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>サブスクリプションとリソース グループに対する起動および停止アクションを対象にする

1. ターゲット VM を指定するために、`External_Stop_ResourceGroupNames` と `External_ExcludeVMNames` 変数を構成します。

2. **Scheduled-StartVM** スケジュールと **Scheduled-StopVM** スケジュールを有効にして、更新します。

3. **ACTION** パラメーター フィールドを **start** に設定し、**WHATIF** パラメーター フィールドを True に設定して、**ScheduledStartStop_Parent** Runbook を実行し、ご自身の変更内容をプレビューします。

### <a name="target-the-start-and-stop-action-by-vm-list"></a>VM リストによる起動および停止アクションを対象にする

1. **ACTION** を **start** に設定して **ScheduledStartStop_Parent** Runbook を実行し、**VMList** パラメーター フィールドに VM のコンマ区切りリストを追加して、**WHATIF** パラメーター フィールドを True に設定します。 変更をプレビューします。

2. VM のコンマ区切りリスト (VM1, VM2, VM3) を使用して、`External_ExcludeVMNames` 変数を構成します。

3. このシナリオでは、`External_Start_ResourceGroupNames` と `External_Stop_ResourceGroupnames` の変数は考慮されません。 このシナリオでは、独自の Automation のスケジュールを作成する必要があります。 詳細については、「[Azure Automation の Runbook をスケジュール設定する](../automation/automation-schedules.md)」を参照してください。

    > [!NOTE]
    > **[ターゲット ResourceGroup 名]** の値は、`External_Start_ResourceGroupNames` と `External_Stop_ResourceGroupNames` の両方の値として格納されます。 粒度を高めるためには、これらの変数をそれぞれ変更して、異なるリソース グループを対象にします。 開始アクションでは `External_Start_ResourceGroupNames` を使用し、停止アクションでは `External_Stop_ResourceGroupNames` を使用します。 VM が起動スケジュールと停止スケジュールに自動的に追加されます。

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>シナリオ 2:タグを使用して VM を順番に起動/停止する

分散ワークロードをサポートする複数の VM のコンポーネントを 2 つ以上含む環境では、コンポーネントの起動および停止順序を指定できることが重要です。 このシナリオを実現するには、次の手順を実行します。

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>サブスクリプションとリソース グループに対する起動および停止アクションを対象にする

1. `External_Start_ResourceGroupNames` および `External_Stop_ResourceGroupNames` 変数で対象となる VM に、正の整数値を持つ `sequencestart` と `sequencestop` タグを追加します。 起動アクションと停止アクションは昇順で実行されます。 VM にタグを付ける方法については、[Azure で Windows 仮想マシンにタグを付ける方法](../virtual-machines/windows/tag.md)と [Azure で Linux 仮想マシンにタグを付ける方法](../virtual-machines/linux/tag.md)に関するページをご覧ください。

2. スケジュール **Sequenced-StartVM** と **Sequenced-StopVM** を、要件を満たす日付と時刻に変更し、スケジュールを有効にします。

3. **ACTION** を **start** に設定し、**WHATIF** を True に設定して、**SequencedStartStop_Parent** Runbook を実行し、ご自身の変更をプレビューします。

4. アクションをプレビューし、運用 VM に対して実装する前に、必要な変更を行います。 準備ができたら、パラメーターを **False** に設定して手動で Runbook を実行するか、Automation のスケジュール `Sequenced-StartVM` と `Sequenced-StopVM` が、指定されたスケジュールに従って自動的に実行されるようにします。

### <a name="target-the-start-and-stop-action-by-vm-list"></a>VM リストによる起動および停止アクションを対象にする

1. `VMList` パラメーターに追加する予定の VM に、正の整数値を持つ `sequencestart` および `sequencestop` タグを追加します。

2. **ACTION** を **start** に設定して **SequencedStartStop_Parent** Runbook を実行し、**VMList** パラメーター フィールドに VM のコンマ区切りリストを追加して、**WHATIF** を True に設定します。 変更をプレビューします。

3. VM のコンマ区切りリスト (VM1, VM2, VM3) を使用して、`External_ExcludeVMNames` 変数を構成します。

4. このシナリオでは、`External_Start_ResourceGroupNames` と `External_Stop_ResourceGroupnames` の変数は考慮されません。 このシナリオでは、独自の Automation のスケジュールを作成する必要があります。 詳細については、「[Azure Automation の Runbook をスケジュール設定する](../automation/automation-schedules.md)」を参照してください。

5. アクションをプレビューし、運用 VM に対して実装する前に、必要な変更を行います。 準備ができたら、パラメーターを **False** に設定して、**monitoring-and-diagnostics/monitoring-action-groupsrunbook** を手動で実行します。 または、指定したスケジュールに従って、Automation のスケジュール `Sequenced-StartVM` と `Sequenced-StopVM` が自動的に実行されるようにします。

## <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>シナリオ 3:CPU 使用率に基づいて自動的に開始/停止する

このソリューションは、営業時間外などの非ピーク期間中に使用されていない VM を評価し、プロセッサ使用率が指定されたパーセンテージ未満の場合は自動的にシャットダウンすることで、サブスクリプションで実行中の Azure Resource Manager および Classic 仮想マシンのコストを管理する上で役立ちます。

既定では、ソリューションは、CPU 割合メトリックを評価して、平均使用率が 5% 以下であるかどうかを確認するように事前構成されています。 このシナリオは次の変数によって制御され、既定値が要件を満たしていない場合は変更できます。

* `External_AutoStop_MetricName`
* `External_AutoStop_Threshold`
* `External_AutoStop_TimeAggregationOperator`
* `External_AutoStop_TimeWindow`
* `External_AutoStop_Frequency`
* `External_AutoStop_Severity`

サブスクリプションおよびリソース グループに対するアクションを有効にして対象にするか、特定の VM リストを対象にすることができます。

**AutoStop_CreateAlert_Parent** runbook を実行すると、対象となるサブスクリプション、リソース グループ、および VM が存在することが確認されます。 VM が存在する場合、Runbook では、親 Runbook によって確認された VM ごとに **AutoStop_CreateAlert_Child** Runbook を呼び出します。 この子 runbook は以下を実行します。

* 確認された VM ごとにメトリック アラート ルールを作成します。

* 指定された時間間隔で CPU が構成済みのしきい値を下回った場合は、特定の VM で **AutoStop_VM_Child** Runbook をトリガーします。 その後、この runbook は VM を停止しようとします。

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-subscription"></a>サブスクリプション内のすべての VM で自動停止アクションを対象にするには以下を行ってください。

1. 確実に、`External_Stop_ResourceGroupNames` 変数が空であるか、* (ワイルドカード) に設定されるようにします。

2. (省略可能な手順) 一部の VM を自動シャットダウンから除外する場合は、VM 名のコンマ区切りリストを `External_ExcludeVMNames` 変数に追加できます。

3. `Schedule_AutoStop_CreateAlert_Parent` スケジュールを有効にして、サブスクリプション内のすべての VM で必要な VM の停止メトリック アラート ルールを作成します。 この種類のスケジュールを実行すると、新しい VM がサブスクリプションに追加されたときに新しいメトリック アラート ルールを作成できます。

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-resource-group-or-multiple-resource-groups"></a>リソース グループまたは複数のリソース グループ内のすべての VM に対して、自動停止アクションを対象にするには

1. リソース グループ名のコンマ区切りリストを、`External_Stop_ResourceGroupNames` 変数に追加します。

2. 一部の VM を自動シャットダウンから除外する場合は、VM 名のコンマ区切りリストを `External_ExcludeVMNames` 変数に追加することができます。

3. **Schedule_AutoStop_CreateAlert_Parent** スケジュールを有効にして実行し、リソース グループのすべての VM で必要な VM 停止メトリック アラート ルールを作成します。 この操作をスケジュールに従って実行すると、新しい VM がリソース グループに追加されたときに新しいメトリック アラート ルールを作成できます。

### <a name="to-target-the-autostop-action-to-a-list-of-vms"></a>VM の一覧に対して自動停止アクションを対象にするには

1. 新しい[スケジュール](shared-resources/schedules.md#create-a-schedule)を作成し、**AutoStop_CreateAlert_Parent** Runbook にリンクして、VM 名のコンマ区切りリストを `VMList` パラメーターに追加します。

2. 必要に応じて、VM の一部を自動シャットダウンから除外する場合は、VM 名のコンマ区切りリストを `External_ExcludeVMNames` 変数に追加できます。

## <a name="configure-email-notifications"></a>電子メール通知の構成

ソリューションのデプロイ後に電子メール通知を変更するには、デプロイ時に作成されたアクション グループを変更します。  

> [!NOTE]
> Azure Government Cloud のサブスクリプションでは、このソリューションの電子メール機能はサポートされません。

1. Azure portal で、 **[監視]** 、 **[アクション グループ]** の順に移動します。 **StartStop_VM_Notication** というアクション グループを選択します。

    ![Automation Update Management ソリューション ページ](media/automation-solution-vm-management/azure-monitor.png)

2. **[StartStop_VM_Notification]** ページで、 **[詳細]** の **[詳細の編集]** をクリックします。 これで、 **[電子メール/SMS/プッシュ/音声]** ページが開きます。 メール アドレスを更新し、 **[OK]** をクリックして変更を保存します。

    ![Automation Update Management ソリューション ページ](media/automation-solution-vm-management/change-email.png)

    アクション グループにさらにアクションを追加することもできます。アクション グループの詳細については、「[アクション グループ](../azure-monitor/platform/action-groups.md)」を参照してください。

ソリューションで仮想マシンがシャットダウンされたときに送信されるメールの例を以下に示します。

![Automation Update Management ソリューション ページ](media/automation-solution-vm-management/email.png)

## <a name="addexclude-vms"></a><a name="add-exclude-vms"></a>VM を追加/除外する

ソリューションには、ソリューションの対象となる VM を追加する機能、ソリューションからマシンを除外する機能があります。

### <a name="add-a-vm"></a>VM を追加する

実行時に VM が開始/停止ソリューションに含まれるようにするためのオプションは 2 つあります。

* ソリューションの親 [Runbook](automation-solution-vm-management.md#runbooks) のそれぞれに `VMList` パラメーターがあります。 状況に合わせて適切な親 Runbook をスケジュールするときに、このパラメーターに VM 名のコンマ区切りリストを渡すことができ、これらの VM はソリューションが実行されるときに含まれます。

* 複数の VM を選択するには、開始または停止する VM が含まれているリソース グループ名で `External_Start_ResourceGroupNames` と `External_Stop_ResourceGroupNames` を設定します。 サブスクリプションのすべてのリソース グループに対してソリューションを実行するように、変数を `*` の値に設定することもできます。

### <a name="exclude-a-vm"></a>VM を除外する

VM をソリューションから除外するには、それを `External_ExcludeVMNames` 変数に追加します。 この変数は、開始/停止ソリューションから除外する特定の VM のコンマ区切りリストです。 このリストは 140 個の VM までに制限されています。 このコンマ区切りリストに 140 を超える VM を追加した場合は、除外されるように設定された VM が誤って起動または停止される可能性があります。

## <a name="modify-the-startup-and-shutdown-schedules"></a>起動および停止スケジュールを変更する

このソリューションの起動および停止スケジュールを管理するには、「[Azure Automation の Runbook をスケジュール設定する](automation-schedules.md)」で説明されている手順に従います。 VM の開始と停止それぞれについて、個別のスケジュールが存在する必要があります。

特定の時刻に VM の停止のみを行うようにソリューションを構成できます。 このシナリオでは、**停止**スケジュールを作成するだけで、対応する**開始**スケジュールは作成しません。 そのためには、次の手順を実行する必要があります。

1. 確実に、シャットダウンする VM のリソース グループが `External_Stop_ResourceGroupNames` 変数に追加されるようにします。

2. VM をシャットダウンする時刻の独自のスケジュールを作成します。

3. **ScheduledStartStop_Parent** Runbook に移動し、 **[スケジュール]** をクリックします。 これにより、前の手順で作成したスケジュールを選択できます。

4. **[パラメーターと実行設定]** を選択し、**ACTION** フィールドを **Stop** に設定します。

5. **[OK]** を選択して変更を保存します。

## <a name="next-steps"></a>次のステップ

* Start/Stop VMs during off-hours のトラブルシューティング方法については、「 [Start/Stop VMs のトラブルシューティング](troubleshoot/start-stop-vm.md)」を参照してください。

* Azure Monitor ログに書き込まれた Automation レコードとサンプル ログ検索クエリを[確認](automation-solution-vm-management-logs.md)して、Start/Stop VM から Automation runbook ジョブの状態を分析します。
