<properties
   pageTitle="JSON 形式のタグを使用して Azure VM の起動とシャットダウンのスケジュールを作成する | Microsoft Azure"
   description="この記事では、タグに関する JSON 文字列を使って VM の起動とシャットダウンのスケジュールを自動化する方法を示します。"
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/18/2016"
   ms.author="magoedte;paulomarquesc" />

# Azure Automation シナリオ - JSON 形式のタグを使用して Azure VM の起動とシャットダウンのスケジュールを作成する

サブスクリプション コストを削減し、ビジネス要件や技術要件に対応するために、仮想マシンの起動とシャットダウンのスケジュールを設定することを望むお客様が一般的です。

次のシナリオでは、Schedule というタグを使用して、Azure のリソース グループ レベルまたは仮想マシン レベルで VM の自動起動と自動シャットダウンを設定できます。このスケジュールは、起動時刻とシャットダウン時刻を指定して、日曜日から土曜日まで構成できます。すぐに使えるいくつかのオプションがあらかじめ用意されています。たとえば、スケールインとスケールアウトを可能にする[仮想マシン スケール セット](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)を自動スケール設定で使用したり、起動とシャットダウンの操作のスケジュールを設定する組み込み機能がある [DevTest Labs](../devtest-lab/devtest-lab-overview.md) サービスを使用したりできます。ただし、これらのオプションは特定のシナリオのみをサポートし、IaaS VM には適用できません。

Schedule タグをリソース グループに適用すると、タグはそのリソース グループ内のすべての仮想マシンに適用されます。いずれかの VM に直接適用されているスケジュールもある場合、前回のスケジュールが次の順序で優先されます。

1.  リソース グループに適用されているスケジュール
2.  リソース グループとリソース グループ内の仮想マシンに適用されているスケジュール
3.  仮想マシンに適用されているスケジュール

このシナリオでは主に、指定された形式の JSON 文字列を受け取り、それを Schedule というタグの値として追加します。Runbook ですべてのリソース グループと仮想マシンをリストし、上に示したシナリオに基づいて各 VM のスケジュールを識別します。その後、スケジュールがアタッチされた VM をループ処理し、どのアクション (停止、シャットダウン、または無視) を実行するか評価します。

これらの Runbook は、[Azure 実行アカウント](../automation/automation-sec-configure-azure-runas-account.md)を使用して認証します。

## シナリオの取得

