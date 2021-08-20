---
title: Azure Automation でグラフィカル Runbook を作成する
description: この記事では、グラフィカル Runbook を Azure Automation で作成、テスト、発行する方法を説明します。
services: automation
ms.subservice: process-automation
ms.date: 07/16/2021
ms.topic: tutorial
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e782fd046af23beb5c84c8e419cd5925db7230ce
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449073"
---
# <a name="tutorial-create-a-graphical-runbook"></a>チュートリアル:グラフィカル Runbook を作成する

このチュートリアルでは、Azure Automation で [グラフィカルな Runbook](../automation-runbook-types.md#graphical-runbooks) を作成する手順を説明します。 Azure portal のグラフィカル エディターを使用して、グラフィカル PowerShell ワークフロー Runbook を作成および編集できます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 簡単なグラフィカル Runbook を作成する
> * Runbook をテストして発行する
> * Runbook ジョブの状態を実行、追跡する
> * Runbook パラメーターと条件付きリンクで Azure 仮想マシンを起動するように Runbook を更新する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

* [Azure 実行アカウント](../create-run-as-account.md)を持つ [Automation アカウント](../index.yml)。Runbook の保存と Azure リソースの認証に使用します。 このアカウントには、仮想マシンを開始および停止するアクセス許可が必要です。
* Automation アカウント用の PowerShell モジュール **Az.Accounts** および **Az.Compute**。 詳細については、「[Azure Automation でモジュールを管理する](../shared-resources/modules.md)」をご覧ください。
* [Azure 仮想マシン](../../virtual-machines/windows/quick-create-portal.md) (VM)。 マシンを停止して起動するので、運用 VM は使用しないでください。 VM が **停止した** 状態で開始します。

## <a name="create-runbook"></a>Runbook を作成する

最初に、`Hello World` というテキストを出力する簡単な Runbook を作成します。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. Azure Portal で、Automation アカウントに移動します。

1. **[プロセス オートメーション]** の **[Runbook]** を選択して、 **[Runbook]** ページを開きます。

1. **[Runbook の作成]** を選択して、 **[Runbook の作成]** ページを開きます。

1. Runbook に `MyFirstRunbook-Graphical` という名前を付けます。

1. **[Runbook の種類]** ドロップダウン メニューから、 **[グラフィック]** を選択します。

   :::image type="content" source="../media/automation-tutorial-runbook-graphical/create-graphical-runbook.png" alt-text="Runbook 入力ページを作成します。":::

1. **[作成]** をクリックして Runbook を作成し、グラフィカル エディターの **[グラフィック Runbook の編集]** ページを開きます。

## <a name="add-activities"></a>アクティビティを追加する

エディターの左側には、**ライブラリ コントロール** があります。 中央には **キャンバス** があります。 右側には **構成コントロール** があります。 **ライブラリ コントロール** を使用すると、Runbook に追加するアクティビティを選択できます。 Runbook のテキストを出力する `Write-Output` コマンドレットを追加します。

1. **ライブラリ コントロール** の検索フィールドに「`Write-Output`」と入力します。

    ![Microsoft.PowerShell.Utility](../media/automation-tutorial-runbook-graphical/search-powershell-cmdlet-writeoutput.png)

1. 一覧の一番下までスクロールします。 **[Write-Output]** を右クリックし、 **[キャンバスに追加]** を選択します。 コマンドレット名の横にある省略記号 [...] を選択し、 **[キャンバスに追加]** を選択してもかまいません。

1. **キャンバス** で **[Write-Output]** アクティビティをクリックします。 この操作によって **[構成コントロール]** ページが設定され、アクティビティを構成できます。

1. **[構成コントロール]** の **[ラベル]** フィールドの既定値はコマンドレットの名前ですが、もっとわかりやすい名前に変更できます。 それを `Write Hello World to output` に変更します。

1. **[パラメーター]** を選択し、コマンドレットのパラメーターの値を指定します。

   一部のコマンドレットには複数のパラメーター セットがあり、どれを使用するかを選択する必要があります。 この場合、`Write-Output` のパラメーター セットは 1 つだけです。

1. **[アクティビティ パラメーター構成]** ページで、`INPUTOBJECT` パラメーターを選択して **[パラメーター値]** ページを開きます。 このパラメーターを使用して、出力ストリームに送信するテキストを指定します。

1. **[データ ソース]** ドロップダウン メニューには、パラメーター値の設定に使用できるソースが含まれます。 このメニューで、 **[PowerShell 式]** を選択します。

   別のアクティビティ、Automation の資産、PowerShell 式などのソースからの出力を使用できます。 この場合、出力は単に `Hello World` です。 PowerShell 式を使用して文字列を指定することができます。

1. **[式]** テキスト ボックスに `"Hello World"` と入力し、 **[OK]** を 2 回選択してグラフィカル エディターに戻ります。

1. **[保存]** を選択して Runbook を保存します。

## <a name="test-the-runbook"></a>Runbook をテストする

Runbook を発行して運用環境で使用できるようにする前に、テストして正常に動作することを確認します。 Runbook をテストすると、そのドラフト バージョンが実行され、その出力を対話形式で表示できます。

1. グラフィカル エディターで、 **[テスト] ペイン** を選択して **[テスト]** ペインを開きます。

1. **[開始]** を選択してテストを開始します。

   [Runbook ジョブ](../automation-runbook-execution.md)が作成され、その状態がペインに表示されます。 ジョブの最初の状態は `Queued` であり、クラウドで runbook worker が使用可能になるのをジョブが待機していることを示しています。 worker がジョブを要求すると、状態は `Starting` に変わります。 最後に、Runbook の実行が実際に開始されると、状態は `Running` になります。

   Runbook ジョブが完了すると、[テスト] ウィンドウにその出力が表示されます。 ここでは、`Hello World` が表示されます。

   :::image type="content" source="../media/automation-tutorial-runbook-graphical/runbook-test-results.png" alt-text="Hello World Runbook の出力。":::

1. 右上隅 の **[X]** を選択して **[テスト]** ペインを閉じ、グラフィカル エディターに戻ります。

## <a name="publish-and-start-the-runbook"></a>Runbook を発行して開始する

作成した Runbook はまだドラフト モードであり、運用環境で実行できるようにするには、まず発行する必要があります。 Runbook を発行するときは、既存の発行済みバージョンをドラフト バージョンで上書きします。

1. グラフィカル エディターで、 **[発行]** を選択して Runbook を発行し、確認を要求されたら **[はい]** をクリックします。 **Runbook** の [概要] ページに戻ります。

1. **Runbook** の [概要] ページの **[ステータス]** の値は、 **[発行済み]** になっています。

1. **[開始]** を選択し、確認を求められたら **[はい]** をクリックして Runbook を開始し、 **[ジョブ]** ページを開きます。

   上部のオプションを使用すると、Runbook の即時開始、将来の開始スケジュールの設定、または Runbook を HTTP 呼び出しで開始できるようにする [Webhook](../automation-webhooks.md) の作成を行うことができます。

   :::image type="content" source="../media/automation-tutorial-runbook-graphical/published-status.png" alt-text="[概要] ページと [発行済み] 状態。":::

1. **[ジョブ]** ページで、 **[ステータス]** フィールドに **[完了]** と表示されていることを確認します。

1. **[出力]** を選択して `Hello World` が表示されていることを確認します。

1. **[すべてのログ]** を選択して Runbook ジョブのストリームを表示し、唯一のエントリを選択して **[Job stream details]\(ジョブ ストリームの詳細情報\)** ページを開きます。 `Hello World` だけが表示されるはずです。

    **[すべてのログ]** には、詳細やエラーのストリームなど、Runbook ジョブの他のストリームも表示できます (Runbook でそれらへの書き込みが行われている場合)。

1. **[Job stream details]\(ジョブ ストリーム の詳細情報\)** ページ、次に **[ジョブ]** ページ を閉じて、**Runbook** の [概要] ページに戻ります。

1. **[リソース]** で、 **[ジョブ]** を選択して Runbook のすべてのジョブを表示します。 [ジョブ] ページには、自分の Runbook で作成されたすべてのジョブの一覧が表示されます。 ジョブを 1 回実行しただけであるため、一覧に表示されるジョブは 1 つだけです。

1. このジョブを選択すると、Runbook を開始したときに表示されたものと同じ **[ジョブ]** ページが開きます。

1. **[ジョブ]** ページを閉じ、左側のメニューから **[概要]** を 選択します。

## <a name="create-variable-assets"></a>変数アセットを作成する

Runbook をテストして発行しましたが、これまでのところ Azure リソースの管理に役立つことは何もしていません。 認証を行うように Runbook を構成する前に、サブスクリプション ID を保持する変数を作成し、認証を行うアクティビティを設定して、この変数を参照する必要があります。 サブスクリプション コンテキストへの参照を含めると、複数のサブスクリプションを簡単に使用できます。

1. **[概要]** で、 **[サブスクリプション ID]** の横にある **[クリップボードにコピー]** アイコンを選択します。

1. **[Runbook]** ページを閉じて、 **[Automation アカウント]** ページに戻ります。

1. **[共有リソース]** で **[変数]** を選択します。

1. **[変数の追加]** を選択して **[新しい変数]** ページを開きます。

1. **[新しい変数]** ページ で、次の値を設定します。

    | フィールド| 値|
    |---|---|
    |値|<kbd>CTRL キーを押しながら V キー</kbd>を押して、サブスクリプション ID を貼り付けます。|
    |名前 |「`AzureSubscriptionId`」と入力します。|
    |Type|既定値 **[文字列]** を維持します。|
    |暗号化|既定値 **[いいえ]** を維持します。|

1. **[作成]** を選択して変数を作成し、 **[変数]** ページに戻ります。

1. **[プロセス オートメーション]** で **[Runbook]** 、ご自身の Runbook である **MyFirstRunbook-Graphical** の順に選択します。

## <a name="add-authentication"></a>認証を追加する

サブスクリプション ID を保持する変数を作成したので、サブスクリプションの実行資格情報を使用して認証を行うように Runbook を構成できます。 認証を構成するには、Azure 実行接続を資産として追加します。 また、[Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) コマンドレットと [Set-AzContext](/powershell/module/az.accounts/Set-AzContext) コマンドレットをキャンバスに追加する必要もあります。

> [!NOTE]
> PowerShell Runbook の場合、`Add-AzAccount` と `Add-AzureRMAccount` は `Connect-AzAccount` の別名です。 これらの別名は、グラフィカルな Runbook では使用できません。 グラフィカルな Runbook で使用できるのは、`Connect-AzAccount` のみです。

1. **[Runbook]** ページで、 **[編集]** を選択してグラフィカル エディターに戻ります。

1. `Write Hello World to output` アクティビティは必要なくなりました。 そのアクティビティを選択すると、アクティビティの右上隅に省略記号が表示されます。 省略記号は見えにくい場合があります。 省略記号を選択し、 **[削除]** を選択します。

1. **ライブラリ コントロール** から、 **[資産]**  >  **[接続]**  >  **[AzureRunAsConnection]** に移動します。 省略記号を選択し、 **[キャンバスに追加]** を選択します。

1. **構成コントロール** で、**ラベル** の値を `Get-AutomationConnection` から `Get Run As Connection` に変更します。

1. **ライブラリ コントロール** の検索フィールドで、「`Connect-AzAccount`」と入力します。

1. `Connect-AzAccount` をキャンバスに追加し、そのアクティビティを `Get Run As Connection` の下にドラッグします。

1. `Get Run As Connection` にポインターを合わせて、図形の下部に円を表示します。 その円を選択したままにすると、矢印が表示されます。 矢印を `Connect-AzAccount` までドラッグしてリンクを作成します。 Runbook は `Get Run As Connection` で開始され、次に `Connect-AzAccount` が実行されます。

    ![アクティビティ間のリンクの作成](../media/automation-tutorial-runbook-graphical/runbook-link-auth-activities.png)

1. **キャンバス** で `Connect-AzAccount` を選択します。

1. **構成コントロール** で、**ラベル** を `Connect-AzAccount` から `Login to Azure` に変更します。

1. **[パラメーター]** を選択して、 **[アクティビティ パラメーターの構成]** ページを開きます。

1. `Connect-AzAccount` コマンドレットには複数のパラメーター セットがあり、パラメーターの値を指定する前に、パラメーター セットを 1 つ選択する必要があります。 **[パラメーター セット]** を選択してから、 **[ServicePrincipalCertificateWithSubscriptionId]** を選択します。 名前が似ていますが、 **[ServicePrincipalCertificateFileWithSubscriptionId]** を選択しないように注意してください

   このパラメーター セットのパラメーターが、 **[アクティビティ パラメーターの構成]** ページに表示されます。

    ![Azure アカウントのパラメーターを追加する](../media/automation-tutorial-runbook-graphical/Add-AzureRmAccount-params.png)

1. **[CERTIFICATETHUMBPRINT]** を選択して、 **[パラメーター値]** ページを開きます。
    1. **[データ ソース]** ドロップダウン メニューから、 **[アクティビティの出力]** を選択します。
    1. **[データの選択]** から、 **[Get Run As Connection]** を選択します。
    1. **[フィールド パス]** テキスト ボックスに、「`CertificateThumbprint`」と入力します。
    1. **[OK]** を選択して、 **[アクティビティ パラメーターの構成]** ページに戻ります。

1. **[SERVICEPRINCIPAL]** を選択して、 **[パラメーター値]** ページを開きます。
    1. **[データ ソース]** ドロップダウン メニューから、 **[定数値]** を選択します。
    1. オプション **[True]** を選択します。
    1. **[OK]** を選択して、 **[アクティビティ パラメーターの構成]** ページに戻ります。

1. **[TENANT]** を選択して、 **[パラメーター値]** ページを開きます。
    1. **[データ ソース]** ドロップダウン メニューから、 **[アクティビティの出力]** を選択します。
    1. **[データの選択]** から、 **[Get Run As Connection]** を選択します。
    1. **[フィールド パス]** テキスト ボックスに、「`TenantId`」と入力します。
    1. **[OK]** を選択して、 **[アクティビティ パラメーターの構成]** ページに戻ります。

1. **[APPLICATIONID]** を選択して、 **[パラメーター値]** ページを開きます。
    1. **[データ ソース]** ドロップダウン メニューから、 **[アクティビティの出力]** を選択します。
    1. **[データの選択]** から、 **[Get Run As Connection]** を選択します。
    1. **[フィールド パス]** テキスト ボックスに、「`ApplicationId`」と入力します。
    1. **[OK]** を選択して、 **[アクティビティ パラメーターの構成]** ページに戻ります。

1. **[OK]** を選択して、グラフィカル エディターに戻ります。

1. **ライブラリ コントロール** の検索フィールドで、「`Set-AzContext`」と入力します。

1. `Set-AzContext` をキャンバスに追加し、そのアクティビティを `Login to Azure` の下にドラッグします。

1. **構成コントロール** で、**ラベル** を `Set-AzContext` から `Specify Subscription Id` に変更します。

1. **[パラメーター]** を選択して、 **[アクティビティ パラメーターの構成]** ページを開きます。

1. `Set-AzContext` コマンドレットには複数のパラメーター セットがあり、パラメーターの値を指定する前に、パラメーター セットを 1 つ選択する必要があります。 **[パラメーター セット]** を選択してから **[Subscription]** を選択します。 このパラメーター セットのパラメーターが、 **[アクティビティ パラメーターの構成]** ページに表示されます。

1. **[SUBSCRIPTION]** を選択して、 **[パラメーター値]** ページを開きます。
    1. **[データ ソース]** ドロップダウン メニューから、 **[Variable asset]\(変数資産\)** を選択します。
    1. 変数の一覧から **[AzureSubscriptionId]** を選択します。
    1. **[OK]** を選択して、 **[アクティビティ パラメーターの構成]** ページに戻ります。

1. **[OK]** を選択して、グラフィカル エディターに戻ります。

1. `Login to Azure` と `Specify Subscription Id` の間のリンクを作成します。 この時点で、Runbook は次のようになります。

    :::image type="content" source="../media/automation-tutorial-runbook-graphical/runbook-auth-config.png" alt-text="矢印を [Specify Subscription ID] にドラッグした後の Runbook のスクリーンショット。":::

## <a name="add-activity-to-start-a-virtual-machine"></a>仮想マシンを開始するアクティビティを追加する

ここで、仮想マシンを起動する `Start-AzVM` アクティビティを追加する必要があります。 Azure サブスクリプション内の任意の仮想マシンを選択できます。ここでは、その名前を [Start-AzVM](/powershell/module/az.compute/start-azvm) コマンドレットにハードコーディングします。

1. **ライブラリ コントロール** の検索フィールドで、「`Start-AzVM`」と入力します。

1. `Start-AzVM` をキャンバスに追加し、そのアクティビティを `Specify Subscription Id` の下にドラッグします。

1. **構成コントロール** から、 **[パラメーター]** を選択し、 **[アクティビティ パラメーターの構成]** ページを開きます。

1. **[パラメーター セット]** を選択し、 **[ResourceGroupNameParameterSetName]** を選択します。 このパラメーター セットのパラメーターが、 **[アクティビティ パラメーターの構成]** ページに表示されます。 パラメーター **RESOURCEGROUPNAME** と **NAME** の横に付いている感嘆符は、それらが必須のパラメーターであることを示します。 どちらのフィールドも文字列値が想定されています。

1. **[RESOURCEGROUPNAME]** を選択して、 **[パラメーター値]** ページを開きます。
    1. **[データ ソース]** ドロップダウン メニューから、 **[PowerShell 式]** を選択します。
    1. **[式]** テキスト ボックスに、リソース グループの名前を二重引用符で囲んで入力します。
    1. **[OK]** を選択して、 **[アクティビティ パラメーターの構成]** ページに戻ります。

1. **[NAME]** を選択して、 **[パラメーター値]** ページを開きます。
    1. **[データ ソース]** ドロップダウン メニューから、 **[PowerShell 式]** を選択します。
    1. **[式]** テキスト ボックスに、仮想マシンの名前を二重引用符で囲んで入力します。
    1. **[OK]** を選択して、 **[アクティビティ パラメーターの構成]** ページに戻ります。

1. **[OK]** を選択して、グラフィカル エディターに戻ります。

1. `Specify Subscription Id` と `Start-AzVM` の間のリンクを作成します。 この時点で、Runbook は次のようになります。

    ![Runbook Start-AzVM の出力](../media/automation-tutorial-runbook-graphical/runbook-startvm.png)

1. **[テスト] ペイン** を選択して、Runbook をテストできるようにします。

1. **[開始]** を選択してテストを開始します。

1. 終わったら、VM が開始されたことを確認します。 その後、後の手順のために VM を停止します。

1. Runbook のグラフィカル エディターに戻ります。

## <a name="add-input-parameters"></a>入力パラメーターを追加する

Runbook ではこの時点で、`Start-AzVM` コマンドレットに対して指定したリソース グループ内の VM が起動されます。 Runbook を開始するときに名前とリソース グループの両方を指定できれば、Runbook はいっそう便利になります。 その機能を提供するための入力パラメーターを Runbook に追加してみましょう。

1. グラフィカル エディターの上部のメニュー バーから、 **[入出力]** を選択します。

1. **[入力の追加]** を選択して、 **[Runbook Input Parameter]\(Runbook 入力パラメーター\)** ページを開きます。

1. **[Runbook Input Parameter]\(Runbook 入力パラメーター\)** ページで、次の値を設定します。

    | フィールド| 値|
    |---|---|
    |名前| 「`VMName`」と入力します。|
    |Type|既定値 **[文字列]** を維持します。|
    |Mandatory|値を **[はい]** に変更します。|

1. **[OK]** を選択して **[入出力]** ページに戻ります

1. **[入力の追加]** を選択して、 **[Runbook Input Parameter]\(Runbook 入力パラメーター\)** ページをもう一度開きます。

1. **[Runbook Input Parameter]\(Runbook 入力パラメーター\)** ページで、次の値を設定します。

    | フィールド| 値|
    |---|---|
    |名前| 「`ResourceGroupName`」と入力します。|
    |Type|既定値 **[文字列]** を維持します。|
    |Mandatory|値を **[はい]** に変更します。|

1. **[OK]** を選択して **[入出力]** ページに戻ります。 このページは次のようになります。

    ![Runbook の入力パラメーター](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-outputs.png)

1. **[OK]** を選択して、グラフィカル エディターに戻ります。

1. 新しい入力はすぐに使用できない場合があります。 **[保存]** を選択し、グラフィカル エディターを閉じてから、グラフィカル エディターをもう一度開きます。 これで新しい入力を使用できるようになります。

1. `Start-AzVM` アクティビティを選択してから **[パラメーター]** を選択して、 **[アクティビティ パラメーターの構成]** ページを開きます。

1. 前に構成したパラメーター **RESOURCEGROUPNAME** の **[データ ソース]** を **[Runbook の入力]** に変更し、 **[ResourceGroupName]** を選択します。 **[OK]** を選択します。

1. 前に構成したパラメーター **NAME** の **[データ ソース]** を **[Runbook の入力]** に変更し、 **[VMName]** を選択します。 **[OK]** を選択します。 このページは次のようになります。

    ![Start-AzVM のパラメーター](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-runbookinput.png)

1. **[OK]** を選択して、グラフィカル エディターに戻ります。

1. **[保存]** 、 **[テスト] ペイン** の順に選択します。 作成した 2 つの入力変数の値を指定できるようになったことを確認します。

1. **[テスト]** ペインを閉じてグラフィカル エディターに戻ります。

1. **[発行]** を選択し、確認を要求されたら **[はい]** を選択して、新しいバージョンの Runbook を発行します。 **Runbook** の [概要] ページに戻ります。

1. **[開始]** を選択して、 **[Runbook の開始]** ページを開きます。

1. パラメーター `VMNAME` と `RESOURCEGROUPNAME` に適切な値を入力します。 **[OK]** をクリックします。 これで、 **[ジョブ]** ページが開きます。

1. ジョブを監視し、 **[ステータス]** が **[完了]** になった後に VM が起動したことを確認します。 その後、後の手順のために VM を停止します。

1. Runbook のグラフィカル エディターに戻ります。

## <a name="create-a-conditional-link"></a>条件付きリンクを作成する

次に、まだ起動されていない場合にのみ VM の起動を試みるように Runbook を変更できます。 これを行うには、VM のインスタンス レベルの状態を取得する [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) コマンドレットを追加します。 次に、`Get Status` という名前の PowerShell ワークフロー コード モジュールを追加できます。これには、VM の状態が実行中か停止しているかを確認するための PowerShell コード スニペットが含まれています。 `Get Status` モジュールの条件付きリンクでは、現在の実行状態が停止の場合にのみ、`Start-AzVM` が実行されます。 この手順の最後では、Runbook で`Write-Output` コマンドレットを使用して、VM が正常に起動されたかどうかを通知するメッセージを出力します。

1. グラフィカル エディターで、`Specify Subscription Id` と `Start-AzVM` の間のリンクを右クリックし、 **[削除]** を選択します。

1. **ライブラリ コントロール** の検索フィールドで、「`Get-AzVM`」と入力します。

1. `Get-AzVM` をキャンバスに追加し、そのアクティビティを `Specify Subscription Id` の下にドラッグします。

1. **構成コントロール** から、 **[パラメーター]** を選択し、 **[アクティビティ パラメーターの構成]** ページを開きます。

   **[パラメーター セット]** を選択し、 **[GetVirtualMachineInResourceGroupParamSet]** を選択します。 このパラメーター セットのパラメーターが、 **[アクティビティ パラメーターの構成]** ページに表示されます。 パラメーター **RESOURCEGROUPNAME** と **NAME** の横に付いている感嘆符は、それらが必須のパラメーターであることを示します。 どちらのフィールドも文字列値が想定されています。

1. **[RESOURCEGROUPNAME]** を選択して、 **[パラメーター値]** ページを開きます。
    1. **[データ ソース]** ドロップダウン メニューから、 **[Runbook の入力]** を選択します。
    1. パラメーター **[ResourceGroupName]** を選択します。
    1. **[OK]** を選択して、 **[アクティビティ パラメーターの構成]** ページに戻ります。

1. **[NAME]** を選択して、 **[パラメーター値]** ページを開きます。
    1. **[データ ソース]** ドロップダウン メニューから、 **[Runbook の入力]** を選択します。
    1. パラメーター **[VMName]** を選択します。
    1. **[OK]** を選択して、 **[アクティビティ パラメーターの構成]** ページに戻ります。

1. **[STATUS]** を選択して、 **[パラメーター値]** ページを開きます。
    1. **[データ ソース]** ドロップダウン メニューから、 **[定数値]** を選択します。
    1. オプション **[True]** を選択します。
    1. **[OK]** を選択して、 **[アクティビティ パラメーターの構成]** ページに戻ります。

1. **[OK]** を選択して、グラフィカル エディターに戻ります。

1. `Specify Subscription Id` と `Get-AzVM` の間のリンクを作成します。

1. **ライブラリ コントロール** の検索フィールドをクリアし、 **[Runbook の制御]**  >  **[コード]** に移動します。 省略記号、 **[キャンバスに追加]** の順に選択します。 アクティビティを `Get-AzVM` の下にドラッグします。

1. **構成コントロール** で、**ラベル** を `Code` から `Get Status` に変更します。

1. **構成コントロール** から、 **[コード]** を選択して **[コード エディター]** ページを開きます。

1. 次のコード スニペットを **PowerShell コード** のテキスト ボックスに貼り付けます。

    ```powershell
    $Statuses = $ActivityOutput['Get-AzVM'].Statuses
    $StatusOut = ""
    foreach ($Status in $Statuses) {
      if($Status.Code -eq "Powerstate/running")
        {$StatusOut = "running"}
      elseif ($Status.Code -eq "Powerstate/deallocated")
        {$StatusOut = "stopped"}
    }
    $StatusOut
    ```

1. **[OK]** を選択して、グラフィカル エディターに戻ります。

1. `Get-AzVM` と `Get Status` の間のリンクを作成します。

1. `Get Status` と `Start-AzVM` の間のリンクを作成します。 この時点で、Runbook は次のようになります。

    ![コード モジュールを含む Runbook](../media/automation-tutorial-runbook-graphical/runbook-startvm-get-status.png)  

1. `Get Status` と `Start-AzVM` の間のリンクを選択します。

1. **構成コントロール** で、 **[条件の適用]** を **[はい]** に変更します。 リンクは、条件が true の場合にのみ対象のアクティビティが実行されることを示す破線になります。  

1. **[条件式]** に、「`$ActivityOutput['Get Status'] -eq "Stopped"`」と入力します。 `Start-AzVM` は、VM が停止している場合にのみ実行されるようになりました。

1. **ライブラリ コントロール** の検索フィールドで、「`Write-Output`」と入力します。

1. `Write-Output` をキャンバスに追加し、そのアクティビティを `Start-AzVM` の下にドラッグします。

1. アクティビティの省略記号を選択し、 **[重複]** を選択します。 重複するアクティビティを、最初のアクティビティの右側にドラッグします。

1. 最初の `Write-Output` アクティビティを選択します。
    1. **構成コントロール** で、**ラベル** を `Write-Output` から `Notify VM Started` に変更します。
    1. **[パラメーター]** を選択して、 **[アクティビティ パラメーターの構成]** ページを開きます。
    1. **[INPUTOBJECT]** を選択して、 **[パラメーター値]** ページを開きます。
    1. **[データ ソース]** ドロップダウン メニューから、 **[PowerShell 式]** を選択します。
    1. **[式]** テキスト ボックスに `"$VMName successfully started."` を入力します。
    1. **[OK]** を選択して、 **[アクティビティ パラメーターの構成]** ページに戻ります。
    1. **[OK]** を選択して、グラフィカル エディターに戻ります。

1. 最初の `Write-Output1` アクティビティを選択します。
    1. **構成コントロール** で、**ラベル** を `Write-Output1` から `Notify VM Start Failed` に変更します。
    1. **[パラメーター]** を選択して、 **[アクティビティ パラメーターの構成]** ページを開きます。
    1. **[INPUTOBJECT]** を選択して、 **[パラメーター値]** ページを開きます。
    1. **[データ ソース]** ドロップダウン メニューから、 **[PowerShell 式]** を選択します。
    1. **[式]** テキスト ボックスに `"$VMName could not start."` を入力します。
    1. **[OK]** を選択して、 **[アクティビティ パラメーターの構成]** ページに戻ります。
    1. **[OK]** を選択して、グラフィカル エディターに戻ります。

1. `Start-AzVM` と `Notify VM Started` の間のリンクを作成します。

1. `Start-AzVM` と `Notify VM Start Failed` の間のリンクを作成します。

1. `Notify VM Started` へのリンクを選択し、 **[条件の適用]** を **[はい]** に変更します。

1. **[条件式]** に「`$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`」と入力します。 これで、この `Write-Output` コントロールは、VM が正常に起動された場合にのみ実行されるようになりました。

1. `Notify VM Start Failed` へのリンクを選択します。

1. **[制御] ページ** から、 **[条件の適用]** で **[はい]** を選択します。

1. **[条件式]** テキスト ボックスに、「`$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`」と入力します。 これで、この `Write-Output` コントロールは、VM が正常に起動されなかった場合にのみ実行されるようになりました。 Runbook は次の図のようになります。

    ![Write-Output がある Runbook](../media/automation-tutorial-runbook-graphical/runbook-startazurermvm-complete.png)

1. Runbook を保存してテスト ウィンドウを開きます。

1. VM が停止している状態で Runbook を開始すると、マシンが起動します。

## <a name="next-steps"></a>次のステップ

* グラフィック作成の詳細については、[Azure Automation でのグラフィカル Runbook の作成](../automation-graphical-authoring-intro.md)に関する記事を参照してください。
* PowerShell Runbook の使用を開始するには、「[PowerShell Runbook を作成する](automation-tutorial-runbook-textual-powershell.md)」を参照してください。
* PowerShell Workflow Runbook の使用を開始するには、「[PowerShell Workflow Runbook を作成する](automation-tutorial-runbook-textual.md)」を参照してください。
* PowerShell コマンドレットのリファレンスについては、「[Az.Automation](/powershell/module/az.automation)」をご覧ください。
