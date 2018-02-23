---
title: "Start/Stop VMs during off-hours ソリューション (プレビュー) | Microsoft Docs"
description: "この VM 管理ソリューションは、スケジュールに従って Azure Resource Manager 仮想マシンを起動および停止し、Log Analytics からプロアクティブに監視します。"
services: automation
documentationCenter: 
authors: eslesar
manager: carmonm
editor: 
ms.assetid: 06c27f72-ac4c-4923-90a6-21f46db21883
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: magoedte
ms.openlocfilehash: 7ffd424de2a7224b5ac50fa228289c5397092b2e
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="startstop-vms-during-off-hours-solution-preview-in-azure-automation"></a>Azure Automation の Start/Stop VMs during off-hours ソリューション (プレビュー)

Start/Stop VMs during off-hours ソリューションでは、ユーザー定義のスケジュールで Azure Virtual Machines を起動および停止し、Azure Log Analytics によって洞察を深め、[SendGrid](https://azuremarketplace.microsoft.com/marketplace/apps/SendGrid.SendGrid?tab=Overview) を使用することで必要に応じて電子メールを送信します。 ほとんどのシナリオで Azure Resource Manager とクラシック VM の両方がサポートされます。 

このソリューションは、サーバーレスで低コストなリソースを使用してコストを削減する必要のあるユーザーに、分散的なオートメーション オプションを提供します。 このソリューションでは次のことが可能です。

* VM の起動および停止をスケジュールする。
* Azure タグを使用して昇順で VM の起動および停止をスケジュールする (クラシック VM ではサポートされません)。
* CPU 使用率の低さに基づいて VM を自動停止する。

## <a name="prerequisites"></a>前提条件

- [Azure 実行アカウント](automation-offering-get-started.md#authentication-methods)を使用した Runbook。  認証方法としては、実行アカウントの使用をお勧めします。有効期限が切れたり頻繁に変わったりするパスワードではなく、証明書を使った認証が使用されるためです。  

- このソリューションが管理するのは、Azure Automation アカウントと同じサブスクリプションの VM に限られます。  

- このソリューションがデプロイされる Azure リージョンは、オーストラリア南東部、カナダ中部、インド中部、米国東部、東日本、東南アジア、英国南部、および西ヨーロッパのみです。  
    
    > [!NOTE]
    > VM のスケジュールを管理する Runbook は、任意のリージョンの VM を対象にできます。  

- VM の開始および停止の Runbook の終了時に電子メール通知を送信するには、Azure Marketplace からオンボード中に、**[はい]** を選択して SendGrid をデプロイします。 

    > [!IMPORTANT]
    > SendGrid はサード パーティのサービスです。 サポートについては、[SendGrid](https://sendgrid.com/contact/) にお問い合わせください。  
    >
   
    SendGrid に関する制限:

    * ユーザー、サブスクリプションごとに最大 1 つの SendGrid アカウント。
    * サブスクリプションごとに最大 2 つの SendGrid アカウント。

このソリューションの以前のバージョンをデプロイしている場合は、このリリースをデプロイする前に、まず以前のバージョンをアカウントから削除する必要があります。  

## <a name="solution-components"></a>ソリューションのコンポーネント

仮想マシンのスタートアップとシャットダウンをビジネス要件に合わせて調整できるように、このソリューションには、構成済みの Runbook、スケジュール、および Log Analytics との統合が含まれています。 

### <a name="runbooks"></a>Runbooks

次の表は、Automation アカウントにデプロイされる Runbook の一覧です。  Runbook のコードを変更することはお勧めできません。 新しい機能が必要なときは、独自の Runbook を記述してください。

> [!IMPORTANT]
> 名前の末尾に "child" が付いている Runbook は、直接実行しないでください。
>

すべての親 Runbook に *WhatIf* パラメーターが含まれます。 これが **True** に設定されている場合、*WhatIf* では、*WhatIf* パラメーターなしで実行するときの Runbook の正確な動作の詳細な記述がサポートされ、正しい VM が対象となっていることが検証されます。  *WhatIf* パラメーターが **False** に設定されている場合、Runbook は定義されているアクションのみを実行します。 

|**Runbook** | **パラメーター** | **説明**|
| --- | --- | ---| 
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | 親 Runbook からのみ呼び出されます。 AutoStop シナリオでリソースごとにアラートを作成します。| 
|AutoStop_CreateAlert_Parent | WhatIf: True または False <br> VMList | 対象となるサブスクリプションまたはリソース グループ内の VM 上で、Azure アラート ルールを作成または更新します。 <br> VMList: VM のコンマ区切りリストです。  *vm1,vm2,vm3* など。| 
|AutoStop_Disable | なし | AutoStop アラートと既定のスケジュールを無効にします。| 
|AutoStop_StopVM_Child | WebHookData | 親 Runbook からのみ呼び出されます。 アラート ルールはこの Runbook を呼び出して、VM を停止します。|  
|Bootstrap_Main | なし | 通常 Azure Resource Manager からアクセスできない webhookURI などのブートストラップ構成を設定するために、1 度だけ使用されます。 この Runbook は、デプロイが正常に完了したら自動的に削除されます。|  
|ScheduledStartStop_Child | VMName <br> Action: Stop または Start <br> ResourceGroupName | 親 Runbook からのみ呼び出されます。 停止スケジュールを停止または起動します。|  
|ScheduledStartStop_Parent | Action: Stop または Start <br> WhatIf: True または False | サブスクリプション内のすべての VM に影響します。 こうしたターゲット リソース グループでのみ実行されるように、**External_Start_ResourceGroupNames** と **External_Stop_ResourceGroupNames** を編集します。 **External_ExcludeVMNames** 変数を更新することで、特定の VM を除外することもできます。 *WhatIf* は他の Runbook と同様に動作します。|  
|SequencedStartStop_Parent | Action: Stop または Start <br> WhatIf: True または False | 起動/停止アクティビティのシーケンスを指定する各 VM に、**SequenceStart** および **SequenceStop** という名前のタグを作成します。 タグの値は、起動または停止する順序に対応する正の整数 (1、2、3) にする必要があります。 *WhatIf* は他の Runbook と同様に動作します。 <br> **注**: Azure Automation 変数で External_Start_ResourceGroupNames、External_Stop_ResourceGroupNames、および External_ExcludeVMNames として定義されたリソース グループ内に VM が存在する必要があります。 アクションを有効にするための適切なタグが必要です。|

### <a name="variables"></a>variables

次の表は、Automation アカウント内に作成される変数の一覧です。  **External** プレフィックスが付いた変数のみを変更することをお勧めします。 **Internal** プレフィックスが付いた変数を変更すると、望ましくない効果がもたらされます。  

|**Variable** | **説明**|
---------|------------|
|External_AutoStop_Condition | アラートをトリガーする前の条件を構成するのに必要な条件演算子。 指定できる値は、**GreaterThan**、**GreaterThanOrEqual**、**LessThan**、および **LessThanOrEqual** です。|  
|External_AutoStop_Description | CPU の割合がしきい値を超えた場合に VM を停止するアラート。|  
|External_AutoStop_MetricName | Azure アラート ルールが構成されるパフォーマンス メトリックの名前。| 
|External_AutoStop_Threshold | 変数 *External_AutoStop_MetricName* で指定される Azure アラート ルールのしきい値。 パーセンテージの値の範囲は 1 ～ 100 です。|  
|External_AutoStop_TimeAggregationOperator | 時間の集計演算子。条件を評価するために選択した時間枠のサイズに適用されます。 指定できる値は、**Average**、**Minimum**、**Maximum**、**Total**、および **Last** です。|  
|External_AutoStop_TimeWindow | アラートをトリガーするために選択されたメトリックを Azure が分析する時間枠のサイズ。 このパラメーターは、timespan 形式で入力を受け入れます。 使用可能な値は、5 分 ～ 6 時間です。|  
|External_EmailFromAddress | 電子メールの送信者を指定します。|  
|External_EmailSubject | 電子メールの件名に使用するテキストを指定します。|  
|External_EmailToAddress | 電子メールの受信者を指定します。 コンマで名前を区切ります。|  
|External_ExcludeVMNames | 除外される VM の名前を入力します。スペースなしのコンマで名前を区切ります。|  
|External_IsSendEmail | 完了時に電子メール通知を送信するオプションを指定します。  **Yes** を指定するか、メールを送信しない場合は **No** を選択します。  最初のデプロイ時に電子メール通知を有効にしていない場合は、このオプションを **No** にする必要があります。|  
|External_Start_ResourceGroupNames | 開始アクションの対象となる 1 つ以上のリソース グループを、コンマ区切り値で指定します。|  
|External_Stop_ResourceGroupNames | 停止アクションの対象となる 1 つ以上のリソース グループを、コンマ区切り値で指定します。|  
|Internal_AutomationAccountName | Automation アカウントの名前を指定します。|  
|Internal_AutoSnooze_WebhookUri | AutoStop シナリオで呼び出される Webhook URI を指定します。|  
|Internal_AzureSubscriptionId | Azure サブスクリプション ID を指定します。|  
|Internal_ResourceGroupName | Automation アカウントのリソース グループ名を指定します。|  
|Internal_SendGridAccountName | SendGrid アカウント名を指定します。|  
|Internal_SendGridPassword | SendGrid アカウントのパスワードを指定します。|  

<br>

すべてのシナリオで、VM を対象とするために **External_Start_ResourceGroupNames**、**External_Stop_ResourceGroupNames**、および **External_ExcludeVMNames** 変数が必要です。例外は、**AutoStop_CreateAlert_Parent** Runbook に対して VM のコンマ区切りリストを指定する場合です。 つまり、開始および停止アクションを発生させるためには、ターゲット リソース グループ内に VM が存在する必要があります。 このロジックは Azure Policy に少し似ています。サブスクリプションまたはリソース グループを対象にすることができ、アクションは新しく作成された VM に継承されます。 この方法により、VM ごとに個別のスケジュールを保持したり、スケールで起動および停止を管理したりする必要がなくなります。

### <a name="schedules"></a>スケジュール

次の表は、Automation アカウント内に作成される既定のスケジュールの一覧です。  それらを変更したり、独自のカスタム スケジュールを作成したりできます。  既定では、それぞれが **Scheduled_StartVM** と **Scheduled-StopVM** を除いて無効になっています。

すべてのスケジュールを有効にすることはお勧めしません。アクションのスケジュール間で重複が生じる可能性があるためです。 実行する必要がある最適化を特定し、それに応じて変更することをお勧めします。  詳細については、概要セクションのシナリオ例をご覧ください。   

|**スケジュール名** | **頻度** | **説明**|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 8 時間ごと | 8 時間ごとに AutoStop_CreateAlert_Parent Runbook を実行します。Runbook は Azure Automation 変数の External_Start_ResourceGroupNames、External_Stop_ResourceGroupNames、および External_ExcludeVMNames の VM ベースの値を停止します。  または、VMList パラメーターを使用して VM のコンマ区切りリストを指定できます。|  
|Scheduled_StopVM | ユーザー定義、毎日 | *Stop* パラメーターを持つ Scheduled_Parent Runbook を毎日指定された時刻に実行します。  アセット変数によって定義されたルールを満たす VM すべてを自動的に停止します。 関連スケジュールで **Scheduled-StartVM** を有効にすることをお勧めします。|  
|Scheduled_StartVM | ユーザー定義、毎日 | *Start* パラメーターを持つ Scheduled_Parent Runbook を毎日指定された時刻に実行します。  適切な変数によって定義されたルールを満たす VM すべてを自動的に開始します。  関連スケジュールで **Scheduled-StopVM** を有効にすることをお勧めします。|
|Sequenced-StopVM | 午前 1 時 00 分 (UTC)、毎週金曜日 | *Stop* パラメーターを持つ Sequenced_Parent Runbook を毎週金曜日の指定された時刻に実行します。  適切な変数で定義された **SequenceStop** のタグを持つ VM すべてを順番 (昇順) に停止します。  タグの値とアセット変数の詳細については、「Runbook」セクションを参照してください。  関連スケジュールで **Sequenced-StartVM** を有効にすることをお勧めします。|
|Sequenced-StartVM | 午後 1 時 00 分 (UTC)、毎週月曜日 | *Start* パラメーターを持つ Sequenced_Parent Runbook を毎週月曜日の指定された時刻に実行します。 適切な変数で定義された **SequenceStart** のタグを持つ VM すべてを順番 (降順)に 起動します。  タグの値とアセット変数の詳細については、「Runbook」セクションを参照してください。  関連スケジュールで **Sequenced-StopVM** を有効にすることをお勧めします。|

<br>

## <a name="configuration"></a>構成

Start/Stop VMs during off-hours ソリューションを、ご利用の Automation アカウントに追加し、変数を設定してソリューションをカスタマイズするには、以下の手順を実行します。

1. Azure Portal で、**[リソースの作成]** をクリックします。<br> ![Azure Portal](media/automation-solution-vm-management/azure-portal-01.png)<br>  
2. [Marketplace] ウィンドウで、「**Start**」、「**Start/Stop**」などのキーワードを入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 または、ソリューションのフルネームから 1 つ以上のキーワードを入力し、Enter キーを押すこともできます。  検索結果から **ピーク時間外 VM 起動/停止 [プレビュー]** を選択します。  
3. 選択したソリューションの **ピーク時間外 VM 起動/停止 [プレビュー]** ウィンドウで概要を確認し、**作成** をクリックします。  
4. **[ソリューションの追加]** ウィンドウが表示されます。 Automation サブスクリプションにインポートする前に、ソリューションの設定を行うよう求められます。<br><br> ![VM 管理の ソリューションの追加 ブレード](media/automation-solution-vm-management/azure-portal-add-solution-01.png)<br><br>
5.  **[ソリューションの追加]** ウィンドウで、**[ワークスペース]** を選択します。 Automation アカウントと同じ Azure サブスクリプションに関連付けられている OMS ワークスペースを選択します。 ワークスペースがない場合は、**[新しいワークスペースの作成]** を選択します。 **[OMS ワークスペース]** ウィンドウで、次の手順を実行します。 
   - 新しい **OMS ワークスペース**の名前を指定します。
   - 関連付ける**サブスクリプション**をドロップダウン リストから選択します (既定値が適切でない場合)。
   - **[リソース グループ]** では、新しいリソース グループを作成するか、既存のリソース グループを選択できます。  
   - **[場所]**を選択します。  現在使用できる場所は、**オーストラリア南東部**、**カナダ中部**、**インド中部**、**米国東部**、**東日本**、**東南アジア**、**英国南部**、および**西ヨーロッパ**のみです。
   - **[価格レベル]** を選択します。  このソリューションには、Free レベルと OMS Paid レベルの 2 つのレベルがあります。  Free レベルは、1 日に収集できるデータの量、リテンション期間、および Runbook ジョブの実行時間 (分) に上限が設けられています。  OMS Paid レベルでは 1 日に収集できるデータの量に上限がありません。  

        > [!NOTE]
        > GB あたりの (スタンドアロン) Paid レベルがオプションとして表示されますが、適用することはできません。  このオプションを選択し、サブスクリプションでこのソリューションの作成を続行すると失敗します。  この問題は、このソリューションが正式にリリースされたときに解決されます。<br>このソリューションでは、Automation ジョブ分数とログ インジェストのみが使用されます。  追加 OMS ノードが環境に追加されることはありません。  

6. **[OMS ワークスペース]** ウィンドウに必要な情報を入力したら、**[作成]** をクリックします。  進行状況はメニューの **[通知]** で追跡できます。完了したら、**[ソリューションの追加]** ウィンドウに戻ります。  
7. **[ソリューションの追加]** ウィンドウで、**[Automation アカウント]** を選択します。  新しい OMS ワークスペースを作成する場合は、そのワークスペースに関連付ける Automation アカウントも新しく作成する必要があります。  **[Automation アカウントの作成]** を選択し、**[Automation アカウントの追加]** ウィンドウで、次の情報を入力します。 
  - **[名前]** フィールドに、Automation アカウントの名前を入力します。

    それ以外のオプションはすべて、選択した OMS ワークスペースに基づいて自動的に入力されます。 こうしたオプションは変更できません。  このソリューションに含まれている Runbook は、Azure 実行アカウントが既定の認証方法になります。  **[OK]** をクリックすると、構成オプションが検証され、Automation アカウントが作成されます。  進行状況は、メニューの **[通知]** で追跡できます。 

    それ以外の方法として、既存の Automation 実行アカウントを選択することもできます。  別の OMS ワークスペースに関連付けられていないアカウントを選択するように注意してください。 既に関連付けられていると、メッセージが返され、別の Automation 実行アカウントを選択するか、新しく作成する必要があります。<br><br> ![Automation Account Already Linked to OMS Workspace](media/automation-solution-vm-management/vm-management-solution-add-solution-blade-autoacct-warning.png)<br>

8. 最後に、**[ソリューションの追加]** ウィンドウで、**[構成]** を選択します。 **[パラメーター]** ウィンドウが表示されます。<br><br> ![ソリューションの [パラメーター] ウィンドウ](media/automation-solution-vm-management/azure-portal-add-solution-02.png)<br><br>  ここでは、次の操作が求められます。  
   - **ターゲット ResourceGroup 名**を指定します。 これは、このソリューションで管理する VM を含むリソース グループの名前です。  複数の名前を入力する場合は、それぞれをコンマで区切ってください (値の大文字と小文字は区別されません)。  ワイルドカードを使用して、サブスクリプション内の全リソース グループの VM を対象にすることもできます。
   - **VM 除外リスト (文字列)** を指定します。 これは、ターゲット リソース グループの 1 つ以上の仮想マシンの名前です。  複数の名前を入力する場合は、それぞれをコンマで区切ってください (値の大文字と小文字は区別されません)。  ワイルドカードの使用がサポートされています。
   - **スケジュール**を選択します。 これは、ターゲット リソース グループの VM を定期的に起動および停止する日時です。  既定では、スケジュールは UTC タイム ゾーンに合わせて構成されます。 別のリージョンを選択することはできません。  ソリューションの構成後、スケジュールを特定のタイム ゾーンに構成するには、「[起動および停止スケジュールの変更](#modifying-the-startup-and-shutdown-schedule)」を参照してください。
   - SendGrid から**電子メール通知**を受信するには、既定値 **[はい]** をそのまま使用し、有効なメール アドレスを指定します。 **[いいえ]** を選択し、後から電子メール通知を受信することにした場合は、コンマで区切られた有効な電子メール アドレスで **External_EmailToAddress** 変数を更新し、**External_IsSendEmail** 変数を **[はい]** という値で変更します。  

9. ソリューションに必要な初期設定を構成したら、**[作成]** を選択します。  すべての設定が検証された後、ソリューションは、ご利用のサブスクリプションにデプロイされます。  このプロセスは、完了までに数秒かかる場合があります。進行状況は、メニューの **[通知]** で確認してください。 

## <a name="collection-frequency"></a>収集の頻度

Automation ジョブのログとジョブのストリーム データは、5 分おきに Log Analytics リポジトリに取り込まれます。  

## <a name="using-the-solution"></a>ソリューションの使用

VM 管理ソリューションを追加すると、**[StartStopVMView]** タイルが、Azure Portal から Log Analytics ワークスペースのダッシュボードに追加されます。  このタイルには、そのソリューションに関して開始された Runbook ジョブの数と、正常に終了した Runbook ジョブの数、およびそのグラフが表示されます。<br><br> ![VM 管理の [StartStopVM View (StartStopVM ビュー)] タイル](media/automation-solution-vm-management/vm-management-solution-startstopvm-view-tile.png)  

Automation アカウントで、ソリューションにアクセスして管理するには、**[ワークスペース]** オプションを選択します。 Log Analytics ページで、左側のウィンドウから **[ソリューション]** を選択します。  **[ソリューション]** ページで、一覧から **Start-Stop-VM[ワークスペース]** ソリューションを選択します。<br><br> ![Log Analytics のソリューション一覧](media/automation-solution-vm-management/azure-portal-select-solution-01.png)  

ソリューションを選択すると、**Start-Stop-VM[ワークスペース]** ソリューション ウィンドウが表示されます。 ここでは、**StartStopVM** タイルなど、重要な情報を確認できます。 Log Analytics ワークスペースと同様、このタイルには、そのソリューションに関して開始された Runbook ジョブの数と、正常に終了した Runbook ジョブの数、およびそのグラフが表示されます。<br><br> ![Automation Update Management ソリューション ページ](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)  

ここから、ジョブ レコードの詳細な分析を実行するには、ドーナツ タイルをクリックします。 ソリューション ダッシュボードに、ジョブ履歴と定義済みのログ検索クエリが表示されます。 Log Analytics Advanced ポータルに切り替えて、検索クエリに基づいて検索を実行します。  

すべての親 Runbook に *WhatIf* パラメーターが含まれます。 これが **True** に設定されている場合、*WhatIf* パラメーターなしで実行するときの Runbook の正確な動作の詳細な記述がサポートされ、正しい VM が対象となっていることが検証されます。 *WhatIf* パラメーターが **False** に設定されている場合、Runbook は定義されているアクションのみを実行します。  


### <a name="scenario-1-daily-startstop-vms-across-a-subscription-or-target-resource-groups"></a>シナリオ 1: サブスクリプションまたはターゲット リソース グループ全体で毎日 VM を起動/停止する 

これは、ソリューションを最初にデプロイするときの既定の構成です。  たとえば、夕方退社するときにサブスクリプション全体のすべての VM を停止し、朝の出社時に VM を起動するように構成できます。 デプロイ中にスケジュール **Scheduled-StartVM** と **Scheduled-StopVM** を構成すると、対象の VM が起動および停止します。
>[!NOTE]
>スケジュール タイム パラメーターを構成するとき、タイム ゾーンは現在のタイム ゾーンになります。 ただし、Azure Automation では UTC 形式で格納されます。  これはデプロイ中に処理されるので、タイム ゾーンの変換を行う必要はありません。

**External_Start_ResourceGroupNames**、**External_Stop_ResourceGroupNames**、および **External_ExcludeVMNames** の各変数を構成することで、どの VM を対象にするかを制御します。  

>[!NOTE]
> **ターゲット ResourceGroup 名**の値は、**External_Start_ResourceGroupNames** と **External_Stop_ResourceGroupNames** の両方の値として格納されます。 粒度を高めるためには、これらの変数をそれぞれ変更して、異なるリソース グループを対象にします。  起動アクション用には **External_Start_ResourceGroupNames** を使用し、停止アクション用には **External_Stop_ResourceGroupNames** を使用します。 VM が起動スケジュールと停止スケジュールに自動的に追加されます。

構成をテストして検証するには、手動で **ScheduledStartStop_Parent** Runbook を開始し、ACTION パラメーターを **start** か **stop** に設定して、WHATIF パラメーターを **True** に設定します。<br><br> ![Runbook のパラメーターを構成する](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)


 スケジュールされたアクションをプレビューし、運用 VM に対して実装する前に、必要な変更を行います。  パラメーターを **False** にセットして手動で Runbook を実行するか、Automation のスケジュール **Schedule-StartVM** と **Schedule-StopVM** が、指定されたスケジュールに従って自動的に実行されるように指定できます。

### <a name="scenario-2-sequence-the-startstop-vms-across-a-subscription-by-using-tags"></a>シナリオ 2: タグを使用してサブスクリプション全体の VM の起動/停止の順序を指定する
分散ワークロードをサポートする複数の VM のコンポーネントを 2 つ以上含む環境では、コンポーネントの起動および停止順序を指定できることが重要です。  これを実現するには、次の手順を実行します。

1. **External_Start_ResourceGroupNames** 変数および **External_Stop_ResourceGroupNames** 変数で対象となっている VM に、**SequenceStart** と **SequenceStop** タグを正の整数値で追加します。  起動アクションと停止アクションは昇順で実行されます。  VM にタグを付ける方法については、[Azure で Windows 仮想マシンにタグを付ける方法](../virtual-machines/windows/tag.md)と [Azure で Linux 仮想マシンにタグを付ける方法](../virtual-machines/linux/tag.md)に関するページをご覧ください。
2. スケジュール **Sequenced-StartVM** と **Sequenced-StopVM** を、要件を満たす日付と時刻に変更し、スケジュールを有効にします。  

構成をテストして検証するには、**SequencedStartStop_Parent** Runbook を手動で開始します。 ACTION パラメーターを **start** または **stop** に設定し、WHATIF パラメーターを **True** に設定します。<br><br> ![Runbook のパラメーターを構成する](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)<br> アクションをプレビューし、運用 VM に対して実装する前に、必要な変更を行います。  準備ができたら、パラメーターを **False** にセットして手動で Runbook を実行するか、Automation のスケジュール **Sequenced-StartVM** と **Sequenced-StopVM** が、指定されたスケジュールに従って自動的に実行されるようにします。  

### <a name="scenario-3-automate-startstop-vms-across-a-subscription-based-on-cpu-utilization"></a>シナリオ 3: CPU 使用率に基づいてサブスクリプション全体で VM を自動的に起動/停止する
このソリューションは、営業時間外などの非ピーク期間中に使用されていない Azure VM を評価し、プロセッサ使用率が x% 未満の場合は自動的にシャットダウンすることで、サブスクリプションの実行中の仮想マシン コストを管理するのに役立ちます。  

既定では、ソリューションは、CPU 割合メトリックを評価して、平均使用率が 5% 以下であるかどうかを確認するように事前構成されています。  これは次の変数によって制御され、既定値が要件を満たしていない場合は変更できます。

* External_AutoStop_MetricName
* External_AutoStop_Threshold
* External_AutoStop_TimeAggregationOperator
* External_AutoStop_TimeWindow

サブスクリプションおよびリソース グループに対するアクション、または特定の VM リストのいずれかを対象にできますが、両方を有効にすることはできません。  

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>サブスクリプションとリソース グループに対して停止アクションを指定する

1. **External_Stop_ResourceGroupNames** 変数と **External_ExcludeVMNames** 変数を構成して対象の VM を指定します。  
2. **Schedule_AutoStop_CreateAlert_Parent** スケジュールを有効にして更新します。
3. ACTION パラメーターを **start** に設定し、WHATIF パラメーターを **True** に設定して、**AutoStop_CreateAlert_Parent** Runbook を実行し、変更をプレビューします。

#### <a name="target-the-stop-action-by-vm-list"></a>VM リストによる停止アクションを対象にする

1. ACTION パラメーターを **start** に設定して **AutoStop_CreateAlert_Parent** Runbook を実行し、*VMList* パラメーターに VM のコンマ区切りリストを追加して、WHATIF パラメーターを **True** に設定します。 変更をプレビューします。  
2. **External_ExcludeVMNames** パラメーターを、VM のコンマ区切りリスト (VM1,VM2,VM3) で構成します。
3. このシナリオでは、**External_Start_ResourceGroupNames** 変数と **External_Stop_ResourceGroupnames** 変数は考慮されていません。  このシナリオでは、独自の Automation のスケジュールを作成する必要があります。 詳細については、「[Azure Automation の Runbook をスケジュール設定する](../automation/automation-schedules.md)」を参照してください。

これで CPU 使用率に基づいて VM を停止するスケジュールが完成したので、次のスケジュールの 1 つを有効にして開始する必要があります。

* サブスクリプションとリソース グループによる起動アクションを対象にします。  **Scheduled-StartVM** スケジュールのテストと有効化については、[シナリオ 1](#scenario-1:-daily-stop/start-vms-across-a-subscription-or-target-resource-groups) の手順を参照してください。
* サブスクリプション、リソース グループ、およびタグによる起動アクションを対象にします。  **Sequenced-StartVM** スケジュールのテストと有効化については、[シナリオ 2](#scenario-2:-sequence-the-stop/start-vms-across-a-subscription-using-tags) の手順を参照してください。


### <a name="configuring-email-notifications"></a>電子メール通知の構成

ソリューションのデプロイ後に電子メール通知を構成するには、次の 3 つの変数を変更します。

* External_EmailFromAddress: 送信者のメール アドレスを指定します。
* External_EmailToAddress: 通知メールを受信するメール アドレスのコンマ区切りリスト (user@hotmail.com,user@outlook.com) を指定します。
* External_IsSendEmail: 電子メールを受信する場合は **Yes** に設定します。 メール通知を無効にするには、**No** に設定します。   


### <a name="modifying-the-startup-and-shutdown-schedules"></a>起動および停止スケジュールの変更

このソリューションの起動および停止スケジュールを管理するには、「[Azure Automation の Runbook をスケジュール設定する](automation-schedules.md)」で説明されている手順に従います。     

## <a name="log-analytics-records"></a>Log Analytics のレコード

Automation により、ジョブ ログとジョブ ストリームの 2 種類のレコードが OMS リポジトリに作成されます。

### <a name="job-logs"></a>ジョブ ログ

プロパティ | [説明]|
----------|----------|
Caller |  操作を開始したユーザー。  スケジュールされたジョブのシステムまたは電子メール アドレスが記録されます。|
カテゴリ | データの種類の分類。  Automation の場合、値は JobLogs です。|
CorrelationId | GUID。Runbook ジョブの関連付け ID です。|
JobId | GUID。Runbook ジョブの ID です。|
operationName | Azure で実行された操作の種類を指定します。  Automation の場合、値は Job です。|
ResourceId | Azure のリソースの種類を指定します。  Automation の場合、値は Runbook に関連付けられている Automation アカウントです。|
ResourceGroup | Runbook ジョブのリソース グループ名を指定します。|
ResourceProvider | デプロイと管理の対象となるリソースのプロバイダー (Azure サービス) を指定します。  Automation の場合、その値は Azure Automation になります。|
ResourceType | Azure のリソースの種類を指定します。  Automation の場合、値は Runbook に関連付けられている Automation アカウントです。|
resultType | Runbook ジョブの状態。  次のいずれかの値になります。<br>- 開始済み<br>- 停止済み<br>- 中断<br>- 失敗<br>- 成功|
resultDescription | Runbook ジョブの結果の状態について説明します。  次のいずれかの値になります。<br>- ジョブが開始されました<br>- ジョブが失敗しました<br>- ジョブが完了しました|
RunbookName | Runbook の名前を指定します。|
SourceSystem | 送信されたデータのソース システムを指定します。  Automation の場合、値は OpsManager です|
StreamType | イベントの種類を指定します。 次のいずれかの値になります。<br>- 詳細<br>- 出力<br>- エラー<br>- 警告|
SubscriptionId | ジョブのサブスクリプション ID を指定します。
Time | Runbook ジョブが実行された日付と時刻。|


### <a name="job-streams"></a>ジョブ ストリーム

プロパティ | [説明]|
----------|----------|
Caller |  操作を開始したユーザー。  スケジュールされたジョブのシステムまたは電子メール アドレスが記録されます。|
カテゴリ | データの種類の分類。  Automation の場合、値は JobStreams です。|
JobId | GUID。Runbook ジョブの ID です。|
operationName | Azure で実行された操作の種類を指定します。  Automation の場合、値は Job です。|
ResourceGroup | Runbook ジョブのリソース グループ名を指定します。|
ResourceId | Azure のリソース ID を指定します。  Automation の場合、値は Runbook に関連付けられている Automation アカウントです。|
ResourceProvider | デプロイと管理の対象となるリソースのプロバイダー (Azure サービス) を指定します。  Automation の場合、その値は Azure Automation になります。|
ResourceType | Azure のリソースの種類を指定します。  Automation の場合、値は Runbook に関連付けられている Automation アカウントです。|
resultType | イベントが生成された時点における Runbook ジョブの結果。 次の値になります。<br>- 処理中|
resultDescription | Runbook からの出力ストリームが含まれます。|
RunbookName | Runbook の名前。|
SourceSystem | 送信されたデータのソース システムを指定します。  Automation の場合、値は OpsManager です。|
StreamType | ジョブ ストリームの種類。 次のいずれかの値になります。<br>- 処理中<br>- 出力<br>- 警告<br>- エラー<br>- デバッグ<br>- 詳細|
Time | Runbook ジョブが実行された日付と時刻。|

**JobLogs** または **JobStreams** のカテゴリ レコードを取得する何らかのログ検索を実行するときは、**JobLogs** ビューまたは **JobStreams** ビューを選択すると、検索から返された最新情報が一連のタイルに集約されて表示されます。

## <a name="sample-log-searches"></a>サンプル ログ検索

以下の表は、このソリューションによって収集されたジョブ レコードを探すログ検索の例です。 

クエリ | [説明]|
----------|----------|
正常に終了した Runbook ScheduledStartStop_Parent のジョブを検索する | search Category == "JobLogs" &#124; where ( RunbookName_s == "ScheduledStartStop_Parent" ) &#124; where ( ResultType == "Completed" )  &#124; summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) &#124; sort by TimeGenerated desc|
正常に終了した Runbook SequencedStartStop_Parent のジョブを検索する | search Category == "JobLogs" &#124; where ( RunbookName_s == "SequencedStartStop_Parent" ) &#124; where ( ResultType == "Completed" )  &#124; summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) &#124; sort by TimeGenerated desc

## <a name="removing-the-solution"></a>ソリューションの削除

ソリューションを使用する必要がなくなった場合、ソリューションを Automation アカウントから削除できます。  ソリューションを削除すると、Runbook のみが削除されます。 ソリューションの追加時に作成されたソリューションまたは変数は削除されません。  これらの資産は、他の Runbook で使用していない限り、手動で削除する必要があります。  

ソリューションを削除するには、次の手順を実行します。

1.  Automation アカウントから、左側のウィンドウで **[ワークスペース]** を選択します。  
2.  **[ソリューション]** ページで、ソリューション **Start-Stop-VM[ワークスペース]** を選択します。  **VMManagementSolution[ワークスペース]** ページで、メニューから **[削除]** を選択します。<br><br> ![VM 管理ソリューションの削除](media/automation-solution-vm-management/vm-management-solution-delete.png)
3.  **[ソリューションの削除]** ウィンドウで、ソリューションを削除するかどうかを確定します。
4.  情報が検証され、ソリューションが作成されている間、メニューの **[通知]** でその進行状況を追跡することができます。  ソリューションの削除プロセスが開始すると、**[ソリューション]** ページに戻ります。  

Automation アカウントと Log Analytics ワークスペースは、このプロセスの一部として削除されません。  Log Analytics ワークスペースを保持しない場合は、これを手動で削除する必要があります。  これは、Azure Portal から行うことができます。

1.    Azure Portal ホーム画面で **[Log Analytics]** を選択します。
2. **[Log Analytics]** ウィンドウで、ワークスペースを選択します。
3. ワークスペースの設定ウィンドウのメニューから **[削除]** を選択します。  
      
## <a name="next-steps"></a>次の手順

- 各種検索クエリの作成方法と、Log Analytics での Automation ジョブ ログの確認方法の詳細については、[Log Analytics でのログ検索](../log-analytics/log-analytics-log-searches.md)に関するページを参照してください。
- Runbook の実行、Runbook ジョブの監視方法、その他の技術的な詳細については、[Runbook ジョブの追跡](automation-runbook-execution.md)に関するページを参照してください。
- Log Analytics とデータ収集ソースの詳細については、[Log Analytics における Azure Storage データの収集](../log-analytics/log-analytics-azure-storage.md)に関するページをご覧ください。






   

