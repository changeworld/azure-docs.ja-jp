---
title: Azure SSIS 統合ランタイムをスケジュール設定する方法 | Microsoft Docs
description: この記事では、Azure Automation とデータ ファクトリを使用して、Azure SSIS 統合ランタイムの開始と停止をスケジュール設定する方法を説明します。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/16/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: f83715d2a382db271686210d9df285c255c09216
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39113996"
---
# <a name="how-to-start-and-stop-the-azure-ssis-integration-runtime-on-a-schedule"></a>Azure SSIS 統合ランタイムをスケジュールに従って起動および停止する方法
この記事では、Azure Automation と Azure Data Factory を使用して、Azure SSIS 統合ランタイム (IR) の開始と停止をスケジュール設定する方法を説明します。 Azure SSIS (SQL Server Integration Services) 統合ランタイム (IR) の実行には関連したコストがかかります。 このため一般には、SSIS パッケージを Azure で実行する必要がある場合にのみ IR を実行し、必要ないときには IR を停止する必要があります。 データ ファクトリ UI または Azure PowerShell を使用すると、[Azure SSIS IR を手動で開始または停止](manage-azure-ssis-integration-runtime.md)できます。

たとえば、Azure Automation の PowerShell Runbook への webhook を含む Web アクティビティを作成し、それらの間で SSIS パッケージの実行アクティビティをチェーンすることができます。 Web アクティビティによって、パッケージの実行前後のぎりぎりの時間に Azure SSIS IR の開始と停止を行うことができます。 SSIS パッケージの実行アクティビティに関する詳細については、「[Azure Data Factory で SSIS アクティビティを使用して SSIS パッケージを実行する](how-to-invoke-ssis-package-ssis-activity.md)」を参照してください。

## <a name="overview-of-the-steps"></a>手順の概要

この記事で説明する手順の概要を次に示します。

1. **Azure Automation Runbook を作成してテストします。** この手順では、Azure SSIS IR を開始または停止するスクリプトを含む PowerShell Runbook を作成します。 次に、開始と停止両方のシナリオで Runbook をテストし、IR の開始または停止を確認します。 
2. **Runbook の 2 つのスケジュールを作成します。** 1 つ目のスケジュールでは、操作として開始を含む Runbook を構成します。 2 つ目のスケジュールでは、操作として停止を含む Runbook を構成します。 どちらのスケジュールでも、Runbook を実行する間隔を指定します。 たとえば、1 つ目を毎日午前 8 時に実行し、2 つ目を毎日午後 11 時に実行するようにスケジュール設定するとします。 最初の Runbook が実行すると、Azure SSIS IR が開始されます。 次の Runbook が実行すると、Azure SSIS IR が停止します。 
3. **Runbook に対して 2 つの webhook を作成します。** 1 つは開始操作用、もう 1 つは停止操作用です。 データ ファクトリ パイプラインで Web アクティビティを構成するときは、これらの webhook の URL を使用します。 
4. **データ ファクトリ パイプラインを作成します**。 作成するパイプラインは、3 つのアクティビティで構成されます。 最初の **Web** アクティビティは、最初の webhook を呼び出して Azure SSIS IR を開始します。 **ストアド プロシージャ** アクティビティは、SSIS パッケージを実行する SQL スクリプトを実行します。 2 つ目の **Web** アクティビティは Azure SSIS IR を停止します。 ストアド プロシージャ アクティビティを使用して、データ ファクトリ パイプラインから SSIS パッケージを呼び出す方法について詳しくは、[SSIS パッケージの呼び出し](how-to-invoke-ssis-package-stored-procedure-activity.md)に関する記事をご覧ください。 次に、スケジュール トリガーを作成し、パイプラインが指定した間隔で実行するようにスケジュール設定します。

## <a name="prerequisites"></a>前提条件
Azure SSIS 統合ランタイムをまだプロビジョニングしていない場合は、[チュートリアル](tutorial-create-azure-ssis-runtime-portal.md)の手順に従ってプロビジョニングします。 