このシナリオは 4 つの PowerShell Workflow Runbook で構成されています。このシナリオの Runbook は、[TechNet ギャラリー](https://gallery.technet.microsoft.com/Azure-Automation-Runbooks-84f0efc7)またはこのプロジェクトの [GitHub](https://github.com/paulomarquesdacosta/azure-automation-scheduled-shutdown-and-startup) リポジトリからダウンロードできます。

Runbook | 説明 
----------|----------
Test-ResourceSchedule | 各仮想マシンのスケジュールを確認します (スケジュールに応じて仮想マシンのシャットダウンまたは起動を実行する)
Add-ResourceSchedule | Schedule タグを VM またはリソース グループに追加します
Update-ResourceSchedule | 既存の Schedule タグを新しいタグで置き換えることによって変更します
Remove-ResourceSchedule | VM またはリソース グループから Schedule タグを削除します 


## このシナリオのインストールと構成

### Runbook をインストールして発行する

Runbook をダウンロードしたら、[Runbook のインポートの手順](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-Azure-Automation)を使用してインポートすることができます。Runbook が Automation アカウントに正常にインポートされたら、各 Runbook を発行します。


### Test-ResourceSchedule にスケジュールを追加する

この手順では、Test-ResourceSchedule Runbook に対してスケジュールを有効にします。これは、どの仮想マシンを起動、シャットダウン、またはそのまま維持するかをテストする Runbook です。

1. Azure ポータルで Automation アカウントを開き、**[Runbook]** タイルをクリックします。
2. **Test-ResourceSchedule** ブレードで、**[スケジュール]** タイルをクリックします。
3. **[スケジュール]** ブレードで、**[スケジュールの追加]** をクリックします。
4. **[スケジュール]** ブレードで **[スケジュールを Runbook にリンクします]** を選択し、**[スケジュール]** ブレードで **[新しいスケジュールの作成]** を選択します。
5.  **[新しいスケジュール]** ブレードで、このスケジュールの名前を入力します。例では、HourlyExecution です。
6. スケジュールの **[開始]** で、スケジュールの開始時刻を四捨五入した時間の増分値に設定します。
7. **[繰り返し]** を選択し、**[繰り返しの間隔]** で間隔として **[1 時間]** を選択します。
8. **[有効期限の設定]** が **[いいえ]** に設定されていることを確認し、**[作成]** をクリックして新しいスケジュールを保存します。
9. **[Runbook のスケジュール設定]** オプション ブレードで **[パラメーターと実行設定]** オプションを選択し、Test-ResourceSchedule の **[パラメーター]** ブレードで、**[SubscriptionName]** フィールドにサブスクリプションの名前を入力します。これは Runbook に必要な唯一のパラメーターです。完了したら **[OK]** をクリックします。
   

完了した Runbook のスケジュールは次のようになります。<br> ![構成済みの Test-ResourceSchedule Runbook](./media/automation-scenario-start-stop-vm-wjson-tags/automation-schedule-config.png)<br>

## JSON 形式の Schedule

このソリューションでは主に、指定された形式の JSON 文字列を受け取り、それを Schedule というタグの値として追加します。次に、Runbook ですべてのリソース グループと仮想マシンをリストし、各仮想マシンのスケジュールを確認した後、アタッチされたスケジュールで仮想マシンをループ処理し、何のアクションを実行するかを確認します。必要な形式を次の例に示します。

    {
       "TzId": "Eastern Standard Time", 
        "0": {  
           "S": "11",
           "E": "17" 
        },
        "1": {
           "S": "9",
           "E": "19"
        },
        "2": {
           "S": "9",
           "E": "19"
        },
    }

この構造体に関する詳細情報:

1. この JSON の構造体の形式は、Azure での 1 つのタグ値の文字数制限 256 文字に合わせて最適化されています。

2. *TzId* は、仮想マシンのタイム ゾーンを表します。この ID は、PowerShell セッションで .NET クラス TimeZoneInfo を使用し、**[System.TimeZoneInfo]::GetSystemTimeZones()** で取得できます。<br>

    ![PowerShell での GetSystemTimeZones](./media/automation-scenario-start-stop-vm-wjson-tags/automation-get-timzone-powershell.png)

    - 曜日は 0 から 6 までの数値で表します。値 0 は日曜日と等しくなります。
    - 開始時刻は、**S** 属性で表し、その値は 24 時間形式です。
    - シャットダウン終了始時刻は **E** 属性で表し、その値は 24 時間形式です。

    S と E の属性に値ゼロ (0) を指定した場合、仮想マシンは評価の時点で現在の状態のままになります。

3. 週の特定の曜日の評価をスキップする場合は、関連した曜日のセクションを追加しないでください。次の例では、月曜日だけが評価され、その他の曜日は無視されます。
   
        {
          "TzId": "Eastern Standard Time",
           "1": {
             "S": "11",
             "E": "17"
           }
        }

## リソース グループまたは仮想マシンのタグ付け

VM をシャットダウンするためには、VM または VM が配置されているリソース グループにタグを付ける必要があります。Schedule タグが付いていない仮想マシンは評価されないため、起動またはシャットダウンされることもありません。このソリューションを使用してリソース グループまたは VM にタグを付ける方法は 2 つあります。ポータルから直接付ける方法と、**Add-ResourceSchedule**、**Update-ResourceSchedule**、**Remove-ResourceSchedule** の Runbook を使用する方法です。

### ポータルからタグを付ける

ポータルで仮想マシンまたはリソース グループにタグを付ける方法は次のとおりです。

1. JSON 文字列をフラット化し、スペースがないことを確認します。JSON 文字列は次のようになります。

        {"TzId":"Eastern Standard Time","0":{"S":"11","E":"17"},"1":{"S":"9","E":"19"},"2": {"S":"9","E":"19"},"3":{"S":"9","E":"19"},"4":{"S":"9","E":"19"},"5":{"S":"9","E":"19"},"6":{"S":"11","E":"17"}}
   

2. [タグ] アイコンを選択して、このスケジュールを適用する VM またはリソース グループを選択します。<br>![VM タグ オプション](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-tag-option.png)
3. キー/値のペアの後に続けてタグを定義します。**[キー]** フィールドに「**Schedule**」と入力し、**[値]** フィールドに JSON 文字列を貼り付けたら、**[保存]** をクリックします。リソースのタグ一覧に新しいタグが表示されます。<br>![VM の Schedule タグ](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-schedule-tag.png)


### PowerShell からタグを付ける

インポートしたすべての Runbook には、スクリプトの先頭にヘルプ情報が含まれていて、PowerShell から直接 Runbook を実行する方法が説明されています。必須パラメーターを渡して PowerShell から Add-ScheduleResource Runbook と Update-ScheduleResource Runbook を呼び出し、ポータルの外部にある VM またはリソース グループの Schedule タグを作成または更新できます。

PowerShell でタグの作成、追加、および削除を行うには、まず [Azure 用の PowerShell 環境を設定する](../powershell-install-configure.md)必要があります。設定が完了した後、以下の手順を開始できます。

### PowerShell でスケジュールのタグを作成する

1. PowerShell セッションを開き、次のコマンドを実行して実行アカウントを認証し、サブスクリプションを指定します。
    
        Conn = Get-AutomationConnection -Name AzureRunAsConnection
        Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
        -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
        Select-AzureRmSubscription -SubscriptionName "MySubscription"
   
2. スケジュールのハッシュ テーブルを定義します。作成方法を次の例に示します。
    
        $schedule= @{ "TzId"="Eastern Standard Time"; "0"= @{"S"="11";"E"="17"};"1"= @{"S"="9";"E"="19"};"2"= @{"S"="9";"E"="19"};"3"= @{"S"="9";"E"="19"};"4"= @{"S"="9";"E"="19"};"5"= @{"S"="9";"E"="19"};"6"= @{"S"="11";"E"="17"}}

3. Runbook に必要なパラメーターを定義します。次の例では、VM を対象にしています。

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; `
        "VmName"="VM01";"Schedule"=$schedule}

    リソース グループにタグを付ける場合は、次のように $params ハッシュ テーブルから *VMName* パラメーターを削除します。

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; `
        "Schedule"=$schedule}

