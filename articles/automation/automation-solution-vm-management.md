---
title: "ピーク時間外 VM 起動/停止ソリューション | Microsoft Docs"
description: "スケジュールに従って Azure Resource Manager 仮想マシンを起動/停止し、Log Analytics からプロアクティブに監視する VM 管理ソリューションについて説明します。"
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
ms.date: 11/22/2017
ms.author: magoedte
ms.openlocfilehash: 2ff2208f62c24c460c9d17533e28fd007549828b
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2017
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Azure Automation でのピーク時間外 VM 起動/停止ソリューション

ピーク時間外 VM 起動/停止ソリューションでは、ユーザー定義のスケジュールで Azure Virtual Machines を起動および停止し、Log Analytics によって洞察を深め、[SendGrid](https://azuremarketplace.microsoft.com/marketplace/apps/SendGrid.SendGrid?tab=Overview) を活用することで必要に応じて電子メールを送信します。 ほとんどのシナリオで Azure Resource Manager とクラシック VM の両方がサポートされます。 

このソリューションは、サーバーレスで低コストなリソースを活用してコストを削減する必要のあるお客様に、分散的なオートメーション機能を提供します。 次のような機能が含まれます。

* VM の起動/停止をスケジュールする
* Azure タグを使用して昇順で VM の起動/停止をスケジュールする (クラシック VM ではサポートされません)
* CPU 使用率の低さに基づいて VM を自動停止させる

## <a name="prerequisites"></a>前提条件

- [Azure 実行アカウント](automation-offering-get-started.md#authentication-methods)を使用した Runbook。  認証方法としては、実行アカウントの使用をお勧めします。有効期限が切れたり頻繁に変わったりするパスワードではなく証明書を使った認証が使用されます。  

- このソリューションで管理できる VM は、Automation アカウントと同じサブスクリプションに属している VM に限られます。  

- このソリューションがデプロイされる Azure リージョンは、オーストラリア南東部、カナダ中部、インド中部、米国東部、東日本、東南アジア、英国南部、西ヨーロッパのみです。  
    
    > [!NOTE]
    > VM のスケジュールを管理する Runbook は、任意のリージョンの VM を対象にできます。  

- VM の開始/停止の Runbook の完了時に電子メール通知を送信するには、Azure Marketplace からオンボード中に、**[はい]** を選択して SendGrid をデプロイする必要があります。 

    > [!IMPORTANT]
    > SendGrid はサード パーティのサービスです。SendGrid のサポートについては、[SendGrid](https://sendgrid.com/contact/) にお問い合わせください。  
    >
   
    SendGrid に関する制限は次のとおりです。

    * ユーザー、サブスクリプションごとに最大 1 つの SendGrid アカウント
    * サブスクリプションごとに最大 2 つの SendGrid アカウント

このソリューションの以前のバージョンをデプロイしている場合は、このリリースをデプロイする前に、まず以前のバージョンをアカウントから削除する必要があります。  

## <a name="solution-components"></a>ソリューションのコンポーネント

このソリューションには、構成済みの Runbook、スケジュール、Log Analytics との統合が含まれており、これらを使用することで仮想マシンのスタートアップとシャットダウンをビジネス要件に合うよう調整できます。 

### <a name="runbooks"></a>Runbook

次の表は、Automation アカウントにデプロイされる Runbook の一覧です。  Runbook のコードを変更することはお勧めできません。新しい機能が必要な場合は、独自の Runbook を記述してください。

> [!NOTE]
> 名前の末尾に "Child" が付いている Runbook は、直接実行しないでください。
>

すべての親 Runbook は *WhatIf* パラメーターを含みます。これが **True** に設定されている場合、*WhatIf* パラメーターなしで実行するときの Runbook の正確な動作の詳細な記述がサポートされ、正しい VM が対象となっていることが検証されます。  *WhatIf* パラメーターが **False** に設定されている場合、Runbook は定義されているアクションのみを実行します。 

|**Runbook** | **パラメーター** | **説明**|
| --- | --- | ---| 
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | 親 Runbook からのみ呼び出されます。 AutoStop シナリオでリソースごとにアラートを作成します。| 
|AutoStop_CreateAlert_Parent | WhatIf: True または False <br> VMList | 対象となるサブスクリプションまたはリソース グループ内の VM 上で、Azure アラート ルールを作成または更新します。 <br> VMList: VM のコンマ区切りリストです。  例: *vm1,vm2,vm3*| 
|AutoStop_Disable | なし | AutoStop アラートと既定のスケジュールを無効にします。| 
|AutoStop_StopVM_Child | WebHookData | 親 Runbook からのみ呼び出されます。 アラート ルールはこの Runbook を呼び出し、この Runbook が VM を停止させます。|  
|Bootstrap_Main | なし | 通常 Azure Resource Manager からアクセスできない WebhookURI などのブートストラップ構成を設定するために、1 度だけ使用されます。 この Runbook は、デプロイが正常に完了したら自動的に削除されます。|  
|ScheduledStartStop_Child | VMName <br> Action: Stop または Start <br> ResourceGroupName | 親 Runbook からのみ呼び出されます。 停止スケジュールの停止または起動を実際に実行します。|  
|ScheduledStartStop_Parent | Action: Stop または Start <br> WhatIf: True または False | サブスクリプション内のすべての VM に適用されます。ただし、**External_Start_ResourceGroupNames** と **External_Stop_ResourceGroupNames** を編集した場合は、これらのリソース グループのみを対象として実行するよう制限されます。 **External_ExcludeVMNames** 変数を更新することで、特定の VM を除外することもできます。 WhatIf は他の Runbook と同様に動作します。|  
|SequencedStartStop_Parent | Action: Stop または Start <br> WhatIf: True または False | 起動\\停止アクティビティの順序を指定する各 VM 上に、**SequenceStart** および **SequenceStop** という名前のタグを作成します。 タグの値は、起動\\停止させる順番 (昇順) と一致した、正の整数 (1、2、3) にする必要があります。 WhatIf は他の Runbook と同様に動作します。 <br> **注: 「Azure Automation 変数 の External_Start_ResourceGroupNames、External_Stop_ResourceGroupNames、および External_ExcludeVMNames で定義されたリソース グループ内に VM が存在する必要があります。また、アクションが適用されるためには VM に適切なタグが必要です。**|

### <a name="variables"></a>variables

次の表は、Automation アカウント内に作成される変数の一覧です。  **External** プレフィックスが付いた変数のみを変更することをお勧めします。**Internal** プレフィックスが付いた変数を変更すると、望ましくない効果が発生します。  

|**Variable** | **説明**|
---------|------------|
|External_AutoStop_Condition | これは、アラートをトリガーする前の条件を構成するために必要な条件演算子です。 指定できる値は、**GreaterThan**、**GreaterThanOrEqual**、**LessThan**、**LessThanOrEqual** です。|  
|External_AutoStop_Description | CPU % がしきい値を超えた場合に、VM を停止させるアラートを生成します。|  
|External_AutoStop_MetricName | Azure アラート ルールを構成する必要のあるパフォーマンス メトリックの名前| 
|External_AutoStop_Threshold | 変数 *External_AutoStop_MetricName* で指定される Azure アラート ルールのしきい値です。 パーセンテージの値の範囲は 1 ～ 100 です。|  
|External_AutoStop_TimeAggregationOperator | 条件を評価するために選択した時間枠のサイズに適用される、時間の集計演算子です。 指定できる値は、**Average**、**Minimum**、**Maximum**、**Total**、**Last** です。|  
|External_AutoStop_TimeWindow | Azure がアラートをトリガーするために選択したメトリックを分析する時間枠のサイズです。 このパラメーターは、timespan 形式で入力を受け入れます。 使用可能な値は、5 分 ～ 6 時間です。|  
|External_EmailFromAddress | 電子メールの送信者を指定します。|  
|External_EmailSubject | 電子メールの件名に使用するテキストを指定します。|  
|External_EmailToAddress | 電子メールの受信者を指定します。 コンマで名前を区切ります。|  
|External_ExcludeVMNames | 除外される VM の名前を入力します。スペースなしのコンマで名前を区切ります。|  
|External_IsSendEmail | 完了時に電子メール通知を送信するオプションを指定します。  **Yes** を指定するか、メールを送信しない場合は **No** を選択します。  最初のデプロイ時に SendGrid を作成していない場合は、このオプションを **No** にする必要があります。|  
|External_Start_ResourceGroupNames | 開始アクションの対象となる 1 つ以上のリソース グループを、コンマ区切り値で指定します。|  
|External_Stop_ResourceGroupNames | 停止アクションの対象となる 1 つ以上のリソース グループを、コンマ区切り値で指定します。|  
|Internal_AutomationAccountName | Automation アカウントの名前を指定します。|  
|Internal_AutoSnooze_WebhookUri | AutoStop シナリオで呼び出される Webhook URI を指定します。|  
|Internal_AzureSubscriptionId | Azure サブスクリプション ID を指定します。|  
|Internal_ResourceGroupName | Azure Automation アカウントのリソース グループ名を指定します。|  
|Internal_SendGridAccountName | SendGrid アカウント名を指定します。|  
|Internal_SendGridPassword | SendGrid アカウントのパスワードを指定します。|  

<br>

すべてのシナリオで、VM を対象とするために **External_Start_ResourceGroupNames**、**External_Stop_ResourceGroupNames**、**External_ExcludeVMNames** 変数が必要です。例外は、**AutoStop_CreateAlert_Parent** Runbook に対して VM のコンマ区切りリストを指定する場合です。 つまり、開始/停止アクションを発生させるためには、対象のリソース グループ内に VM が存在する必要があります。 このロジックは Azure Policy に少し似ています。サブスクリプションまたはリソース グループを対象にすることができ、アクションは新しく作成された VM に継承されます。 この方法では、VM ごとに個別のスケジュールを保持したり、スケールで起動/停止を管理したりする必要を回避できます。

### <a name="schedules"></a>スケジュール

次の表は、Automation アカウント内に作成される既定のスケジュールの一覧です。  それらを変更したり、独自のカスタム スケジュールを作成したりできます。  既定では、これらのスケジュールは **Scheduled_StartVM** と **Scheduled-StopVM** を除いて無効になっています。

すべてのスケジュールを有効にすることはお勧めしません。アクションを実行するスケジュール間で重複が生じる可能性があるためです。  代わりに、実行する必要がある最適化を決定し、それに応じてスケジュールを変更することが最善です。  詳細については、概要セクションのシナリオ例をご覧ください。   

|**スケジュール名** | **頻度** | **説明**|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 8 時間ごとに実行 | 8 時間ごとに AutoStop_CreateAlert_Parent Runbook を実行します。Runbook は Azure Automation 変数の "External_Start_ResourceGroupNames"、"External_Stop_ResourceGroupNames"、および "External_ExcludeVMNames" の値に基づいて VM を停止させます。  または、"VMList" パラメーターを使用して VM のコンマ区切りリストを指定できます。|  
|Scheduled_StopVM | ユーザー定義、毎日 | "Stop" パラメーターを持つ Scheduled_Parent Runbook を毎日指定された時刻に実行します。  アセット変数を介して定義されたルールを満たすすべての VM を自動的に停止させます。  姉妹スケジュールである Scheduled-StartVM を有効にすることをお勧めします。|  
|Scheduled_StartVM | ユーザー定義、毎日 | *Start* パラメーターを持つ Scheduled_Parent Runbook を毎日指定された時刻に実行します。  定義されたルールと適切な変数の一部を満たすすべての VM を、自動的に起動させます。  姉妹スケジュールである **Scheduled-StopVM** を有効にすることをお勧めします。|
|Sequenced-StopVM | 午前 1 時 00 分 (UTC)、毎週金曜日 | *Stop* パラメーターを持つ Sequenced_Parent Runbook を毎週金曜日の指定された時刻に実行します。  **SequenceStop** タグと適切な変数の一部を持つすべての VM を、順番に (昇順) 停止させます。  タグの値とアセット変数の詳細については、「Runbook」セクションをご覧ください。  姉妹スケジュールである **Sequenced-StartVM** を有効にすることをお勧めします。|
|Sequenced-StartVM | 午後 1 時 00 分 (UTC)、毎週月曜日 | *Start* パラメーターを持つ Sequenced_Parent Runbook を毎週月曜日の指定された時刻に実行します。  **SequenceStop** タグと適切な変数の一部を持つすべての VM を、順番に (降順) 起動させます。  タグの値とアセット変数の詳細については、「Runbook」セクションをご覧ください。  姉妹スケジュールである **Sequenced-StopVM** を有効にすることをお勧めします。|

<br>

## <a name="configuration"></a>構成

ピーク時間外 VM 起動/停止 [プレビュー] ソリューションをご利用の Automation アカウントに追加し、変数を設定してソリューションをカスタマイズするには、以下の手順を実行します。

1. Azure Portal で、**[新規]** をクリックします。<br> ![Azure ポータル](media/automation-solution-vm-management/azure-portal-01.png)<br>  
2. [Marketplace] ウィンドウで、「**Start VM**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 検索結果から **ピーク時間外 VM 起動/停止 [プレビュー]** を選択します。  
3. 選択したソリューションの **ピーク時間外 VM 起動/停止 [プレビュー]** ウィンドウで概要を確認し、**作成** をクリックします。  
4. **[ソリューションの追加]** ウィンドウが表示されます。Automation サブスクリプションにインポートする前に、ソリューションの設定を行うよう求められます。<br><br> ![VM 管理の ソリューションの追加 ブレード](media/automation-solution-vm-management/azure-portal-add-solution-01.png)<br><br>
5.  **[ソリューションの追加]** ブレードで **[ワークスペース]** を選択し、ここで Automation アカウントと同じ Azure サブスクリプションに関連付けられている OMS ワークスペースを選択するか、新しいワークスペースを作成します。  ワークスペースがない場合は、**[新しいワークスペースの作成]** を選択し、**[OMS Workspace (OMS ワークスペース)]** ウィンドウで次の手順を実行します。 
   - 新しい **OMS ワークスペース**の名前を指定します。
   - 関連付ける**サブスクリプション**をドロップダウン リストから選択します (既定値が適切でない場合)。
   - **[リソース グループ]** には、新しいリソース グループを作成するか、既にあるリソース グループを選択することができます。  
   - **[場所]**を選択します。  現在選択できる場所は、**オーストラリア南東部**、**カナダ中部**、**インド中部**、**米国東部**、**東日本**、**東南アジア**、**英国南部**、**西ヨーロッパ**のみです。
   - **[価格レベル]** を選択します。  このソリューションは、2 つのレベルで提供されています (Free レベルと OMS Paid レベル)。  Free レベルは、1 日に収集できるデータの量、リテンション期間、Runbook ジョブの実行時間 (分) に上限が設けられています。  OMS Paid レベルでは 1 日に収集できるデータの量に上限がありません。  

        > [!NOTE]
        > オプションとして GB あたりの (スタンドアロン) Paid レベルが表示されますが、適用することはできません。  サブスクリプション内でこのオプションを選択してこの記事のソリューションの作成を続行すると失敗します。  この問題は、このソリューションが正式にリリースされたときに解決されます。<br>このソリューションを使うと、Automation ジョブ分数とログの取り込みのみが使用されます。  ソリューションによって OMS ノードが環境に追加されることはありません。  

6. **[OMS workspace (OMS ワークスペース)]** ブレードに必要な情報を入力したら、**[作成]** をクリックします。  情報が検証され、ワークスペースが作成されている間、メニューの **[通知]** でその進行状況を追跡することができます。  その後、自動的に **[ソリューションの追加]** ブレードに戻ります。  
7. **[ソリューションの追加]** ブレードで **[Automation アカウント]** を選択します。  OMS ワークスペースを新たに作成する場合はさらに、先ほど指定した新しい OMS ワークスペース (Azure サブスクリプション、リソース グループ、リージョンを含む) に関連付ける新しい Automation アカウントを作成する必要があります。  **[Automation アカウントの作成]** を選択し、**[Automation アカウントの追加]** ブレードで次の情報を入力します。 
  - **[名前]** フィールドに、Automation アカウントの名前を入力します。

    それ以外のオプションはすべて、選択した OMS ワークスペースに基づいて自動的に入力されます。これらのオプションを変更することはできません。  このソリューションに含まれている Runbook は、Azure 実行アカウントが既定の認証方法になります。  **[OK]** をクリックすると、設定したオプションが検証され、Automation アカウントが作成されます。  進行状況は、メニューの **[通知]** で追跡できます。 

    それ以外の方法として、既存の Automation 実行アカウントを選択することもできます。  既に別の OMS ワークスペースに関連付けられているアカウントは選択できないので注意してください。選択すると、その旨を伝えるメッセージがブレードに表示されます。  既に関連付けられている場合は、別の Automation 実行アカウントを選択するか、新たに作成する必要があります。<br><br> ![Automation Account Already Linked to OMS Workspace](media/automation-solution-vm-management/vm-management-solution-add-solution-blade-autoacct-warning.png)<br>

8. 最後に、**[ソリューションの追加]** ブレードで **[構成]** を選択すると、**[パラメーター]** ブレードが表示されます。<br><br> ![ソリューションの [パラメーター] ウィンドウ](media/automation-solution-vm-management/azure-portal-add-solution-02.png)<br><br>  **[パラメーター]** ブレードでは、次の作業が求められます。  
   - **[Target ResourceGroup Names (ターゲット リソース グループ名)]** に、このソリューションで管理する VM を含んだリソース グループの名前を指定します。  複数の名前を入力する場合は、各名前をコンマで区切ってください (値の大文字と小文字は区別されません)。  ワイルドカードを使用して、サブスクリプション内の全リソース グループの VM を対象にすることもできます。
   - **VM 除外リスト (文字列)** を指定してください。対象のリソース グループから仮想マシンの名前を 1 つ以上指定します。  複数の名前を入力する場合は、各名前をコンマで区切ってください (値の大文字と小文字は区別されません)。  ワイルドカードの使用がサポートされています。
   - ターゲット リソース グループ内の VM を定期的に起動/停止する日時を **[スケジュール]** で選択します。  スケジュールは既定で UTC タイム ゾーンに構成され、別のリージョンを選ぶことはできません。  ソリューション構成後にスケジュールを特定のタイム ゾーンに構成したい場合は、後の「[開始と停止のスケジュールの変更](#modifying-the-startup-and-shutdown-schedule)」をご覧ください。
   - SendGrid から**電子メール通知**を受信するには、既定値 **[はい]** をそのまま使用し、有効なメール アドレスを指定します。  **[いいえ]** を選択した場合、後から電子メール通知を受信するには、Azure Marketplace からソリューションを再デプロイする必要があります。  

10. ソリューションに必要な初期設定が完了したら、**[作成]** を選択します。  すべての設定が検証された後、ご利用のサブスクリプションへのソリューションのデプロイが試行されます。  このプロセスは、完了までに数秒かかる場合があります。進行状況は、メニューの **[通知]** で確認してください。 

## <a name="collection-frequency"></a>収集の頻度

Automation ジョブのログとジョブのストリーム データは、5 分おきに Log Analytics リポジトリに取り込まれます。  

## <a name="using-the-solution"></a>ソリューションの使用

VM 管理ソリューションを追加すると、Azure Portal の Log Analytics ワークスペース内で、ダッシュボードに **StartStopVM ビュー** タイルが追加されます。  このタイルには、ソリューションに関して開始された Runbook ジョブと正常に完了した Runbook ジョブの数、およびそのグラフが表示されます。<br><br> ![VM 管理の [StartStopVM View (StartStopVM ビュー)] タイル](media/automation-solution-vm-management/vm-management-solution-startstopvm-view-tile.png)  

Automation アカウント内でソリューションにアクセスして管理するには、**ワークスペース** オプションを選択し、Log Analytics ページで左側のウィンドウから **[ソリューション]** を選択します。  **[ソリューション]** ページで、一覧からソリューション **Start-Stop-VM[ワークスペース]** を選択します。<br><br> ![Log Analytics のソリューション一覧](media/automation-solution-vm-management/azure-portal-select-solution-01.png)  

ソリューションを選択すると、**Start-Stop-VM[ワークスペース]** ソリューション ブレードが表示されて、重要な情報を確認できます。たとえば、Log Analytics ワークスペースの **[StartStopVM]** タイルと同様、そのソリューションに関して開始された Runbook ジョブと正常に完了した Runbook ジョブの数、およびそのグラフが表示されます。<br><br> ![Automation Update Management ソリューション ページ](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)  

ここからジョブ レコードの詳細な分析を実行することもできます。ドーナツ タイルをクリックするか、ソリューション ダッシュボードから、ジョブ履歴、定義済みのログ検索クエリを表示したり、Log Analytics Advanced ポータルに切り替えて検索クエリに基づいて検索したりできます。  

すべての親 Runbook は *WhatIf* パラメーターを含みます。これが **True** に設定されている場合、*WhatIf* パラメーターなしで実行するときの Runbook の正確な動作の詳細な記述がサポートされ、正しい VM が対象となっていることが検証されます。  *WhatIf* パラメーターが **False** に設定されている場合、Runbook は定義されているアクションのみを実行します。  


### <a name="scenario-1-daily-stopstart-vms-across-a-subscription-or-target-resource-groups"></a>シナリオ 1: サブスクリプションまたは対象のリソース グループ全体で毎日 VM を停止/起動させる 

これは、ソリューションを最初にデプロイするときの既定の構成です。  たとえば、退社する夕方にサブスクリプション全体のすべての VM を停止させ、出社する朝に VM を起動させるように構成できます。 デプロイ中にスケジュール **Scheduled-StartVM" と **Scheduled-StopVM** を構成すると、対象の VM が起動および停止されます。
>[!NOTE]
>スケジュール タイム パラメーターを構成するとき、タイム ゾーンは現在のタイム ゾーンになります。ただし、Azure Automation では UTC 形式で格納されます。  これはデプロイ中に処理されるので、タイム ゾーンの変換を行う必要はありません。

2 つの変数を構成することで、スコープに入る VM を制御できます (**External_Start_ResourceGroupNames**、**External_Stop_ResourceGroupNames、**External_ExcludeVMNames**)。  

>[!NOTE]
> 変数 **Target ResourceGroup Names**" の値は、変数 **External_Start_ResourceGroupNames** と変数 **External_Stop_ResourceGroupNames** の値として格納されます。 粒度を高めるためには、これらの変数をそれぞれ変更して、異なるリソース グループを対象にします。  代わりに、起動アクション用には **External_Start_ResourceGroupNames** を使用し、停止アクション用には **External_Stop_ResourceGroupNames** を使用します。 新しい VM が自動的に起動スケジュールと停止スケジュールに加えられます。

構成をテストして検証するには、手動で **ScheduledStartStop_Parent** Runbook を開始させ、*ACTION* パラメーターを **start** か **stop** に設定し、*WhatIf* パラメーターを **true** に設定します。<br><br> ![Runbook のパラメーターを構成する](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)<br> これにより、実行されるアクションをプレビューし、運用環境の VM に対して実装する前に必要に応じて変更を加えることができます。  準備ができたら、パラメーターを **false** にセットして手動で Runbook を実行するか、Automation のスケジュール **Schedule-StartVM** と **Schedule-StopVM** を指定のスケジュール通りに自動的に実行させることができます。

### <a name="scenario-2-sequence-the-stopstart-vms-across-a-subscription-using-tags"></a>シナリオ 2: タグを使用してサブスクリプション全体の VM の停止/起動の順序を指定する
分散ワークロードをサポートする複数の VM のコンポーネントを 2 つ以上含む環境では、コンポーネントが起動/停止される順序の指定をサポートすることが重要です。  これを実現するには、以下の手順を実行します。

1. **External_Start_ResourceGroupNames** と **External_Stop*ResourceGroupNames** 変数で指定されるサブスクリプション全体の VM に、**SequenceStart** と **SequenceStop** タグを正の整数値で追加します。  起動アクションと停止アクションは昇順で実行されます。  VM にタグを付ける方法については、[Azure で Windows 仮想マシンにタグを付ける方法](../virtual-machines/windows/tag.md)と[Azure で Linux 仮想マシンにタグを付ける方法](../virtual-machines/linux/tag.md)に関するページをご覧ください
2. スケジュール **Sequenced-StartVM** と **Sequenced-StopVM** の日付と時刻を要件を満たすように変更し、スケジュールを有効にします。  

構成をテストして検証するには、手動で **SequencedStartStop_Parent** Runbook を開始させ、*ACTION* パラメーターを **start** か **stop** に設定し、*WhatIf* パラメーターを **True** に設定します。<br><br> ![Runbook のパラメーターを構成する](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)<br> これにより、実行されるアクションをプレビューし、運用環境の VM に対して実装する前に必要に応じて変更を加えることができます。  準備ができたら、パラメーターを **false** にセットして手動で Runbook を実行するか、Automation のスケジュール **Sequenced-StartVM** と **Sequenced-StopVM** を指定のスケジュール通りに自動的に実行させることができます。  

### <a name="scenario-3-auto-stopstart-vms-across-a-subscription-based-on-cpu-utilization"></a>シナリオ 3: CPU 使用率に基づいてサブスクリプション全体で VM を自動的に停止/起動させる
このソリューションは、営業時間外などの非ピーク期間中に使用されていない Azure VM を評価し、プロセッサ使用率が x % 未満の場合は自動的にそれらをシャットダウンすることで、サブスクリプションの VM の稼働コストを管理するのに役立ちます  

既定では、ソリューションは CPU の割合メトリックで平均使用率が 5% 以下であるかどうかを評価するよう事前構成されています。  これは次の変数によって制御され、既定値が要件を満たしていない場合には変更することができます。

* External_AutoStop_MetricName
* External_AutoStop_Threshold
* External_AutoStop_TimeAggregationOperator
* External_AutoStop_TimeWindow

サブスクリプションまたはリソース グループに対してアクションを指定するか、VM のリストを指定することができますが、両方を有効にすることはできません。  

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>サブスクリプションとリソース グループに対して停止アクションを指定する

1. **External_Stop_ResourceGroupNames** 変数と **External_ExcludeVMNames** 変数を構成して対象の VM を指定します。  
2. **Schedule_AutoStop_CreateAlert_Parent** スケジュールを有効にして更新します。
3. *ACTION* パラメーターを **start** に設定し、変更をプレビューするために *WhatIf* パラメーターを **True** に設定して、**AutoStop_CreateAlert_Parent** Runbook を実行します。

#### <a name="target-stop-action-by-vm-list"></a>VM リストによって停止アクションを指定する

1. *ACTION* パラメーターを **start** に設定し、*VMList* パラメーターに VM のコンマ区切りリストを加え、変更をプレビューするために *WhatIf* パラメーターを **True** に設定して、**AutoStop_CreateAlert_Parent** Runbook を実行します。  
2. **External_ExcludeVMNames** パラメーターを、VM のコンマ区切りリスト (VM1,VM2,VM3) で構成します。
3. このシナリオでは、**External_Start_ResourceGroupNames** 変数と **External_Stop_ResourceGroupnames** 変数は考慮されていません。  このシナリオでは、独自の Automation のスケジュールを作成する必要があります。 詳細については、「[Azure Automation の Runbook をスケジュール設定する](../automation/automation-schedules.md)」をご覧ください。

これで CPU 使用率に基づいて VM を停止させるスケジュールが完成したので、以下のスケジュールの 1 つを有効にして開始させる必要があります。

* サブスクリプションとリソース グループに対して起動アクションを指定します。  **Scheduled-StartVM** スケジュールのテストと有効化については、[シナリオ 1](#scenario-1:-daily-stop/start-vms-across-a-subscription-or-target-resource-groups) の手順をご覧ください。
* サブスクリプション、リソース グループ、およびタグによって起動アクションを指定します。  "Sequenced-StartVM" スケジュールのテストと有効化については、[シナリオ 2](#scenario-2:-sequence-the-stop/start-vms-across-a-subscription-using-tags) の手順をご覧ください。


### <a name="configuring-e-mail-notifications"></a>電子メール通知の構成

ソリューションのデプロイ後に電子メール通知を構成するには、次の 3 つの変数を変更します。

* External_EmailFromAddress - 送信者のメール アドレスを指定します
* External_EmailToAddress - 通知メールを受信するメール アドレスのコンマ区切りリスト (user@hotmail.com,user@outlook.com) です
* External_IsSendEmail - 電子メールを受信する場合は **Yes** に、メール通知を無効にする場合は **No** に設定します。   


### <a name="modifying-the-startup-and-shutdown-schedules"></a>起動および停止スケジュールの変更

このソリューションの起動および停止スケジュールを管理するには、「[Azure Automation の Runbook をスケジュール設定する](automation-schedules.md)」で説明されている手順に従います。     

## <a name="log-analytics-records"></a>Log Analytics のレコード

Automation により 2 種類のレコードが OMS リポジトリに作成されます。

### <a name="job-logs"></a>ジョブ ログ

プロパティ | Description|
----------|----------|
Caller |  操作を開始したユーザー。  スケジュールされたジョブのシステムまたは電子メール アドレスが記録されます。|
カテゴリ | データの種類の分類。  Automation の場合、値は JobLogs です。|
CorrelationId | GUID。Runbook ジョブの関連付け ID です。|
JobId | GUID。Runbook ジョブの ID です。|
operationName | Azure で実行された操作の種類を指定します。  Automation の場合、値はジョブになります。|
resourceId | Azure のリソースの種類を指定します。  Automation の場合、値は Runbook に関連付けられている Automation アカウントです。|
ResourceGroup | Runbook ジョブのリソース グループ名を指定します。|
ResourceProvider | デプロイと管理の対象となるリソースのプロバイダー (Azure サービス) を指定します。  Automation の場合、その値は Azure Automation になります。|
ResourceType | Azure のリソースの種類を指定します。  Automation の場合、値は Runbook に関連付けられている Automation アカウントです。|
resultType | Runbook ジョブの状態。  次のいずれかの値になります。<br>- 開始済み<br>- 停止済み<br>- 中断<br>- 失敗<br>- 成功|
resultDescription | Runbook ジョブの結果の状態について説明します。  次のいずれかの値になります。<br>- ジョブが開始されました<br>- ジョブが失敗しました<br>- ジョブが完了しました|
RunbookName | Runbook の名前を指定します。|
SourceSystem | 送信されたデータのソース システムを指定します。  Automation の場合、値は OpsManager になります。|
StreamType | イベントの種類を指定します。 次のいずれかの値になります。<br>- 詳細<br>- 出力<br>- エラー<br>- 警告|
SubscriptionId | ジョブのサブスクリプション ID を指定します。
Time | Runbook ジョブが実行された日付と時刻。|


### <a name="job-streams"></a>ジョブ ストリーム

プロパティ | Description|
----------|----------|
Caller |  操作を開始したユーザー。  スケジュールされたジョブのシステムまたは電子メール アドレスが記録されます。|
カテゴリ | データの種類の分類。  Automation の場合、値は JobStreams です。|
JobId | GUID。Runbook ジョブの ID です。|
operationName | Azure で実行された操作の種類を指定します。  Automation の場合、値はジョブになります。|
ResourceGroup | Runbook ジョブのリソース グループ名を指定します。|
resourceId | Azure のリソース ID を指定します。  Automation の場合、値は Runbook に関連付けられている Automation アカウントです。|
ResourceProvider | デプロイと管理の対象となるリソースのプロバイダー (Azure サービス) を指定します。  Automation の場合、その値は Azure Automation になります。|
ResourceType | Azure のリソースの種類を指定します。  Automation の場合、値は Runbook に関連付けられている Automation アカウントです。|
resultType | イベントが生成された時点における Runbook ジョブの結果。  次のいずれかの値になります。<br>- 処理中|
resultDescription | Runbook からの出力ストリームが含まれます。|
RunbookName | Runbook の名前。|
SourceSystem | 送信されたデータのソース システムを指定します。  Automation の場合、値は OpsManager になります。|
StreamType | ジョブ ストリームの種類。 次のいずれかの値になります。<br>- 進行状況<br>- 出力<br>- 警告<br>- エラー<br>- デバッグ<br>- 詳細|
Time | Runbook ジョブが実行された日付と時刻。|

**JobLogs** カテゴリまたは **JobStreams** カテゴリのレコードを取得する何らかのログ検索を実行するときは、**JobLogs** ビューまたは **JobStreams** ビューを選択すると、検索から返された最新情報が一連のタイルに集約されて表示されます。

## <a name="sample-log-searches"></a>サンプル ログ検索

以下の表は、このソリューションによって収集されたジョブ レコードを探すログ検索の例です。 

クエリ | 説明|
----------|----------|
正常に完了した Runbook ScheduledStartStop_Parent のジョブを検索する | search Category == "JobLogs" &#124; where ( RunbookName_s == "ScheduledStartStop_Parent" ) &#124; where ( ResultType == "Completed" )  &#124; summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) &#124; sort by TimeGenerated desc|
正常に完了した Runbook SequencedStartStop_Parent のジョブを検索する | search Category == "JobLogs" &#124; where ( RunbookName_s == "SequencedStartStop_Parent" ) &#124; where ( ResultType == "Completed" )  &#124; summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) &#124; sort by TimeGenerated desc

## <a name="removing-the-solution"></a>ソリューションの削除

ソリューションを使用する必要がなくなった場合、ソリューションを Automation アカウントから削除できます。  ソリューションを削除すると、Runbook のみが削除され、ソリューションの追加時に作成されたソリューションまたは変数は削除されません。  これらの資産は、他の Runbook で使用していない限り、手動で削除する必要があります。  

ソリューションを削除するには、次の手順を実行します。

1.  Automation アカウントから、左側のウィンドウで **[ワークスペース]** を選択します。  
2.  **[ソリューション]** ページで、ソリューション **Start-Stop-VM[ワークスペース]** を選択します。  **VMManagementSolution[ワークスペース]** ページで、メニューから **[削除]** をクリックします。<br><br> ![VM 管理ソリューションの削除](media/automation-solution-vm-management/vm-management-solution-delete.png)
3.  **[ソリューションの削除]** ウィンドウで、ソリューションを削除するかどうかを確定します。
4.  情報が検証され、ソリューションが作成されている間、メニューの **[通知]** でその進行状況を追跡することができます。  ソリューションの削除プロセスが開始すると、**[ソリューション]** ページに戻ります。  

Automation アカウントと Log Analytics ワークスペースは、このプロセスの一部として削除されません。  Log Analytics ワークスペースを保持しない場合は、これを手動で削除する必要があります。  これは、Azure Portal からも行えます。   Azure Portal のホーム画面から **[Log Analytics]** を選択し、**[Log Analytics]** ブレードでワークスペースを選択して、ワークスペースの設定ブレードのメニューから **[削除]** をクリックします。  
      
## <a name="next-steps"></a>次のステップ

- 各種検索クエリの作成方法と、Log Analytics での Automation ジョブの確認方法の詳細については、「 [Log Analytics におけるログの検索](../log-analytics/log-analytics-log-searches.md)
- Runbook の実行、Runbook ジョブの監視方法、その他の技術的な詳細については、 [Runbook ジョブの追跡](automation-runbook-execution.md)
- Log Analytics とデータ収集ソースの詳細については、「[Log Analytics における Azure Storage データの収集について](../log-analytics/log-analytics-azure-storage.md)」をご覧ください。






   