## <a name="create-and-test-an-azure-automation-runbook"></a>Azure Automation Runbook を作成してテストする
このセクションでは、次の手順を実行します。 

1. Azure Automation アカウントを作成します。
2. Azure Automation アカウントに PowerShell Runbook を作成します。 Runbooku に関連付けられる PowerShell スクリプトは、OPERATION パラメーターに指定するコマンドに応じて、Azure SSIS IR を開始または停止します。 
3. 開始と停止両方のシナリオで Runbook をテストし、作動することを確認します。 

### <a name="create-an-azure-automation-account"></a>Azure Automation アカウントを作成する
Azure Automation アカウントを持っていない場合は、この手順の説明に従って作成します。 手順について詳しくは、「[Azure Automation アカウントを作成する](../automation/automation-quickstart-create-account.md)」をご覧ください。 この手順の中で、**Azure 実行** アカウント (Azure Active Directory のサービス プリンシパル) を作成し、Azure サブスクリプションの**共同作成者**ロールに追加します。 これが、Azure SSIS IR があるデータ ファクトリを含むサブスクリプションと同じであることを確認します。 Azure Automation はこのアカウントを使用して、Azure Resource Manager に対して認証し、リソースを処理します。 

1. Web ブラウザー (**Microsoft Edge** または **Google Chrome**) を起動します。 現在、Data Factory の UI がサポートされる Web ブラウザーは Microsoft Edge と Google Chrome だけです。
2. [Azure Portal](https://portal.azure.com/) にログインします。    
3. 左側のメニューで **[新規]** を選択し、**[監視 + 管理]** を選択し、**[Automation]** を選択します。 

    ![[新規] -> [監視 + 管理] -> [Automation]](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
2. **[Automation アカウントの追加]** ウィンドウで次の手順を実行します。 

    1. Automation アカウントの**名前**を指定します。 
    2. Azure SSIS IR があるデータ ファクトリを含む**サブスクリプション**を選択します。 
    3. **[リソース グループ]** では、**[新規作成]** を選択して新しいリソース グループを作成するか、**[既存のものを使用]** を選択して既存のリソース グループを選択します。 
    4. Automation アカウントの**場所**を選択します。 
    5. **[実行アカウントの作成]** が **[はい]** に設定されていることを確認します。 サービス プリンシパルが Azure Active Directory に作成されます。 これは Azure サブスクリプションの**共同作成者**ロールに追加されます。
    6. **[ダッシュボードにピン留めする]** を選択すると、ポータルのダッシュボードに表示されます。 
    7. **[作成]** を選択します。 

        ![[新規] -> [監視 + 管理] -> [Automation]](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
3. ダッシュボードと通知に**展開ステータス**が表示されます。 
    
    ![Automation の展開](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
4. Automation アカウントが正常に作成されると、ホームページが表示されます。 

    ![Automation ホームページ](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-data-factory-modules"></a>データ ファクトリ モジュールをインポートする

1. 左側のメニューの **[共有リソース]** セクションで **[モジュール]** を選択し、モジュールの一覧に **AzureRM.Profile** と **AzureRM.DataFactoryV2** があるかどうか確認します。

    ![必要なモジュールの確認](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  [AzureRM.DataFactoryV2 モジュール](https://www.powershellgallery.com/packages/AzureRM.DataFactoryV2/)の PowerShell ギャラリーに移動して、**[Deploy to Azure Automation]\(Azure Automation にデプロイする\)**、自分の Automation アカウントの順に選択し、**[OK]** を選択します。 左側のメニューの **[共有リソース]** セクションの **[モジュール]** に戻り、**AzureRM.DataFactoryV2** モジュールの **[ステータス]** が**利用可能**に変わるまで待ちます。

    ![データ ファクトリ モジュールの確認](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  [AzureRM.Profile モジュール](https://www.powershellgallery.com/packages/AzureRM.profile/)の PowerShell ギャラリーに移動して、**[Deploy to Azure Automation]\(Azure Automation にデプロイする\)** をクリックして、自分の Automation アカウントを選択し、**[OK]** を選択します。 左側のメニューの **[共有リソース]** セクションの **[モジュール]** に戻り、**AzureRM.Profile** モジュールの **[ステータス]** が**利用可能**に変わるまで待ちます。

    ![プロファイル モジュールの確認](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-a-powershell-runbook"></a>PowerShell Runbook を作成する
次に、PowerShell Runbook を作成する手順を説明します。 Runbooku に関連付けられるスクリプトは、**OPERATION** パラメーターに指定するコマンドに応じて、Azure SSIS IR を開始または停止します。 このセクションでは、Runbook を作成するためのすべての詳細は説明しません。 詳しくは、[Runbook の作成](../automation/automation-quickstart-create-runbook.md)に関する記事をご覧ください。

1. **[Runbook]** タブに切り替え、ツールバーから **[+ Runbook の追加]** を選択します。 

    ![[Runbook の追加] ボタン](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
2. **[新しい Runbook の作成]** を選択して、次の手順を実行します。 

    1. **[名前]** に、「**StartStopAzureSsisRuntime**」と入力します。
    2. **[Runbook の種類]** で、**[PowerShell]** を選びます。
    3. **[作成]** を選択します。
    
        ![[Runbook の追加] ボタン](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
3. Runbook のスクリプト ウィンドウに次のスクリプトをコピーして貼り付けます。 ツールバーの **[保存]** ボタンと **[公開]** ボタンを使用して、Runbook を保存してから公開します。 

    ```powershell
    Param
    (
          [Parameter (Mandatory= $true)]
          [String] $ResourceGroupName,
    
          [Parameter (Mandatory= $true)]
          [String] $DataFactoryName,
    
          [Parameter (Mandatory= $true)]
          [String] $AzureSSISName,
    
          [Parameter (Mandatory= $true)]
          [String] $Operation
    )
    
    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
    
        "Logging in to Azure..."
        Connect-AzureRmAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
        if (!$servicePrincipalConnection)
        {
            $ErrorMessage = "Connection $connectionName not found."
            throw $ErrorMessage
        } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
        }
    }
    
    if($Operation -eq "START" -or $operation -eq "start")
    {
        "##### Starting #####"
        Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $AzureSSISName -Force
    }
    elseif($Operation -eq "STOP" -or $operation -eq "stop")
    {
        "##### Stopping #####"
        Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    }  
    "##### Completed #####"    
    ```

    ![PowerShell Runbook の編集](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
5. ツールバーの **[開始]** ボタンを選択して、Runbook をテストします。 

    ![Runbook を開始するボタン](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
6. **[Runbook の開始]** ウィンドウで、次の手順を実行します。 

    1. **[RESOURCEGROUPNAME]** には、Azure SSIS IR があるデータ ファクトリを含むリソース グループの名前を入力します。 
    2. **[DATAFACTORYNAME]** には、Azure SSIS IR があるデータ ファクトリの名前を入力します。 
    3. **[AZURESSISNAME]** には、Azure SSIS IR の名前を入力します。 
    4. **[OPERATION]** には、「**START**」を入力します。 
    5. **[OK]** を選択します。  

        ![[Runbook の開始] ウィンドウ](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
7. [ジョブ] ウィンドウで、**[出力]** タイルを選択します。 ジョブの出力ウィンドウで、メッセージ "**##### Completed #####**" が "**##### Starting #####**" の後に表示されるまで待機します。 Azure SSIS IR を開始するには約 20 分かかります。 **[ジョブ]** ウィンドウを閉じて、**[Runbook]** ウィンドウに戻ります。

    ![Azure SSIS IR - 開始](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
8.  前の 2 つの手順を繰り返しますが、**OPERATION** の値として **STOP** を使用してください。 ツールバーの **[開始]** ボタンを選択して、再び Runbook を開始します。 リソース グループ名、データ ファクトリ名、Azure SSIS IR 名を指定します。 **[OPERATION]** には「**STOP**」を入力します。 

    ジョブの出力ウィンドウで、メッセージ "**##### Completed #####**" が "**##### Stopping #####**" の後に表示されるまで待機します。 Azure SSIS IR の停止は、Azure SSIS IR の開始ほどは時間がかかりません。 **[ジョブ]** ウィンドウを閉じて、**[Runbook]** ウィンドウに戻ります。

## <a name="create-schedules-for-the-runbook-to-startstop-the-azure-ssis-ir"></a>Azure SSIS IR を開始または停止する Rrunbook のスケジュールを作成する
前のセクションでは、Azure SSIS IR を開始または停止する Azure Automation Runbook を作成しました。 このセクションでは、Runbook の 2 つのスケジュールを作成します。 1 つ目のスケジュールを構成するときは、OPERATION パラメーターに START を指定します。 同様に、2 つ目のスケジュールを構成するときは、OPERATION パラメーターに STOP を指定します。 スケジュールを作成する手順について詳しくは、「[スケジュールを作成する](../automation/automation-schedules.md#creating-a-schedule)」をご覧ください。

1. **[Runbook]** ウィンドウで **[スケジュール]** を選択し、ツールバーの **[+ スケジュールの追加]** を選択します。 

    ![Azure SSIS IR - 開始](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
2. **[Runbook のスケジュール設定]** ウィンドウで、次の手順を実行します。 

    1. **[スケジュールを Runbook にリンクします]** を選択します。 
    2. **[新しいスケジュールを作成します]** を選択します。
    3. **[新しいスケジュール]** ウィンドウで、**[名前]** に「**毎日 IR を開始**」と入力します。 
    4. **[開始]** セクションでは、時刻について、現在の時刻から数分後の時刻を指定します。 
    5. **[繰り返し]** では **[定期的]** を選択します。 
    6. **[繰り返し間隔]** セクションでは、**[日]** を選択します。 
    7. **[作成]** を選択します。 

        ![Azure SSIS IR 開始のスケジュール](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
3. **[パラメーターと実行設定]** タブに切り替えます。リソース グループ名、データ ファクトリ名、Azure SSIS IR 名を指定します。 **[OPERATION]** には、「**START**」を入力します。 **[OK]** を選択します。 **[OK]** を再び選択すると、Runbook の **[スケジュール]** ページにスケジュールが表示されます。 

    ![Azure SSIS IR を開始するスケジュール](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
4. 前の 2 つの手順を繰り返して、「**毎日 IR を停止**」という名前のスケジュールを作成します。 ここでは、「**毎日 IR を開始**」スケジュールで指定した時刻から 30 分以上後の時刻を指定します。 **[OPERATION]** には「**STOP**」を指定します。 
5. **[Runbook]** ウィンドウで、左側のメニューの **[ジョブ]** を選択します。 指定時刻にスケジュールによって作成されたジョブとその状態が表示されます。 出力が Runbook をテストした際の表示と同じかどうかなど、ジョブの詳細を確認できます。 

    ![Azure SSIS IR を開始するスケジュール](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
6. テストが終了したら、スケジュールを編集して **[有効]** に **[いいえ]** を選択し、スケジュールを無効にします。 左側のメニューで **[スケジュール]** を選択し、**[毎日 IR を開始] または [毎日 IR を停止]** を選択し、**[有効]** で **[いいえ]** を選択します。 

## <a name="create-webhooks-to-start-and-stop-the-azure-ssis-ir"></a>Azure SSIS IR を開始および停止する webhook を作成する
「[Webhook の作成](../automation/automation-webhooks.md#creating-a-webhook)」の手順に従って、Runbook に 2 つの webhook を作成します。 1 つ目には OPERATION として START を指定し、2 つ目には OPERATION として STOP を指定します。 両方の webhook の URL を任意の場所に保存します (テキスト ファイルや OneNote ノートブックなど)。 データ ファクトリ パイプラインで Web アクティビティを構成するときは、これらの URL を使用します。 次の図は、Azure SSIS IR を開始する webhook の作成例です。

1. **[Runbook]** ウィンドウで、左側のメニューの **[Webhook]** を選択し、ツールバーの **[+ Webhook の追加]** を選択します。 

    ![[Webhook] -> [Webhook の追加]](./media/how-to-schedule-azure-ssis-integration-runtime/add-web-hook-menu.png)
2. **[Webhook の追加]** ウィンドウで **[新しい Webhook を作成します]** を選択し、次の操作を行います。 

    1. **[名前]** に「**StartAzureSsisIR**」を入力します。 
    2. **[有効]** が **[はい]** に設定されていることを確認します。 
    3. **[URL]** をコピーして、任意の場所に保存します。 この手順は重要です。 後から URL を確認できません。 
    4. **[OK]** を選択します。 

        ![新しい Webhook のウィンドウ](./media/how-to-schedule-azure-ssis-integration-runtime/new-web-hook-window.png)
3. **[パラメーターと実行設定]** タブに切り替えます。リソース グループ名、データ ファクトリ名、Azure SSIS IR 名を指定します。 **[OPERATION]** には、「**START**」を入力します。 Click **OK**. **[作成]** をクリックします。 

    ![webhook - パラメーターと実行設定](./media/how-to-schedule-azure-ssis-integration-runtime/webhook-parameters.png)
4. 前の 3 つの手順を繰り返して、「**StopAzureSsisIR**」という名前でもう 1 つの webhook を作成します。 必ず URL をコピーしてください。 パラメーターと実行設定を指定するとき、**[OPERATION]** には「**STOP**」を指定します。 

**StartAzureSsisIR** webhook に対して 1 つ、**StopAzureSsisIR** webhook に対して 1 つ、2 つの URL が必要です。 これらの URL に HTTP POST 要求を送信すると、Azure SSIS IR を開始または停止できます。 

## <a name="create-and-schedule-a-data-factory-pipeline-that-startsstops-the-ir"></a>IR を開始または停止するデータ ファクトリ パイプラインを作成してスケジュール設定する
このセクションでは、Web アクティビティを使用して、前のセクションで作成した webhook を呼び出す方法を説明します。

作成するパイプラインは、3 つのアクティビティで構成されます。 

1. 最初の **Web** アクティビティは、最初の webhook を呼び出して Azure SSIS IR を開始します。 
2. **[SSIS パッケージの実行]** アクティビティまたは **[ストアド プロシージャ]** アクティビティによって SSIS パッケージが実行されます。
3. 2 つ目の **Web** アクティビティは、Azure SSIS IR を停止する webhook を呼び出します。 

パイプラインを作成してテストしたら、スケジュール トリガーを作成して、パイプラインに関連付けます。 スケジュール トリガーによってパイプラインのスケジュールが定義されます。 たとえば、毎日午後 11 時に実行するようにスケジュール設定されたトリガーを作成します。 このトリガーは、毎日午後 11 時にパイプラインを実行します。 パイプラインが、Azure SSIS IR を開始し、SSIS パッケージを実行し、その後 Azure SSIS IR を停止します。 

### <a name="create-a-data-factory"></a>Data Factory を作成する。

1. [Azure Portal](https://portal.azure.com/) にログインします。    
2. 左側のメニューで **[新規]** をクリックし、**[データ + 分析]**、**[Data Factory]** の順にクリックします。 
   
   ![New->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. **[新しいデータ ファクトリ]** ページで、**[名前]** に「**MyAzureSsisDataFactory**」と入力します。 
      
     ![[新しいデータ ファクトリ] ページ](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Azure データ ファクトリの名前は **グローバルに一意**にする必要があります。 次のエラーが発生した場合は、データ ファクトリの名前を変更して (yournameMyAzureSsisDataFactory など) 作成し直してください。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事を参照してください。
  
       `Data factory name �MyAzureSsisDataFactory� is not available`
3. データ ファクトリを作成する Azure **サブスクリプション**を選択します。 
4. **[リソース グループ]** について、次の手順のいずれかを行います。
     
      - **[Use existing (既存のものを使用)]** を選択し、ドロップダウン リストから既存のリソース グループを選択します。 
      - **[新規作成]** を選択し、リソース グループの名前を入力します。   
         
      リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。  
4. **バージョン**として **[V2]** を選択します。
5. データ ファクトリの **場所** を選択します。 データ ファクトリの作成がサポートされている場所のみが一覧に表示されます。
6. **[ダッシュボードにピン留めする]** をオンにします。     
7. **Create** をクリックしてください。
8. ダッシュボードに、**[Deploying data factory]\(データ ファクトリをデプロイしています\)** というステータスを示したタイルが表示されます。 

    ![[Deploying data factory]\(データ ファクトリをデプロイしています\) タイル](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. 作成が完了すると、図に示されているような **[Data Factory]** ページが表示されます。
   
   ![データ ファクトリのホーム ページ](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. **[Author & Monitor]\(作成と監視\)** をクリックして、別のタブで Data Factory ユーザー インターフェイス (UI) を起動します。

### <a name="create-a-pipeline"></a>パイプラインを作成する。

1. **[はじめに]** ページで **[Create pipeline]\(パイプラインの作成\)** を選択します。 

   ![開始ページ](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
2. **[アクティビティ]** ツールの **[全般]** を展開し、**[Web]** アクティビティをパイプライン デザイナー画面にドラッグ アンド ドロップします。 **[プロパティ]** ウィンドウの **[全般]** タブで、アクティビティの名前を「**StartIR**」に変更します。

   ![最初の Web アクティビティ - [全般] タブ](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-general-tab.png)
3. **プロパティ** ウィンドウで **[設定]** タブに切り替え、以下の操作を行います。 

    1. **[URL]** には、Azure SSIS IR を開始する webhook の URL を貼り付けます。 
    2. **[メソッド]** では、**[POST]** を選択します。 
    3. **[本文]** には、「`{"message":"hello world"}`」を入力します。 
   
        ![最初の Web アクティビティ - [設定] タブ](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-settnigs-tab.png)

4. **[アクティビティ]** ツールボックスの **[全般]** セクションから、[SSIS パッケージの実行] アクティビティ、または [ストアド プロシージャ] アクティビティをドラッグ アンド ドロップします。 アクティビティの名前を「**RunSSISPackage**」に設定します。 

5. [SSIS パッケージの実行] アクティビティを選択した場合は、[Azure Data Factory での SSIS アクティビティを使用した SSIS パッケージの実行](how-to-invoke-ssis-package-ssis-activity.md)に関する記事に記載されている指示に従って、アクティビティの作成を完了します。  起動するまで最大 30 分間かかるため、Azure SSIS IR が使用可能になるまで待つための、十分な頻度の再試行回数を指定することを確認します。 

    ![再試行の設定](media/how-to-schedule-azure-ssis-integration-runtime/retry-settings.png)

6. [ストアド プロシージャ] アクティビティを選択した場合は、「[Azure Data Factory のストアド プロシージャ アクティビティを使用して SSIS パッケージを呼び出す](how-to-invoke-ssis-package-stored-procedure-activity.md)」に記載されている指示に従って、アクティビティの作成を完了します。 起動に最大 30 分かかるため、Azure SSIS IR が使用できるようになるまで待つための TRANSACT-SQL スクリプトを挿入してください。
    ```sql
    DECLARE @return_value int, @exe_id bigint, @err_msg nvarchar(150)

    -- Wait until Azure-SSIS IR is started
    WHILE NOT EXISTS (SELECT * FROM [SSISDB].[catalog].[worker_agents] WHERE IsEnabled = 1 AND LastOnlineTime > DATEADD(MINUTE, -10, SYSDATETIMEOFFSET()))
    BEGIN
        WAITFOR DELAY '00:00:01';
    END

    EXEC @return_value = [SSISDB].[catalog].[create_execution] @folder_name=N'YourFolder',
        @project_name=N'YourProject', @package_name=N'YourPackage',
        @use32bitruntime=0, @runincluster=1, @useanyworker=1,
        @execution_id=@exe_id OUTPUT 

    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1

    EXEC [SSISDB].[catalog].[start_execution] @execution_id = @exe_id, @retry_count = 0

    -- Raise an error for unsuccessful package execution, check package execution status = created (1)/running (2)/canceled (3)/
    -- failed (4)/pending (5)/ended unexpectedly (6)/succeeded (7)/stopping (8)/completed (9) 
    IF (SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id = @exe_id) <> 7 
    BEGIN
        SET @err_msg=N'Your package execution did not succeed for execution ID: '+ CAST(@execution_id as nvarchar(20))
        RAISERROR(@err_msg, 15, 1)
    END
    ```

7. **[Web]** アクティビティを **[SSIS パッケージの実行]** または **[ストアド プロシージャ]** アクティビティに接続します。 

    ![[Web] アクティビティと [ストアド プロシージャ] アクティビティを接続する](./media/how-to-schedule-azure-ssis-integration-runtime/connect-web-sproc.png)

8. 別の **[Web]** アクティビティを **[SSIS パッケージの実行]** または **[ストアド プロシージャ]** アクティビティの右側にドラッグ アンド ドロップします。 アクティビティの名前を「**StopIR**」に設定します。 
9. **プロパティ** ウィンドウで **[設定]** タブに切り替え、以下の操作を行います。 

    1. **[URL]** には、Azure SSIS IR を停止する webhook の URL を貼り付けます。 
    2. **[メソッド]** では、**[POST]** を選択します。 
    3. **[本文]** には、「`{"message":"hello world"}`」を入力します。  
10. **[SSIS パッケージの実行]** アクティビティまたは **[ストアド プロシージャ]** アクティビティを最後の **[Web]** アクティビティに接続します。

    ![完全なパイプライン](./media/how-to-schedule-azure-ssis-integration-runtime/full-pipeline.png)
11. ツール バーの **[検証]** をクリックしてパイプライン設定を検証します。 **[>>]** ボタンをクリックして、**[Pipeline Validation Report]\(パイプライン検証レポート\)** を閉じます。 

    ![パイプラインの検証](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-the-pipeline"></a>パイプラインをテスト実行する

1. パイプラインのツールバーで **[テストの実行]** を選択します。 下の **[出力]** ウィンドウに出力が表示されます。 

    ![テストの実行](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
2. Azure Automation アカウントの **[Runbook]** ページで、Azure SSIS IR を開始して終了するジョブが実行されたことを確認できます。 

    ![Runbook ジョブ](./media/how-to-schedule-azure-ssis-integration-runtime/runbook-jobs.png)
3. SQL Server Management Studio を起動します。 **[サーバーへの接続]** ウィンドウで、次の操作を行います。 

    1. **[サーバー名]** には、**&lt;ご使用の Azure SQL Database&gt;.database.windows.net** を指定します。
    2. **[オプション >>]** を選択します。
    3. **[データベースの接続]** では **[SSISDB]** を選択します。
    4. **[接続]** を選択します。 
    5. **[Integration Services カタログ]** -> **[SSISDB]** -> ご使用のフォルダー -> **[プロジェクト]** -> ご使用の SSIS プロジェクト -> **[パッケージ]** を展開します。 
    6. SSIS パッケージを右クリックし、**[レポート]** -> **[標準レポート]** -> **[すべての実行]** を選択します。 
    7. SSIS パッケージが実行されたことを確認します。 

        ![SSIS パッケージの実行を確認](./media/how-to-schedule-azure-ssis-integration-runtime/verfiy-ssis-package-run.png)

### <a name="schedule-the-pipeline"></a>パイプラインのスケジュールを設定する 
パイプラインの予期したとおりの動作が確認できたところで、トリガーを作成してこのパイプラインを指定の間隔で実行できます。 スケジュール トリガーをパイプラインに関連付ける方法について詳しくは、「[スケジュールに基づいてパイプラインをトリガーする](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)」をご覧ください。

1. パイプラインのツール バーで、**[トリガー]** を選択し、**[新規作成/編集]** を選択します。 

    ![[トリガー] -> [新規作成/編集]](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)
2. **[Add Triggers]\(トリガーの追加\)** ウィンドウで **[+ 新規]** を選択します。

    ![[Add Triggers]\(トリガーの追加\) - [新規]](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)
3. **[新しいトリガー]** で、次の操作を行います。 

    1. **[名前]** には、トリガーの名前を指定します。 次の例では、「**毎日実行する**」がトリガーの名前です。 
    2. **[種類]** では **[スケジュール]** を選択します。 
    3. **[開始日]** では、開始日時を選択します。 
    4. **[繰り返し]** では、トリガーの間隔を指定します。 次の例では毎日 1 回です。 
    5. **[終了]** では、**[指定日]** オプションを選択して日付と時刻を指定できます。 
    6. **[アクティブ化]** を選択します。 トリガーは、ソリューションをデータ ファクトリに公開するとすぐにアクティブ化されます。 
    7. **[次へ]** を選択します。

        ![[トリガー] -> [新規作成/編集]](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
4. **[Trigger Run Parameters]\(トリガー実行のパラメーター\)** ページで、警告を確認し、**[完了]** を選択します。 
5. 左側のウィンドウの **[すべてを公開]** を選択し、ソリューションをデータ ファクトリに公開します。 

    ![すべてを公開](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)

### <a name="monitor-the-pipeline-and-trigger-in-the-azure-portal"></a>Azure portal でパイプラインとトリガーを監視する

1. トリガーの実行とパイプラインの実行を監視するには、左側の **[監視]** タブを使用します。 手順について詳しくは、「[パイプラインの監視](quickstart-create-data-factory-portal.md#monitor-the-pipeline)」をご覧ください。

    ![パイプライン実行](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)
2. パイプラインの実行に関連付けられているアクティビティの実行を表示するには、**[アクション]** 列にある最初のリンク (**[View Activity Runs]\(アクティビティの実行の表示\)**) をクリックします。 パイプラインの各アクティビティ (最初の Web アクティビティ、ストアド プロシージャ アクティビティ、2 つ目の Web アクティビティ) に関連している 3 つのアクティビティ実行を確認できます。 戻ってパイプラインの実行を表示するには、上部の **[パイプライン]** リンクをクリックします。

    ![アクティビティの実行](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)
3. また、一番上の **[Pipeline Runs]\(パイプラインの実行\)** の横にあるドロップダウン リストから **[Trigger runs]\(トリガーの実行\)** を選択して、トリガーの実行を表示することもできます。 

    ![トリガーの実行](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

### <a name="monitor-the-pipeline-and-trigger-with-powershell"></a>PowerShell でパイプラインとトリガーを監視する

次の例のようなスクリプトを使用して、パイプラインとトリガーを監視します。

1. パイプラインの実行の状態を取得します。

  ```powershell
  Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
  ```

2. トリガーに関する情報を取得します。

  ```powershell
  Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
  ```

3. トリガーの実行の状態を取得します。

  ```powershell
  Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
  ```

## <a name="next-steps"></a>次の手順
次のブログ記事を参照してください。
-   [ADF パイプラインでの SSIS アクティビティを含む ETL/ELT ワークフローの最新化と拡張](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)

SSIS ドキュメントの次の記事をご覧ください。 

- [Azure での SSIS パッケージのデプロイ、実行、監視](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Azure での SSIS カタログへの接続](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Azure でのパッケージ実行のスケジュール設定](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Windows 認証を使用したオンプレミス データ ソースへの接続](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)
