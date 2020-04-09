---
title: Azure Automation での Runbook の出力および メッセージ
description: Azure Automation で Runbook から出力とエラー メッセージを作成および取得する方法を説明します。
services: automation
ms.subservice: process-automation
ms.date: 12/04/2018
ms.topic: conceptual
ms.openlocfilehash: 457b2d2211ea1ba5fa36cec4b7e9a214f5bcad77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367093"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Azure Automation での Runbook の出力および メッセージ

ほとんどの Azure Automation Runbook は、何らかの形式の出力があります。 この出力には、ユーザーへのエラー メッセージや別の Runbook で使用することを目的とした複合オブジェクトなどがあります。 Windows PowerShell では、スクリプトまたはワークフローから出力を送信するための [複数のストリーム](/powershell/module/microsoft.powershell.core/about/about_redirection) が提供されます。 Azure Automation は、これらの各ストリームで異なる動作をします。 Runbook を作成するときには、ストリームの使用に関するベスト プラクティスに従ってください。

次の表では、発行済の Runbook と [Runbook のテスト](automation-testing-runbook.md)中に関して、各ストリームの概要と Azure portal での動作について示しています。 出力ストリームは、Runbook 間の通信に使用されるメイン ストリームです。 他のストリームは、ユーザーに情報を伝えることを目的としたメッセージ ストリームに分類されます。 

| ストリーム | 説明 | 公開済み | テスト |
|:--- |:--- |:--- |:--- |
| エラー |ユーザー向けのエラー メッセージ 例外とは異なり、既定では Runbook はエラー メッセージ発行後に実行を継続します。 |ジョブ履歴に書き込まれます |[テスト出力] ウィンドウに表示されます |
| デバッグ |対話型ユーザー向けのメッセージ。 Runbook では使用しないでください。 |ジョブ履歴には書き込まれません |[テスト出力] ウィンドウには表示されません |
| 出力 |他の Runbook によって使用されることを目的とするオブジェクト。 |ジョブ履歴に書き込まれます |[テスト出力] ウィンドウに表示されます |
| 進行状況 |Runbook の各アクティビティの前後に自動的に生成されるレコード。 Runbook では、独自の進行状況レコードを作成しないでください。それらは対話型ユーザー向けです。 |Runbook の進行状況ログが有効な場合のみ、ジョブ履歴に書き込まれます |[テスト出力] ウィンドウには表示されません |
| "詳細" |一般情報またはデバッグ情報を提供するメッセージ。 |Runbook の詳細ログが有効な場合のみ、ジョブ履歴に書き込まれます |Runbook で `VerbosePreference` 変数が Continue に設定されている場合のみ、[テスト出力] ウィンドウに表示されます |
| 警告 |ユーザー向けの警告メッセージ。 |ジョブ履歴に書き込まれます |[テスト出力] ウィンドウに表示されます |

>[!NOTE]
>この記事は、新しい Azure PowerShell Az モジュールを使用するために更新されました。 AzureRM モジュールはまだ使用でき、少なくとも 2020 年 12 月までは引き続きバグ修正が行われます。 Az モジュールと AzureRM の互換性の詳細については、「[Introducing the new Azure PowerShell Az module (新しい Azure PowerShell Az モジュールの概要)](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)」を参照してください。 Hybrid Runbook Worker での Az モジュールのインストール手順については、「[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)」を参照してください。 Automation アカウントについては、「[Azure Automation の Azure PowerShell モジュールを更新する方法](automation-update-azure-modules.md)」に従って、モジュールを最新バージョンに更新できます。

## <a name="output-stream"></a>出力ストリーム

出力ストリームは、スクリプトまたはワークフローが正しく実行された場合に作成されるオブジェクトの出力で使用されます。 Azure Automation では、このストリームは主に、[現在の Runbook](automation-child-runbooks.md) を呼び出す親 Runbook が使用するオブジェクトで使用されます。 親 Runbook が [Runbook をインラインで呼び出す](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution)と、子は出力ストリームからのデータを親に返します。 

