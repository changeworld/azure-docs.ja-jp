---
title: Azure Automation での初めてのグラフィカルな Runbook
description: 簡単なグラフィカル Runbook を作成、テスト、および発行する手順を説明するチュートリアルです。
keywords: Runbook, Runbook テンプレート, Runbook Automation, Azure Runbook
services: automation
ms.subservice: process-automation
ms.date: 04/13/2018
ms.topic: conceptual
ms.openlocfilehash: e208982145f0c3178a26526c73af81ab009a8d46
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605196"
---
# <a name="my-first-graphical-runbook"></a>初めてのグラフィカルな Runbook

> [!div class="op_single_selector"]
> * [グラフィカル](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell ワークフロー](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

このチュートリアルでは、Azure Automation で [グラフィカルな Runbook](automation-runbook-types.md#graphical-runbooks) を作成する手順を説明します。 最初に、簡単な Runbook を作成、テスト、発行しながら、Runbook ジョブの状態を追跡する方法を学習します。 その後、実際に Azure リソースを管理するように Runbook を変更し、ここでは Azure 仮想マシンを開始します。 チュートリアルの最後で、Runbook のパラメーターおよび条件付きリンクを追加することによって、Runbook をいっそう堅牢にします。

>[!NOTE]
>この記事は、新しい Azure PowerShell Az モジュールを使用するために更新されました。 AzureRM モジュールはまだ使用でき、少なくとも 2020 年 12 月までは引き続きバグ修正が行われます。 Az モジュールと AzureRM の互換性の詳細については、「[Introducing the new Azure PowerShell Az module (新しい Azure PowerShell Az モジュールの概要)](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)」を参照してください。 Hybrid Runbook Worker での Az モジュールのインストール手順については、「[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)」を参照してください。 Automation アカウントについては、「[Azure Automation の Azure PowerShell モジュールを更新する方法](automation-update-azure-modules.md)」に従って、モジュールを最新バージョンに更新できます。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* Azure のサブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* [Automation アカウント](automation-offering-get-started.md)。Runbook の保存と Azure リソースの認証に使用します。 このアカウントには、仮想マシンを開始および停止するアクセス許可が必要です。
* Azure 仮想マシン。 マシンを停止して起動するので、運用 VM は使用しないでください。

## <a name="step-1---create-runbook"></a>手順 1 - Runbook を作成する

最初に、"Hello World" というテキストを出力する簡単な Runbook を作成します。

1. Azure ポータルで、Automation アカウントを開きます。 

    Automation アカウント ページでは、そのアカウントのリソースを簡単に確認できます。 既に資産がいくつかあります。 これらの資産のほとんどは、新しい Automation アカウントに自動的に含まれるモジュールです。 また、サブスクリプションに関連付けられている資格情報資産も持っている必要があります。
2. **[プロセス オートメーション]** の **[Runbook]** を選択し、Runbook の一覧を開きます。
3. **[+ Runbook の作成]** を選択して、新しい Runbook を作成します。
4. Runbook の名前を "MyFirstRunbook-Graphical" に設定します。
5. ここでは、[グラフィカルな Runbook](automation-graphical-authoring-intro.md) を作成します。 **[Runbook の種類]** での **[グラフィック]** を選択します。<br> ![新しい Runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. **[作成]** をクリックして Runbook を作成し、グラフィカル エディターを開きます。

## <a name="step-2---add-activities"></a>手順 2 - アクティビティを追加する

エディターの左側にあるライブラリ コントロールでは、Runbook に追加するアクティビティを選択できます。 Runbook のテキストを出力する **Write-Output** コマンドレットを追加します。

1. ライブラリ コントロールの検索フィールドをクリックして、「write-output」と入力します。 検索結果は、次の図のようになります。 <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. 一覧の一番下までスクロールします。 **[Write-Output]** を右クリックし、 **[キャンバスに追加]** を選択します。 または、コマンドレット名の横にある省略記号 [...] をクリックし、 **[キャンバスに追加]** を選択してもかまいません。
1. キャンバスで **[Write-Output]** アクティビティをクリックします。 この操作によって [構成コントロール] ページが開き、アクティビティを構成できます。
1. **[ラベル]** フィールドの既定値はコマンドレットの名前ですが、もっとわかりやすい名前に変更できます。 「Write Hello World to output」に変更します。
1. **[パラメーター]** をクリックし、コマンドレットのパラメーターの値を指定します。

   一部のコマンドレットには複数のパラメーター セットがあり、どれを使用するかを選択する必要があります。 この場合、**Write-Output** のパラメーター セットは 1 つだけです。

1. *[InputObject]* パラメーターを選択します。 このパラメーターを使用して、出力ストリームに送信するテキストを指定します。
1. **[データ ソース]** ドロップダウン メニューには、パラメーター値の設定に使用できるソースが含まれます。 このメニューで、 **[PowerShell 式]** を選択します。 

   別のアクティビティ、Automation の資産、PowerShell 式などのソースからの出力を使用できます。 この場合、出力は単に "Hello World" です。 PowerShell 式を使用して文字列を指定することができます。<br>

1. **[式]** フィールドに「Hello World」と入力し、 **[OK]** を 2 回クリックしてキャンバスに戻ります。
1. **[保存]** をクリックして Runbook を保存します。

## <a name="step-3---test-the-runbook"></a>手順 3 - Runbook をテストする

Runbook を発行して運用環境で使用できるようにする前に、テストして正常に動作することを確認します。 Runbook をテストすると、そのドラフト バージョンが実行され、その出力を対話形式で表示できます。

1. **[テスト ウィンドウ]** を選択して、[テスト] ペインを開きます。
1. **[開始]** をクリックしてテストを開始します。 有効なオプションはこれだけです。
1. [Runbook ジョブ](automation-runbook-execution.md)が作成され、その状態がペインに表示されることに注意してください。

   ジョブの最初の状態は "キュー済み" であり、クラウドで runbook worker が使用可能になるのをジョブが待機していることを示します。 worker がジョブを要求すると、状態は "開始中" になります。 最後に、Runbook の実行が実際に開始されると、状態は "実行中" になります。

1. Runbook ジョブが完了すると、[テスト] ペインにその出力が表示されます。 この場合は、"Hello World" と表示されます。<br> ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
1. テスト ウィンドウを閉じてキャンバスに戻ります。

## <a name="step-4---publish-and-start-the-runbook"></a>手順 4 - Runbook を発行して開始する

作成した Runbook は、まだドラフト モードです。 運用環境で実行する前に、発行する必要があります。 Runbook を発行するときは、既存の発行済みバージョンをドラフト バージョンで上書きします。 この例では、Runbook を作成したばかりなので、発行済みバージョンはまだありません。

1. **[発行]** を選択して Runbook を発行し、確認を要求されたら **[はい]** をクリックします。
1. [Runbook] ページで左にスクロールして Runbook を表示し、 **[編集状態]** の値が [発行済み] に設定されていることを確認します。
1. 右へスクロールして戻り、MyFirstRunbook-Graphical のページを表示します。

   上部のオプションを使用すると、Runbook の即時開始、将来の開始スケジュールの設定、または Runbook を HTTP 呼び出しで開始できるようにする [Webhook](automation-webhooks.md) の作成を行うことができます。

1. **[開始]** を選択し、確認を求められたら **[はい]** をクリックして、Runbook を開始します。
1. 作成した Runbook ジョブのジョブ ペインが開きます。 **[ジョブの状態]** に [完了] と表示されていることを確認します。
1. **[出力]** をクリックして [出力] ページを開くと、"Hello World" と表示されます。
1. [出力] ページを閉じます。
1. **[すべてのログ]** をクリックして、Runbook ジョブのストリーム ウィンドウを開きます。 出力ストリームには "Hello World" のみが表示されます。 

    Runbook で詳細ストリームやエラー ストリームに書き込まれている場合は、[ストリーム] ペインで Runbook ジョブの他のストリームも表示できることに注意してください。
1. [ストリーム] ペインと [ジョブ] ペインを閉じて、MyFirstRunbook-Graphical ページに戻ります。
1. Runbook のすべてのジョブを表示するには、 **[リソース]** の **[ジョブ]** を選択します。 [ジョブ] ページには、自分の Runbook で作成されたすべてのジョブの一覧が表示されます。 ジョブを 1 回実行しただけであるため、一覧に表示されるジョブは 1 つだけです。
1. ジョブ名をクリックすると、Runbook を開始したときに表示されたのと同じ [ジョブ] ペインが開きます。 Runbook に対して作成されたジョブの詳細を表示するには、このペインを使用します。

## <a name="step-5---create-variable-assets"></a>手順 5 - 変数アセットを作成する

Runbook をテストして発行しましたが、これまでのところ Azure リソースの管理に役立つことは何もしていません。 認証を行うように Runbook を構成する前に、サブスクリプション ID を保持する変数を作成し、認証を行うアクティビティを設定して、この変数を参照する必要があります。 サブスクリプション コンテキストへの参照を含めると、複数のサブスクリプションを簡単に使用できます。

1. ナビゲーション ペインの **[サブスクリプション]** オプションからサブスクリプション ID をコピーします。
1. [Automation アカウント] ページで、 **[共有リソース]** の **[変数]** を選択します。
1. **[+ 変数の追加]** を選択します。
1. [新しい変数] ページで、表示されるフィールドを次のように設定します。

    * **[名前]** -- 「AzureSubscriptionId」と入力します。
    * **[値]** -- サブスクリプション ID を入力します。 
    * **[種類]** -- 文字列を選択したままにします。
    * **[暗号化]** -- 既定値を使用します。
1. **[作成]** をクリックして変数を作成します。

## <a name="step-6---add-authentication"></a>手順 6 - 認証を追加する

サブスクリプション ID を保持する変数を作成したので、サブスクリプションの実行資格情報を使用して認証を行うように Runbook を構成できます。 これを行うには、Azure 実行接続を資産として追加します。 また、[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount?view=azps-3.5.0) コマンドレットと [Set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/Set-AzContext?view=azps-3.5.0) コマンドレットをキャンバスに追加する必要もあります。

>[!NOTE]
>PowerShell Runbook の場合、**Add-AzAccount** と **Add-AzureRMAccount** は **Connect-AzAccount** の別名です。 これらの別名は、グラフィカルな Runbook では使用できないことに注意してください。 グラフィカルな Runbook で使用できるのは、**Connect-AzAccount** 自体だけです。

1. Runbook に戻り、MyFirstRunbook-Graphical ページの **[編集]** を選択します。
1. "Write Hello World to output" エントリはもう必要ありません。 省略記号をクリックして、 **[削除]** を選択します。
1. ライブラリ コントロールで、 **[資産]** 、 **[接続]** の順に展開します。 **[キャンバスに追加]** を選択して、キャンバスに "AzureRunAsConnection" を追加します。
1. ライブラリ コントロールで、検索フィールドに「Connect-AzAccount」と入力します。
1. **Connect-AzAccount** をキャンバスに追加します。
1. **Get Run As Connection** にポインターを合わせて、図形の下部に円を表示します。 円をクリックし、矢印を **Connect-AzAccount** にドラッグしてリンクを作成します。 Runbook で、まず **Get Run As Connection** が実行され、次に **Connect-AzAccount** が実行されます。<br> ![アクティビティ間のリンクの作成](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. キャンバスで **Connect-AzAccount** を選択します。 [構成制御] ペインで、 **[ラベル]** フィールドに「Login to Azure」と入力します。
1. **[パラメーター]** をクリックすると、[アクティビティ パラメーターの構成] ページが表示されます。
1. **Connect-AzAccount** コマンドレットには複数のパラメーター セットがあり、パラメーターの値を指定する前に、パラメーター セットを 1 つ選択する必要があります。 **[パラメーター セット]** をクリックして、ServicePrincipalCertificate パラメーター セットを選択します。
1. このパラメーター セットのパラメーターが、[アクティビティ パラメーター構成] ページに表示されます。 **[APPLICATIONID]** をクリックします。<br> ![Azure アカウントのパラメーターを追加する](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. [パラメーター値] ページで、次のように設定して、 **[OK]** をクリックします。

   * **[データ ソース]** -- **[アクティビティの出力]** を選択します。
   * データ ソース リスト -- **Get Run As Connection** を選択します。
   * **[フィールド パス]** -- 「ApplicationId」と入力します。 フィールド パスにプロパティ名を指定するのは、アクティビティによって複数のプロパティを備えたオブジェクトが出力されるためです。
1. **CERTIFICATETHUMBPRINT** をクリックし、[パラメーター値] ページで次のように設定して、 **[OK]** をクリックします。

    * **[データ ソース]** -- **[アクティビティの出力]** を選択します。
    * データ ソース リスト -- **Get Run As Connection** を選択します。
    * **[フィールド パス]** -- 「CertificateThumbprint」と入力します。
1. **SERVICEPRINCIPAL** をクリックし、[パラメーター値] ページで、 **[データ ソース]** フィールドに ConstantValue を選択し、 **[True]** オプションをクリックして、 **[OK]** をクリックします。
1. **TENANTID** をクリックし、[パラメーター値] ページで次のように設定します。 終わったら、 **[OK]** を 2 回クリックします。

    * **[データ ソース]** -- **[アクティビティの出力]** を選択します。 
    * データ ソース リスト -- **Get Run As Connection** を選択します。
    * **[フィールド パス]** -- 「TenantId」と入力します。 
1. ライブラリ コントロールの検索ボックスに「Set-AzContext」と入力します。
1. **Set-AzContext** をキャンバスに追加します。
1. キャンバスで **Set-AzContext** を選択します。 [構成制御] ペインで、 **[ラベル]** に「Specify Subscription Id」と入力します。
1. **[パラメーター]** をクリックすると、[アクティビティ パラメーターの構成] ページが表示されます。
1. **Set-AzContext** コマンドレットには複数のパラメーター セットがあり、パラメーターの値を指定する前に、パラメーター セットを 1 つ選択する必要があります。 **[パラメーター セット]** をクリックして、SubscriptionId パラメーター セットを選択します。
1. このパラメーター セットのパラメーターが、[アクティビティ パラメーター構成] ページに表示されます。 **SubscriptionID** をクリックします。
1. [パラメーター値] ページで、 **[データ ソース]** に **[変数資産]** を選択し、ソース リストから **AzureSubscriptionId** を選択します。 終わったら、 **[OK]** を 2 回クリックします。
1. **Login to Azure** にポインターを合わせて、図形の下部に円を表示します。 円をクリックし、矢印を **[Specify Subscription Id]** までドラッグします。 

この時点の Runbook は次のようになります。 <br>![Runbook 認証の構成](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>手順 7 - 仮想マシンを開始するアクティビティを追加する

ここでは、仮想マシンを起動するための **Start-AzVM** アクティビティを追加する必要があります。 Azure サブスクリプション内の任意の仮想マシンを選択できます。ここでは、その名前を [Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm?view=azps-3.5.0) コマンドレットにハードコーディングします。

1. ライブラリ コントロールの検索フィールドに「**Start-Az**」と入力します。
2. **Start-AzVM** をキャンバスに追加した後、それをクリックして **Specify Subscription Id** の下にドラッグします。
1. **[Specify Subscription Id]** にポインターを合わせて、図形の下部に円を表示します。 円をクリックし、矢印を **Start-AzVM** までドラッグします。
1. **Start-AzVM** を選択します。 **[パラメーター]** 、 **[パラメーター セット]** の順にクリックし、アクティビティに対するセットを表示します。
1. **ResourceGroupNameParameterSetName** パラメーター セットを選択します。 フィールド **ResourceGroupName** と **Name** の横に付いている感嘆符は、必須のパラメーターであることを示します。 どちらのフィールドも文字列値が想定されていることに注意してください。
1. 一覧から **Name**してください。 **[データ ソース]** フィールドで、 **[PowerShell 式]** を選択します。 この Runbook を開始するために使用する VM として、二重引用符で囲んだコンピューター名を入力します。 **[OK]** をクリックします。
1. 一覧から **ResourceGroupName**してください。 **[データ ソース]** フィールドの値として [PowerShell 式] を使用し、リソース グループの名前を二重引用符で囲んで入力します。 **[OK]** をクリックします。
1. **テスト ウィンドウ**をクリックして、Runbook をテストできるようにします。
1. **[開始]** をクリックしてテストを開始します。 終わったら、VM が開始されたことを確認します。 

この時点の Runbook は次のようになります。 <br>![Runbook 認証の構成](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>手順 8 - 追加の入力パラメーターを追加する

現在、Runbook では、**Start-AzVM** コマンドレットに対して指定したリソース グループ内の VM が起動されます。 Runbook を開始するときに名前とリソース グループの両方を指定できれば、Runbook はいっそう便利になります。 その機能を提供するための入力パラメーターを Runbook に追加してみましょう。

1. MyFirstRunbook-Graphical ペインで **[編集]** をクリックして、グラフィカル エディターを開きます。
1. **[入力と出力]** 、 **[入力の追加]** の順に選択して、Runbook 入力パラメーター ウィンドウを開きます。
1. 表示されるフィールドを次のように設定して、 **[OK]** をクリックします。
   * **[名前]** -- 「*VMName*」と指定します。
   * **[種類]** -- 文字列の設定のままにします。
   * **[必須]** -- 値を [はい] に変更します。
1. *ResourceGroupName* という名前の 2 番目の必須入力パラメーターを作成し、 **[OK]** をクリックして [入力と出力] ペインを閉じます。<br> ![Runbook の入力パラメーター](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. **Start-AzVM** アクティビティを選択し、 **[パラメーター]** をクリックします。
1. **Name** の **[データ ソース]** フィールドを **[Runbook の入力]** に変更します。 次に、**VMName** を選択します。
1. **ResourceGroupName** の **[データ ソース]** フィールドを **[Runbook の入力]** に変更し、**ResourceGroupName** を選択します。<br> ![Start-AzVM のパラメーター](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. Runbook を保存してテスト ウィンドウを開きます。 テストで使用する 2 つの入力変数の値を指定できるようになりました。
1. テスト ウィンドウを閉じます。
1. **[発行]** をクリックして、新しいバージョンの Runbook を発行します。
1. 前に起動した VM を停止します。
1. **[開始]** をクリックして Runbook を開始します。 起動する VM の **VMName** と **ResourceGroupName** の値を入力します。
1. Runbook が完了したら、VM が起動されていることを確認します。

## <a name="step-9---create-a-conditional-link"></a>手順 9 - 条件付きリンクを作成する

次に、まだ起動されていない場合にのみ VM の起動を試みるように Runbook を変更します。 これを行うには、VM のインスタンス レベルの状態を取得する [Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) コマンドレットを追加します。 次に、Get Status という名前の PowerShell ワークフロー コード モジュールを追加します。これには、VM が実行中か停止中かを調べるための PowerShell コード スニペットが含まれています。 Get Status モジュールからの条件付きリンクでは、現在の実行状態が停止の場合にのみ、**Start-AzVM** が実行されます。 この手順の最後では、Runbook で**Write-Output** コマンドレットを使用して、VM が正常に起動されたかどうかを通知するメッセージを出力します。

1. グラフィカル エディターで MyFirstRunbook-Graphical を開きます。
1. **Specify Subscription Id** と **Start-AzVM** の間のリンクをクリックし、**Delete** キーを押して、リンクを削除します。
1. ライブラリ コントロールの検索フィールドに「Get-Az」と入力します。
1. **Get-AzVM** をキャンバスに追加します。
1. **Get-AzVM**、 **[パラメーター セット]** の順に選択し、**Get-AzVM** のセットを表示します。 
1. GetVirtualMachineInResourceGroupNameParamSet パラメーター セットを選択します。 **ResourceGroupName** フィールドと **Name** フィールドの横に付いている感嘆符は、必須のパラメーターであることを示します。 どちらのフィールドも文字列値が想定されていることに注意してください。
1. **Name** の **[データ ソース]** で **[Runbook の入力]** を選択し、**VMName** を選択します。 **[OK]** をクリックします。
1. **ResourceGroupName** の **[データ ソース]** で、 **[Runbook の入力]** を選択し、**ResourceGroupName** を選択します。 **[OK]** をクリックします。
1. **Status** の **[データ ソース]** で、 **[定数値]** を選択し、 **[True]** を選択します。 **[OK]** をクリックします。
1. **Specify Subscription Id** から **Get-AzVM** へのリンクを作成します。
1. ライブラリ コントロールで **[Runbook の制御]** を展開し、**Code** をキャンバスに追加します。  
1. **Get-AzVM** から **Code** へのリンクを作成します。  
1. **Code** をクリックし、構成ペインでラベルを **Get Status** に変更します。
1. **Code** を選択すると、[コード エディター] ページが表示されます。  
1. コード エディターに、次のコード スニペットを貼り付けます。

    ```powershell-interactive
     $StatusesJson = $ActivityOutput['Get-AzVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```

1. **Get Status** から **Start-AzVM** へのリンクを作成します。<br> ![コード モジュールを含む Runbook](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. リンクを選択し、構成ウィンドウで **[条件の適用]** を [はい] に変更します。 リンクが、条件が true の場合にのみ対象のアクティビティが実行されることを示す破線になることに注意してください。  
1. **[条件式]** に「`$ActivityOutput['Get Status'] -eq "Stopped"`」と入力します。 **Start-AzVM** は、VM が停止している場合にのみ実行されるようになりました。
1. ライブラリ コントロールで **Cmdlets** 、**Microsoft.PowerShell.Utility** の順に展開します。
1. **Write-Output** をキャンバスに 2 回追加します。
1. 1 つ目の **Write-Output** コントロールで、 **[パラメーター]** をクリックし、 **[ラベル]** の値を **Notify VM Started** に変更します。
1. **InputObject** で、 **[データ ソース]** を **[PowerShell 式]** に変更し、式に「$VMName successfully started.」と入力します。
1. 2 つ目の **Write-Output** コントロールで、 **[パラメーター]** をクリックし、 **[ラベル]** の値を **Notify VM Start Failed** に変更します。
1. **InputObject** の **[データ ソース]** を **[PowerShell 式]** に変更し、式に「$VMName could not start.」と入力します。
1. **Start-AzVM** から **Notify VM Started** および **Notify VM Start Failed** へのリンクを作成します。
1. **Notify VM Started** へのリンクを選択し、 **[条件の適用]** を True に変更します。
1. **[条件式]** に「`$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`」と入力します。 これで、この **Write-Output** コントロールは、VM が正常に起動された場合にのみ実行されるようになりました。
1. **Notify VM Start Failed** へのリンクを選択し、 **[条件の適用]** を True に変更します。
1. **[条件式]** フィールドに「`$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`」と入力します。 これで、この **Write-Output** コントロールは、VM が正常に起動されなかった場合にのみ実行されるようになりました。 Runbook は次の図のようになります。 <br> ![Write-Output での Runbook](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Runbook を保存してテスト ウィンドウを開きます。
1. VM が停止している状態で Runbook を開始すると、マシンが起動します。

## <a name="next-steps"></a>次のステップ

* グラフィカル作成の詳細については、「[Azure Automation でのグラフィカル作成](automation-graphical-authoring-intro.md)」を参照してください。
* PowerShell Runbook の使用を開始するには、「[初めての PowerShell Runbook](automation-first-runbook-textual-powershell.md)」を参照してください。
* PowerShell Workflow Runbook の使用を開始するには、「 [最初の PowerShell Workflow Runbook](automation-first-runbook-textual.md)」を参照してください。