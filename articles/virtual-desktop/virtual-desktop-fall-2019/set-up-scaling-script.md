---
title: Azure Automation によるセッション ホストのスケーリング - Azure
description: Azure Automation を使用して Windows Virtual Desktop のセッション ホストを自動的にスケーリングする方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f659a40cbb9e3ef2d0e7fe4e527518a76507d5ee
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745704"
---
# <a name="scale-session-hosts-using-azure-automation"></a>Azure Automation を使用してセッション ホストをスケーリングする

>[!IMPORTANT]
>この記事の内容は、Azure Resource Manager Windows Virtual Desktop オブジェクトをサポートしていない Fall 2019 リリースに適用されます。

仮想マシン (VM) をスケーリングすると、Windows Virtual Desktop の総デプロイ コストを削減できます。 これは、ピーク時以外の使用時間帯にセッション ホスト VM をシャットダウンして割り当て解除し、ピーク時間帯に再びオンにして再割り当てすることを意味します。

この記事では、Windows Virtual Desktop 環境でセッション ホスト仮想マシンを自動的にスケーリングする、Azure Automation および Azure Logic Apps で構築されたスケーリング ツールについて説明します。 スケーリング ツールの使用方法を確認するには、「[前提条件](#prerequisites)」に進んでください。

## <a name="report-issues"></a>レポートに関する問題

スケーリング ツールの問題レポートは、現在、Microsoft サポートではなく GitHub で処理されています。 スケーリング ツールで何か問題が発生した場合は、[RDS の GitHub ページ](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4a-WVD-scaling-logicapps)で "4a-WVD-scaling-logicapps" というラベルの GitHub 問題を開いてレポートできます。

## <a name="how-the-scaling-tool-works"></a>スケーリング ツールのしくみ

スケーリング ツールでは、セッション ホスト VM のコストを最適化したいお客様のために低コストの自動化オプションを提供しています。

スケーリング ツールを使用すると、次の操作を行えます。
 
- ピーク時とピーク時以外の営業時間に基づいて、VM の起動と停止をスケジュールします。
- CPU コアあたりのセッション数に基づいて VM をスケールアウトします。
- ピーク時以外の時間帯に VM をスケールインして、最低限の数のセッション ホスト VM だけを実行状態のままにします。

スケーリング ツールは、Azure Automation PowerShell Runbook、Webhook、Azure Logic Apps を組み合わせて使用することで機能します。 ツールが実行されると、Azure Logic Apps によって Webhook が呼び出されて Azure Automation Runbook が起動します。 その後、Runbook によってジョブが作成されます。

ピーク時の使用時間帯は、このジョブによって現在のセッション数と現在実行中のセッション ホストの VM 容量が、ホスト プールごとにチェックされます。 この情報を使用して、実行中のセッション ホスト VM が既存のセッションをサポートできるかどうかが、**createazurelogicapp.ps1** ファイルに定義された *SessionThresholdPerCPU* パラメーターに基づいて計算されます。 セッション ホスト VM が既存のセッションをサポートできない場合は、このジョブによってホスト プール内の追加のセッション ホスト VM が起動されます。

>[!NOTE]
>*SessionThresholdPerCPU* では、VM 上のセッション数は制限されません。 このパラメーターは、接続を負荷分散するために、どのタイミングで新しい VM を起動する必要があるかを決めるだけのものです。 セッションの数を制限するには、「[Set-RdsHostPool](/powershell/module/windowsvirtualdesktop/set-rdshostpool/)」の手順に従って、*MaxSessionLimit* パラメーターを適切に構成する必要があります。

ピーク時以外の使用時間帯は、*MinimumNumberOfRDSH* パラメーターに基づいて、シャットダウンすべきセッション ホスト VM がジョブによって特定されます。 新しいセッションがホストに接続できないよう、セッション ホスト VM はドレイン モードに設定されます。 *LimitSecondsToForceLogOffUser* パラメーターを 0 以外の正の値に設定した場合は、ジョブによって、現在サインインしているユーザーは作業内容を保存するよう通知され、構成された時間待機した後、強制的にサインアウトされます。セッション ホスト VM 上のすべてのユーザー セッションがサインアウトされると、ジョブによって VM がシャットダウンされます。

*LimitSecondsToForceLogOffUser* パラメーターを 0 に設定した場合は、指定されたグループ ポリシー内のセッション構成設定で、ユーザー セッションのサインオフを処理できるようになります。 これらのグループ ポリシーを確認するには、 **[コンピューターの構成]**  >  **[ポリシー]**  >  **[管理用テンプレート]**  >  **[Windows コンポーネント]**  >  **[ターミナル サービス]**  >  **[ターミナル サーバー]**  >  **[セッションの時間制限]** にアクセスしてください。 セッション ホスト VM にアクティブなセッションが存在する場合は、ジョブによってセッション ホスト VM は実行状態のままとなります。 アクティブなセッションが存在しない場合は、ジョブによってセッション ホスト VM はシャットダウンされます。

ジョブは、設定された繰り返し間隔に基づいて定期的に実行されます。 Windows Virtual Desktop 環境のサイズに基づいてこの間隔を変更できますが、仮想マシンの起動とシャットダウンには時間がかかることがあるため、遅延を考慮してください。 繰り返し間隔を 15 分に設定することをお勧めします。

ただし、このツールには次の制限事項もあります。

- このソリューションは、プールされたセッション ホスト VM にのみ適用されます。
- このソリューションは任意のリージョンの VM を管理しますが、Azure Automation アカウントおよび Azure Logic Apps と同じサブスクリプションでのみ使用できます。

>[!NOTE]
>スケーリング ツールでは、スケーリングするホスト プールの負荷分散モードを制御します。 ピーク時とピーク時以外の両方に対して、幅優先の負荷分散に設定されます。

## <a name="prerequisites"></a>前提条件

スケーリング ツールの設定を開始する前に、次の準備ができていることを確認してください。

- [Windows Virtual Desktop のテナントとホスト プール](create-host-pools-arm-template.md)
- 構成されて Windows Virtual Desktop サービスに登録されたセッション ホスト プール VM
- Azure サブスクリプション対する[共同作成者のアクセス権](../../role-based-access-control/role-assignments-portal.md)を持っているユーザー

ツールのデプロイに使用するマシンには、次のものが必要です。 

- Windows PowerShell 5.1 以降
- Microsoft Az PowerShell モジュール

すべての準備が整ったら、始めましょう。

## <a name="create-an-azure-automation-account"></a>Azure Automation アカウントを作成する

まず、PowerShell Runbook を実行するために、Azure Automation アカウントが必要です。 アカウントの設定方法は次のとおりです。

1. Windows PowerShell を管理者として開きます。
2. 次のコマンドレットを実行して、Azure アカウントにサインインします。

     ```powershell
     Login-AzAccount
     ```

     >[!NOTE]
     >アカウントには、スケーリング ツールをデプロイする Azure サブスクリプションに対する共同作成者の権限が必要です。

3. 次のコマンドレットを実行して、Azure Automation アカウントを作成するためのスクリプトをダウンロードします。

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazureautomationaccount.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazureautomationaccount.ps1"
     ```

4. 次のコマンドレットを実行して、スクリプトを実行し、Azure Automation アカウントを作成します。

     ```powershell
     .\createazureautomationaccount.ps1 -SubscriptionID <azuresubscriptionid> -ResourceGroupName <resourcegroupname> -AutomationAccountName <name of automation account> -Location "Azure region for deployment"
     ```

5. コマンドレットの出力には、Webhook の URI が含まれています。 Azure ロジック アプリの実行スケジュールを設定するときにパラメーターとして使用するため、必ずこの URI を記録しておいてください。

6. Azure Automation アカウントの設定が完了したら、Azure サブスクリプションにサインインし、次の図に示すように、指定のリソースグループに Azure Automation アカウントと関連する Runbook が表示されていることを確認します。

![新しく作成した Automation アカウントと Runbook を示す Azure の概要ページの画像。](../media/automation-account.png)

  Webhook が必要な場所にあるかどうかを確認するには、Runbook の名前を選択します。 次に、Runbook の Resources セクションに移動して、 **[Webhooks]** を選択します。

## <a name="create-an-azure-automation-run-as-account"></a>Azure Automation の実行アカウントを作成する

これで Azure Automation アカウントの用意ができたので、Azure リソースにアクセスするために Azure Automation の実行アカウントも作成する必要があります。

[Azure Automation の実行アカウント](../../automation/manage-runas-account.md)は、Azure コマンドレットを使用して Azure のリソースを管理するための認証を提供します。 実行アカウントを作成すると、新しいサービス プリンシパル ユーザーが Azure Active Directory に作成され、サブスクリプション レベルでこのユーザーに共同作成者ロールが割り当てられます。Azure 実行アカウントは、資格情報オブジェクトにユーザー名とパスワードを保存しなくても、証明書とサービス プリンシパル名で安全に認証できる優れた方法です。 実行アカウントの認証の詳細については、「[実行アカウントのアクセス許可の制限](../../automation/manage-runas-account.md#limit-run-as-account-permissions)」を参照してください。

サブスクリプション管理者ロールのメンバーであり、サブスクリプションの共同管理者であるユーザーは、次のセクションの手順に従って実行アカウントを作成できます。

Azure アカウントで実行アカウントを作成するには、次の操作を行います。

1. Azure Portal で **[すべてのサービス]** を選択します。 リソースの一覧で、 **[Automation アカウント]** を入力して選択します。

2. **[Automation アカウント]** ページで、自分の Automation アカウントの名前を選択します。

3. ウィンドウの左側のペインで、[アカウント設定] セクションの **[実行アカウント]** を選択します。

4. **[Azure 実行アカウント]** を選択します。 **[Azure 実行アカウントを追加する]** ウィンドウが表示されたら、概要情報を確認した後で **[作成]** を選択して、アカウントの作成プロセスを開始します。

5. Azure で実行アカウントが作成されるまで数分待ちます。 メニューの [通知] で作成の進行状況を追跡できます。

6. 作成プロセスが完了すると、指定の Automation アカウントに AzureRunAsConnection という名前の資産が作成されます。 この接続資産には、アプリケーション ID、テナント ID、サブスクリプション ID、証明書の拇印が格納されます。 アプリケーション ID は後で使用するため、覚えておいてください。

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Windows Virtual Desktop にロールの割り当てを作成する

次に、AzureRunAsConnection が Windows Virtual Desktop と対話できるように、ロールの割り当てを作成する必要があります。 必ず PowerShell を使用して、ロールの割り当てを作成するアクセス許可を持つアカウントでサインインしてください。

まず、PowerShell セッション内で使用する [Windows Virtual Desktop PowerShell モジュール](/powershell/windows-virtual-desktop/overview/)をダウンロードしてインポートします (まだ行っていない場合)。 次の PowerShell コマンドレットを実行して、Windows Virtual Desktop に接続し、テナントを表示します。

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

Get-RdsTenant
```

スケーリングするホスト プールを含むテナントを見つけたら、「[Azure Automation アカウントを作成する](#create-an-azure-automation-account)」の手順に従い、前のコマンドレットで取得したテナント名を次のコマンドレットに使用して、ロールの割り当てを作成 します。

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId <applicationid> -TenantName <tenantname>
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Azure ロジック アプリと実行スケジュールを作成する

最後に、Azure ロジック アプリを作成し、新しいスケーリング ツールの実行スケジュールを設定する必要があります。

1.  Windows PowerShell を管理者として開きます。

2.  次のコマンドレットを実行して、Azure アカウントにサインインします。

     ```powershell
     Login-AzAccount
     ```

3. 次のコマンドレットを実行して、ローカル コンピューターに createazurelogicapp.ps1 スクリプト ファイルをダウンロードします。

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazurelogicapp.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazurelogicapp.ps1"
     ```

4. 次のコマンドレットを実行して、RDS 所有者または RDS 共同作成者のアクセス許可を持つアカウントを使用して Windows Virtual Desktop にサインインします。

     ```powershell
     Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
     ```

5. 次の PowerShell スクリプトを実行して、Azure ロジック アプリと実行スケジュールを作成します。

     ```powershell
     $aadTenantId = (Get-AzContext).Tenant.Id
     
     $azureSubscription = Get-AzSubscription | Out-GridView -PassThru -Title "Select your Azure Subscription"
     Select-AzSubscription -Subscription $azureSubscription.Id
     $subscriptionId = $azureSubscription.Id
     
     $resourceGroup = Get-AzResourceGroup | Out-GridView -PassThru -Title "Select the resource group for the new Azure Logic App"
     $resourceGroupName = $resourceGroup.ResourceGroupName
     $location = $resourceGroup.Location
     
     $wvdTenant = Get-RdsTenant | Out-GridView -PassThru -Title "Select your WVD tenant"
     $tenantName = $wvdTenant.TenantName
     
     $wvdHostpool = Get-RdsHostPool -TenantName $wvdTenant.TenantName | Out-GridView -PassThru -Title "Select the host pool you'd like to scale"
     $hostPoolName = $wvdHostpool.HostPoolName
     
     $recurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"
     $beginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"
     $endPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"
     $timeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"
     $sessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"
     $minimumNumberOfRdsh = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"
     $limitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, users will be signed out immediately"
     $logOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"
     $logOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"
     
     $automationAccount = Get-AzAutomationAccount -ResourceGroupName $resourceGroup.ResourceGroupName | Out-GridView -PassThru
     $automationAccountName = $automationAccount.AutomationAccountName
     $automationAccountConnection = Get-AzAutomationConnection -ResourceGroupName $resourceGroup.ResourceGroupName -AutomationAccountName $automationAccount.AutomationAccountName | Out-GridView -PassThru -Title "Select the Azure RunAs connection asset"
     $connectionAssetName = $automationAccountConnection.Name
     
     $webHookURI = Read-Host -Prompt "Enter the URI of the WebHook returned by when you created the Azure Automation Account"
     $maintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don't want to be managed by this scaling tool"

     .\createazurelogicapp.ps1 -ResourceGroupName $resourceGroupName `
       -AADTenantID $aadTenantId `
       -SubscriptionID $subscriptionId `
       -TenantName $tenantName `
       -HostPoolName $hostPoolName `
       -RecurrenceInterval $recurrenceInterval `
       -BeginPeakTime $beginPeakTime `
       -EndPeakTime $endPeakTime `
       -TimeDifference $timeDifference `
       -SessionThresholdPerCPU $sessionThresholdPerCPU `
       -MinimumNumberOfRDSH $minimumNumberOfRdsh `
       -LimitSecondsToForceLogOffUser $limitSecondsToForceLogOffUser `
       -LogOffMessageTitle $logOffMessageTitle `
       -LogOffMessageBody $logOffMessageBody `
       -Location $location `
       -ConnectionAssetName $connectionAssetName `
       -WebHookURI $webHookURI `
       -AutomationAccountName $automationAccountName `
       -MaintenanceTagName $maintenanceTagName
     ```

     スクリプトを実行すると、次の図に示すように、ロジック アプリがリソース グループに表示されます。

     ![Azure ロジック アプリの例を表す概要ページの画像。](../media/logic-app.png)

繰り返し間隔やタイム ゾーンを変更するなど、実行スケジュールに変更を加えるには、自動スケーリングのスケジューラにアクセスし、 **[編集]** を選択して Logic Apps デザイナーに移動します。

![Logic Apps デザイナーの画像。 繰り返し時間と Webhook ファイルを編集できる [繰り返し] メニューと [Webhook] メニューが開いています。](../media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>スケーリング ツールを管理する

スケーリング ツールの作成が完了したので、その出力にアクセスできます。 ここでは、役に立つと思われるいくつかの機能について説明します。

### <a name="view-job-status"></a>ジョブの状態を見る

すべての Runbook ジョブの状態の概要を表示したり、Azure portal で特定の Runbook ジョブの詳細な状態を表示したりできます。

選択した Automation アカウントの右側にある [ジョブの統計情報] で、すべての Runbook ジョブの概要の一覧を表示できます。 ウィンドウの左側にある **[ジョブ]** ページを開くと、現在のジョブの状態、開始時刻、完了時刻が表示されます。

![ジョブの状態ページのスクリーンショット。](../media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>ログとスケーリング ツールの出力を見る

スケールアウト操作やスケールイン操作のログを表示するには、Runbook を開いて、ジョブの名前を選択します。

Azure Automation アカウントをホストしているリソース グループの Runbook (既定の名前は WVDAutoScaleRunbook) に移動して、 **[概要]** を選択します。 [概要] ページで、[最近のジョブ] の下にあるジョブを選択すると、次の図に示すようなスケーリング ツールの出力が表示されます。

![スケーリング ツールの出力ウィンドウの画像。](../media/tool-output.png)