Runbook で出力ストリームを使用して一般情報がクライアントに伝達されるのは、それが別の Runbook から呼び出されない場合のみです。 ただし通常は、ベスト プラクティスとして、Runbook で[詳細ストリーム](#verbose-stream)を使用してユーザーに一般情報を伝えてください。

Runbook で出力ストリームにデータを書き込むには、[Write-Output](https://technet.microsoft.com/library/hh849921.aspx) を使用します。 または、スクリプト内の独自の行にオブジェクトを配置することもできます。

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="handling-output-from-a-function"></a>関数からの出力を処理する

Runbook 関数が出力ストリームに書き込むと、出力が Runbook に返されます。 Runbook がその出力を変数に代入する場合、出力は出力ストリームに書き込まれません。 関数内から他のストリームへの書き込みは、Runbook の対応するストリームに書き込まれます。 次の PowerShell Workflow Runbook のサンプルについて考えてみましょう。

```powershell
Workflow Test-Runbook
{
  Write-Verbose "Verbose outside of function" -Verbose
  Write-Output "Output outside of function"
  $functionOutput = Test-Function
  $functionOutput

  Function Test-Function
  {
    Write-Verbose "Verbose inside of function" -Verbose
    Write-Output "Output inside of function"
  }
}
```

Runbook ジョブの出力ストリームは次のとおりです。

```output
Output inside of function
Output outside of function
```

Runbook ジョブの詳細ストリームは次のとおりです。

```output
Verbose outside of function
Verbose inside of function
```

Runbook を発行したら、それを開始する前に、詳細ストリーム出力を取得するために Runbook の設定で詳細ログを有効にする必要もあります。

### <a name="declaring-output-data-type"></a>出力のデータ型の宣言

出力 データ型の例を以下に示します。

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>ワークフローで出力のデータ型を宣言する

ワークフローでは、[OutputType 属性](https://technet.microsoft.com/library/hh847785.aspx)を使用して、その出力のデータ型を指定します。 この属性は実行時に影響はありませんが、設計時にユーザーに Runbook の予想される出力を示します。 Runbook 用のツール セットは進化し続けるため、設計時に出力のデータ型を宣言することの重要性が高まります。 したがって、ベスト プラクティスは、作成するすべての Runbook にこの宣言を含めることです。

次のサンプル Runbook は、文字列オブジェクトを出力し、その出力の型宣言が含まれています。 Runbook が特定の型の配列を出力する場合でも、型の配列ではなく、型を指定してください。

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>グラフィカル ワークフローで出力のデータ型を宣言する

グラフィカルまたはグラフィカル PowerShell ワークフローの Runbook で出力型を宣言するには、 **[入力と出力]** メニュー オプションを選択し、出力型を入力します。 親 Runbook で参照するときに簡単に識別できるように、完全な .NET クラス名を使用することをお勧めします。 完全な名前を使用することにより、そのクラスのすべてのプロパティが Runbook のデータバスに公開され、プロパティを条件付きロジック、ログ記録、他の Runbook アクティビティでの値としての参照で使用する際に柔軟性が高まります。<br> ![Runbook Input and Output option](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>[入力と出力] プロパティ ウィンドウで **[出力の種類]** フィールドに値を入力した後は、コントロールの外側をクリックして、入力内容を認識させる必要があります。

次の例では、入力と出力の機能を説明するために 2 つのグラフィカル Runbook を示します。 モジュール式の Runbook デザイン モデルを適用すると、Runbook の 1 つは、実行アカウントを使用した Azure での認証を管理する認証 Runbook テンプレートになります。 2 番目の Runbook は、通常、特定のシナリオを自動化するためのコア ロジックを実行します。ここでは、認証 Runbook テンプレートが実行されます。 [テスト出力] ウィンドウに結果が表示されます。 通常の状況では、この Runbook で子 Runbook からの出力を利用して、リソースに対する操作を行います。

**AuthenticateTo-Azure** Runbook の基本的なロジックは、次のとおりです。<br> ![Authenticate Runbook Template Example](media/automation-runbook-output-and-messages/runbook-authentication-template.png)時の両方の場合の、Azure 管理ポータルでの各ストリームとその動作の簡単な説明を示します。

Runbook には出力型 `Microsoft.Azure.Commands.Profile.Models.PSAzureContext` が含まれています。これは、認証プロファイルのプロパティを返します。<br> ![Runbook Output Type Example](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

この Runbook は単純ですが、ここで呼び出す構成項目が 1 つあります。 最後のアクティビティで `Write-Output` コマンドレットが実行され、`Inputobject` パラメーターの PowerShell 式を使用してプロファイル データが変数に書き込まれます。 このパラメーターは `Write-Output` では必須です。

この例の **Test-ChildOutputType** という 2 番目の Runbook では、2 つのアクティビティが定義されています。<br> ![Example Child Output Type Runbook](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

最初のアクティビティでは、**AuthenticateTo-Azure** Runbook が呼び出されます。 2 番目のアクティビティの実行では、 **[データ ソース]** が **[アクティビティの出力]** に設定設定された `Write-Verbose` コマンドレットが実行されます。 また、 **[フィールド パス]** は **Context.Subscription.SubscriptionName** に設定されています。これは、**AuthenticateTo-Azure** Runbook からのコンテキスト出力です。<br> ![Write-Verbose コマンドレットのパラメーター データ ソース](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

結果の出力は、サブスクリプションの名前です。<br> ![Test-ChildOutputType Runbook Results](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="message-streams"></a>メッセージ ストリーム

出力ストリームとは異なり、メッセージ ストリームはユーザーに情報を伝えます。 さまざまな種類の情報向けに複数のメッセージ ストリームがあり、Azure Automation では各ストリームが異なる方法で処理されます。

### <a name="warning-and-error-streams"></a>警告およびエラー ストリーム

警告およびエラー ストリームは、Runbook で発生する問題をログに記録します。 Azure Automation では、Runbook の実行時に、これらのストリームがジョブ履歴に書き込まれます。 Automation には、Runbook のテスト時に Azure portal の [テスト出力] ウィンドウに出力されたストリームが含まれます。 

既定では、警告またはエラーの後も Runbook は引き続き実行されます。 警告またはエラー時に Runbook を中断するように指定することができます。これを行うには、メッセージを作成する前に、Runbook で[ユーザー設定変数](#preference-variables)を設定します。 たとえば、Runbook を例外の場合と同様にエラーで中断するようにするには、`ErrorActionPreference`変数を Stop に設定します。

警告またはエラー メッセージを作成するには、[Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) または [Write-Error](https://technet.microsoft.com/library/hh849962.aspx) コマンドレットを使用します。 アクティビティでは、警告およびエラー ストリームに書き込むこともできます。

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="debug-stream"></a>デバッグ ストリーム

Azure Automation では、対話型ユーザーにデバッグ メッセージ ストリームが使用されます。 これは Runbook では使用しないでください。

### <a name="verbose-stream"></a>詳細ストリーム

詳細メッセージ ストリームでは、Runbook の操作に関する一般情報がサポートされます。 Runbook ではデバッグ ストリームを使用できないため、Runbook ではデバッグ情報には詳細メッセージを使用する必要があります。 

既定では、パフォーマンス上の理由から、公開された Runbook からの詳細メッセージはジョブ履歴に格納されません。 詳細メッセージを格納するには、Azure portal の **[構成]** タブの **[詳細レコードの記録]** 設定を使用して、公開されている Runbook が詳細メッセージを記録するように構成します。 このオプションを有効にするのは、Runbook のトラブルシューティングやデバッグをする場合のみです。 ほとんどの場合、詳細レコードを記録しないという既定の設定を維持する必要があります。

[Runbook のテスト時](automation-testing-runbook.md)には、Runbook が詳細レコードを記録するように構成されている場合でも、詳細メッセージは表示されません。 [Runbook のテスト時](automation-testing-runbook.md)に詳細メッセージを表示するには、`VerbosePreference` 変数を Continue に設定する必要があります。 この変数が設定されると、Azure portal の [テスト出力] ウィンドウに詳細メッセージが表示されます。

次のコードでは、[Write-Verbose](https://technet.microsoft.com/library/hh849951.aspx) コマンドレットを使用して詳細メッセージを作成しています。

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

## <a name="progress-records"></a>進捗状況レコード

Azure portal の **[構成]** タブ使用して、進行状況レコードを記録するように Runbook を構成できます。 既定の設定では、パフォーマンスを最大化するためにレコードがログに記録されません。 ほとんどの場合、既定の設定をそのまま使用してください。 このオプションを有効にするのは、Runbook のトラブルシューティングやデバッグをする場合のみです。 

進行状況レコードのログ記録を有効にすると、Runbook では、各アクティビティの実行の前後にジョブ履歴にレコードが書き込まれます。 Runbook のテスト時には、Runbook が進行状況レコードを記録するように構成されている場合でも、進行状況メッセージは表示されません。

>[!NOTE]
>[Write-Progress](https://technet.microsoft.com/library/hh849902.aspx) コマンドレットは、対話ユーザー向けに使用するものであるため、Runbook では無効です。

## <a name="preference-variables"></a>ユーザー設定変数

Runbook で特定の Windows PowerShell [ユーザー設定変数](https://technet.microsoft.com/library/hh847796.aspx)を設定して、異なる出力ストリームに送信されるデータへの応答を制御できます。 次の表は、Runbook で使用できるユーザー設定変数と、それらの既定値と有効値を示しています。 Windows PowerShell を Azure Automation の外部で使用する場合は、ユーザー設定変数で使用可能なその他の値があります。

| 変数 | Default value | 有効な値 |
|:--- |:--- |:--- |
| `WarningPreference` |Continue |Stop<br>Continue<br>SilentlyContinue |
| `ErrorActionPreference` |Continue |Stop<br>Continue<br>SilentlyContinue |
| `VerbosePreference` |SilentlyContinue |Stop<br>Continue<br>SilentlyContinue |

次の表は、Runbook で有効なユーザー設定変数値の動作の一覧です。

| 値 | 動作 |
|:--- |:--- |
| Continue |メッセージを記録し、Runbook の実行を続けます。 |
| SilentlyContinue |メッセージを記録せずに Runbook の実行を続けます。 この値はメッセージを無視する効果があります。 |
| Stop |メッセージを記録し、Runbook を中断します。 |

## <a name="retrieving-runbook-output-and-messages"></a><a name="runbook-output"></a>Runbook の出力とメッセージの取得

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>Azure portal で Runbook の出力とメッセージを取得する

Runbook ジョブの詳細は、Azure portal で Runbook の **[ジョブ]** タブを使用して参照できます。 ジョブの概要として、入力パラメーターと[出力ストリーム](#output-stream)、さらにジョブに関する全般情報が表示されます。例外が発生した場合は、その情報も表示されます。 ジョブ履歴には、出力ストリームと[警告およびエラー ストリーム](#warning-and-error-streams)からのメッセージが含まれています。 さらに、詳細および進行状況レコードを記録するように Runbook が構成されている場合は、[詳細ストリーム](#verbose-stream)と[進行状況レコード](#progress-records)からのメッセージが含まれます。

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>Windows PowerShell で Runbook の出力とメッセージを取得する

Windows PowerShell では、[Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) コマンドレットを使用して、Runbook から出力とメッセージを取得できます。 このコマンドレットにはジョブの ID が必要であり、取得するストリームを指定する `Stream` というパラメーターがあります。 このパラメーターに Any の値を指定すると、ジョブのすべてのストリームを取得できます。

次の例は、サンプル Runbook を開始し、完了するまで待機します。 Runbook の実行が完了すると、スクリプトによりジョブから Runbook の出力ストリームが収集されます。

```powershell
$job = Start-AzAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzAutomationJob -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output

# For more detailed job output, pipe the output of Get-AzAutomationJobOutput to Get-AzAutomationJobOutputRecord
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzAutomationJobOutputRecord
```

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>グラフィカル Runbook で Runbook の出力とメッセージを取得する

グラフィカル Runbook では、出力とメッセージの追加のログ記録が、アクティビティ レベルのトレースの形式で使用できます。 トレースには Basic および Detailed の 2 つのレベルがあります。 基本トレースでは、Runbook での各アクティビティの開始および終了時刻と、アクティビティの再試行に関連する情報が表示されます。 例としては、アクティビティの試行数や開始時刻などがあります。 詳細トレースでは、各アクティビティの基本トレース機能に加えて、入出力データのログが含まれます。 

現在、アクティビティ レベルのトレースでは、詳細ストリームを使用してレコードが書き込まれます。 そのため、トレースを有効にするときは、詳細ログ記録を有効にする必要があります。 トレースが有効になっているグラフィカル Runbook では、進行状況レコードを記録する必要はありません。 Basic トレースが同じ目的で機能し、情報がより詳細です。

![グラフィカル作成のジョブ ストリーム ビュー](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

グラフィカル Runbook で詳細ログとトレースを有効にすると、より多くの情報が運用環境の**ジョブ ストリーム** ビューで利用できることが画像から確認できます。 この追加情報は、Runbook での運用上の問題のトラブルシューティングに不可欠です。 

ただし、Runbook の進行状況を追跡してトラブルシューティングを行うためにこの情報が必要な場合を除き、一般的にはトレースをオフにしておくことをお勧めします。 トレース レコードは、特に大量になることがあります。 グラフィカル Runbook のトレースでは、基本または詳細トレースの構成に応じて、アクティビティごとに 2 個から 4 個のレコードを取得できます。

**アクティビティ レベルのトレースを有効にするには:**

1. Azure ポータルで、Automation アカウントを開きます。
2. **[プロセス オートメーション]** セクションの **[Runbook]** を選択し、Runbook の一覧を開きます。
3. [Runbook] ページで、Runbook の一覧からグラフィカル Runbook を選択します。
4. **[設定]** の下の **[ログとトレース]** をクリックします。
5. [ログとトレース] ページの **[詳細レコードのログ]** で、 **[オン]** をクリックして詳細ログを有効にします。
6. **[アクティビティ レベルのトレース]** で、必要なトレースのレベルに基づいて、トレース レベルを **[基本]** または **[詳細]** に変更します。<br>

   ![グラフィカル作成の [ログとトレース] ページ](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>Microsoft Azure Monitor ログで Runbook の出力とメッセージを取得する

Azure Automation では、Runbook ジョブの状態とジョブ ストリームを Log Analytics ワークスペースに送信できます。 Azure Monitor では、次のことを可能にするログがサポートされます。

* Automation ジョブに関する情報を得る。
* Runbook ジョブの状態 (失敗、中断など) に基づいて電子メールまたはアラートをトリガーする。
* ジョブ ストリームをまたぐ高度なクエリを記述する。
* Automation アカウントをまたいでジョブどうしを関連付ける。
* ジョブ履歴を視覚化する。

Azure Monitor ログとの統合を構成して、ジョブ データを収集、関連付け、および操作する方法の詳細については、「[Automation から Azure Monitor ログにジョブの状態とジョブ ストリームを転送する](automation-manage-send-joblogs-log-analytics.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* Runbook の実行、Runbook ジョブの監視、その他の技術的な詳細については、[Runbook ジョブの追跡](automation-runbook-execution.md)に関するページを参照してください。
* 子 Runbook を設計および使用する方法については、「[Azure Automation での子 Runbook](automation-child-runbooks.md)」を参照してください。
* PowerShell (言語リファレンス、学習モジュールを含む) の詳細については、[PowerShell ドキュメント](/powershell/scripting/overview)を参照してください。