4. 次のパラメーターを指定して **Add-ResourceSchedule** Runbook を実行し、Schedule タグを作成します。

        Start-AzureRmAutomationRunbook -Name "Add-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"

5. リソース グループまたは仮想マシンのタグを更新する場合は、次のパラメーターを指定して **Update-ResourceSchedule** Runbook を実行します。

        Start-AzureRmAutomationRunbook -Name "Update-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"

### PowerShell でスケジュールのタグを削除する

1. PowerShell セッションを開き、次のコマンドを実行して実行アカウントを認証し、サブスクリプションを選択して指定します。
    
        Conn = Get-AutomationConnection -Name AzureRunAsConnection
        Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
        -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
        Select-AzureRmSubscription -SubscriptionName "MySubscription"

2. Runbook に必要なパラメーターを定義します。次の例では、VM を対象にしています。

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01" ` 
        ;"VmName"="VM01"}

    リソース グループからタグを削除する場合は、次のように $params ハッシュ テーブルから *VMName* パラメーターを削除します。

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"}

3. **Remove-ResourceSchedule** Runbook を実行して Schedule タグを削除します。

        Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"

4. リソース グループまたは仮想マシンのタグを更新する場合は、次のパラメーターを指定して **Remove-ResourceSchedule** Runbook を実行します。

        Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"


>[AZURE.NOTE] これらの Runbook (および仮想マシンの状態) を積極的に監視し、仮想マシンがシャットダウンされ、その後に起動されたことを確認するようお勧めします。

Azure ポータルで Runbook の [ジョブ] タイルを選択すると、**Test-ResourceSchedule** Runbook ジョブの詳細を表示できます。ジョブの概要として、入力パラメーターと出力ストリーム、さらにジョブに関する全般情報が表示されます。例外が発生した場合は、その情報も表示されます。[履歴] には、出力ストリームと警告およびエラー ストリームからのメッセージが表示されます。Runbook 実行の詳細な結果を表示するには、[出力] タイルを選択します。<br> ![Test-ResourceSchedule の出力](./media/automation-scenario-start-stop-vm-wjson-tags/automation-job-output.png)

## 次のステップ

-  PowerShell Workflow Runbook の使用を開始するには、「[最初の PowerShell Workflow Runbook](automation-first-runbook-textual.md)」を参照してください。
-  Runbook の種類とそれらの利点や制限事項の詳細については、「[Azure Automation の Runbook の種類](automation-runbook-types.md)」を参照してください。
-  PowerShell スクリプトのサポート機能の詳細については、[Azure Automation でのネイティブ PowerShell スクリプトのサポート](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)に関するブログを参照してください。
-  Runbook のログ記録と出力の詳細については、「[Azure Automation での Runbook の出力およびメッセージ](automation-runbook-output-and-messages.md)」を参照してください。
-  Azure 実行アカウントの詳細と、それを使用して Runbook を認証する方法については、「[Azure 実行アカウントを使用した Runbook の認証](../automation/automation-sec-configure-azure-runas-account.md)」を参照してください。

<!---HONumber=AcomDC_0720_2016-->