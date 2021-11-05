---
title: Webhook から Azure Automation の Runbook を開始する
description: この記事では、Webhook を使用して、HTTP 呼び出しから Azure Automation の Runbook を開始する方法を説明します。
services: automation
ms.subservice: process-automation
ms.date: 07/21/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e79feb7b45500a53bc7e13557aedcd1c0410554b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026451"
---
# <a name="start-a-runbook-from-a-webhook"></a>webhook から Runbook を開始する

Webhook を使用すると、外部サービスは、1 つの HTTP 要求により、Azure Automation で特定の Runbook を開始することができます。 外部サービスには、Azure DevOps Services、GitHub、Azure Monitor ログ、およびカスタム アプリケーションが含まれます。 このようなサービスでは、Webhook を使用することで、完全な Azure Automation API を実装せずに、Runbook を開始できます。 Webhook と、Runbook の他の 開始方法を比較するには、「[Azure Automation で Runbook を開始する](./start-runbooks.md)」を参照してください。

> [!NOTE]
> Webhook を使用して Python Runbook を開始することはできません。

![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Webhook を使用する TLS 1.2 のクライアント要件を理解するには、「[Azure Automation に対する TLS 1.2](automation-managing-data.md#tls-12-for-azure-automation)」を参照してください。

## <a name="webhook-properties"></a>Webhook のプロパティ

次のテーブルは、Webhook 用に構成する必要があるプロパティについて説明しています。

| プロパティ | 説明 |
|:--- |:--- |
| 名前 |Webhook の名前。 これはクライアントに公開されないため、任意の名前を指定できます。 これはユーザーが Azure Automation の Runbook を識別する場合にのみ使用されます。 ベスト プラクティスとして、webhook を使用するクライアントに関連した名前を webhook に付ける必要があります。 |
| URL |Webhook の URL。 これは、Webhook にリンクされた Runbook を開始するためにクライアントが HTTP POST で呼び出す一意のアドレスです。 これは、Webhook を作成するときに自動的に生成されます。 カスタム URL を指定することはできません。 <br> <br> この URL には、サードパーティのシステムが追加の認証なしに Runbook を呼び出すことを許可するセキュリティ トークンが含まれています。 このため、この URL はパスワードのように扱う必要があります。 セキュリティ上の理由により、この URL は、Webhook を作成するときに Azure portal でのみ表示できます。 将来の使用に備えて、URL を安全な場所にメモしてください。 |
| 有効期限 | Webhook の有効期限。これを過ぎると使用できなくなります。 有効期限は Webhook の作成後、Webhook の有効期限が切れていない限り変更できます。 |
| Enabled | Webhook が既定で作成時に有効になるかどうかを示す設定。 このプロパティを [Disabled]\(無効\) に設定すると、クライアントは Webhook を使用できません。 このプロパティは、Webhook の作成時、または作成後にいつでも設定できます。 |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Webhook が Runbook を開始するときに使用されるパラメーター

Webhook は、Runbook の開始時に使用される Runbook パラメーターの値を定義できます。 Webhook には、必須の Runbook パラメーターの値を含める必要があり、省略可能なパラメーターの値を含めることもできます。 Webhook に対して構成されているパラメーター値は、Webhook の作成後であっても変更できます。 1 つの Runbook にリンクされている複数の Webhook は、それぞれ異なる Runbook パラメーター値を使用できます。 Webhook を使用して Runbook を開始した場合、クライアントは Webhook で定義されているパラメーターの値をオーバーライドできません。

クライアントからデータを受け取るために、Runbook では `WebhookData` という名前の 1 つのパラメーターがサポートされています。 このパラメーターは、クライアントが POST 要求に含めるデータを格納するオブジェクトを定義します。

![WebhookData のプロパティ](media/automation-webhooks/webhook-data-properties.png)

`WebhookData` パラメーターには、次のプロパティがあります。

| プロパティ | 説明 |
|:--- |:--- |
| WebhookName | Webhook の名前。 |
| RequestHeader | 受信 POST 要求のヘッダーを含むハッシュ テーブル。 |
| RequestBody | 受信 POST 要求の本文。 この本文には、文字列、JSON、XML、form-encoded など、あらゆるデータの書式設定が保持されます。 想定されるデータ形式を操作するには、Runbook を記述する必要があります。 |

`WebhookData` パラメーターをサポートするために必要な Webhook の構成はなく、Runbook はこれを受け入れる必要ありません。 Runbook がこのパラメーターを定義していない場合、クライアントから送信された要求の詳細はすべて無視されます。

> [!NOTE]
> Webhook を呼び出すときは、呼び出しが失敗した場合に備えて、クライアントは常にパラメーター値を格納しておく必要があります。 ネットワークの停止や接続の問題がある場合、アプリケーションは、失敗した Webhook 呼び出しを取得できません。

Webhook 作成時に `WebhookData` の値を指定すると、それは、クライアントの POST 要求のデータで Webhook が Runbook を開始したときに上書きされます。 これは、アプリケーションが要求本文にデータを含まない場合でも発生します。

Webhook 以外のメカニズムを使用して `WebhookData` を定義する Runbook を開始する場合は、Runbook が認識する `WebhookData` の値を指定できます。 この値は、`WebhookData` パラメーターと同じ[プロパティ](#webhook-properties)を持つオブジェクトである必要があります。これにより、Runbook は、Webhook によって渡される実際の `WebhookData` オブジェクトと同様にそれを扱うことができます。

たとえば、Azure portal から次の Runbook を開始するときにテスト用のサンプル Webhook データを渡す場合は、そのデータをユーザー インターフェイスで JSON として渡す必要があります。

![UI からの WebhookData パラメーター](media/automation-webhooks/WebhookData-parameter-from-UI.png)

次の Runbook の例では、`WebhookData` の以下のプロパティを定義します。

* **WebhookName**: MyWebhook
* **RequestBody**: `*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

次に、`WebhookData` パラメーターに対して UI で以下の JSON オブジェクトを渡します。 この例は、復帰文字と改行文字を使用しており、Webhook から渡される形式と一致します。

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![UI から WebhookData パラメーターを開始する](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Azure Automation は、Runbook ジョブにすべての入力パラメーターの値を記録します。 したがって、Webhook 要求でクライアントが提供した入力はすべてログに記録され、Automation ジョブにアクセスできるすべてのユーザーが使用できます。 このため、Webhook の呼び出しに機密情報を含める場合には注意する必要があります。

## <a name="webhook-security"></a>Webhook のセキュリティ

Webhook のセキュリティは、Webhook の呼び出しを許可するセキュリティ トークンを含む URL のプライバシーに依存します。 Azure Automation は、正しい URL に対して要求が行われている限り、いかなる認証も実行しません。 この理由により、クライアントは、要求を検証する代替方法を使用せずに機密性の高い操作を実行する Runbook には Webhook を使用しないようにする必要があります。

次の戦略を考えます。

* Webhook で呼び出すかどうかを判断するためのロジックを Runbook 内に含めることができます。 `WebhookData` パラメーターの `WebhookName` プロパティを Runbook に検査させます。 Runbook は、`RequestHeader` と `RequestBody` のプロパティで特定の情報を検索することにより、追加の検証を実行できます。

* Webhook 要求の受信時に外部条件の検証を Runbook に実行させます。 たとえば、GitHub リポジトリへの新しいコミットが発生するたびに GitHub によって呼び出される Runbook について考えてみましょう。 Runbook は、GitHub に接続して、新しいコミットが発生したことを確認してから続行することができます。

* Azure Automation では、Azure 仮想ネットワーク サービス タグ、特に [GuestAndHybridManagement](../virtual-network/service-tags-overview.md) がサポートされています。 サービス タグを使用して、[ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md#security-rules)または [Azure Firewall](../firewall/service-tags.md) でのネットワーク アクセス制御を定義し、仮想ネットワーク内から Webhook をトリガーできます。 セキュリティ規則を作成するときに、特定の IP アドレスの代わりにサービス タグを使用できます。 規則の適切なソースまたは宛先フィールドにサービス タグ名 **GuestAndHybridManagement** を指定することで、Automation サービスのトラフィックを許可または拒否できます。 このサービス タグでは、IP 範囲を特定のリージョンに制限することによる、より詳細な制御はサポートされていません。

## <a name="create-a-webhook"></a>webhook を作成する

Webhook には発行された Runbook が必要です。 このチュートリアルでは、「[Azure Automation Runbook を作成する](./learn/powershell-runbook-managed-identity.md)」で作成した Runbook の変更バージョンを使用します。 先に進むには、次のコードを使用して PowerShell Runbook を編集します。

```powershell
param
(
    [Parameter(Mandatory=$false)]
    [object] $WebhookData
)

if ($WebhookData.RequestBody) { 
    $names = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        foreach ($x in $names)
        {
            $name = $x.Name
            Write-Output "Hello $name"
        }
}
else {
    Write-Output "Hello World!"
}
```

次に、変更された Runbook を保存して発行します。 次の例では、Azure portal、PowerShell、および REST を使用して Webhook を作成する方法を示しています。

### <a name="from-the-portal"></a>ポータルから

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. Azure Portal で、Automation アカウントに移動します。

1. **[プロセス オートメーション]** で、 **[Runbook]** を選択して **[Runbook]** ページを開きます。

1. 一覧から自分の Runbook を選択して Runbook の **[概要]** ページを開きます。

1. **[Webhook の追加]** を選択して **[Webhook の追加]** ページを開きます。

   :::image type="content" source="media/automation-webhooks/add-webhook-icon.png" alt-text="[Webhook の追加] が強調表示されている Runbook の [概要] ページ。":::

1. **[Webhook の追加]** ページで、 **[新しい Webhook を作成します]** を選択します。

   :::image type="content" source="media/automation-webhooks/add-webhook-page-create.png" alt-text="&quot;作成&quot; が強調表示されている [Webhook の追加] ページ。":::

1. Webhook の **名前** を入力します。 **[有効期限]** フィールドでは、有効期限が既定で現在の日付から 1 年後に設定されます。

1. コピー アイコンをクリックするか、<kbd>Ctrl + C</kbd> キーを押して、Webhook の URL をコピーします。 次に、URL を安全な場所に保存します。

    :::image type="content" source="media/automation-webhooks/create-new-webhook.png" alt-text="URL が強調表示されている Webhook の作成ページ。":::

    > [!IMPORTANT]
    > Webhook を作成した後は、その URL を再取得することはできません。 上記のようにコピーして記録したことを確認してください。

1. **[OK]** を選択して **[Webhook の追加]** ページに戻ります。

1. **[Webhook の追加]** ページで、 **[パラメーターと実行設定を構成する]** を選択して **[パラメーター]** ページを開きます。

   :::image type="content" source="media/automation-webhooks/add-webhook-page-parameters.png" alt-text="&quot;パラメーター&quot; が強調表示されている [Webhook の追加] ページ。":::

1. **[パラメーター]** ページを確認します。 この記事で使用されているサンプル Runbook の場合、変更は必要ありません。 **[OK]** を選択して **[Webhook の追加]** ページに戻ります。

1. **[Webhook の追加]** ページで、 **[作成]** を選択します。 Webhook が作成され、Runbook の **[概要]** ページに戻ります。

### <a name="using-powershell"></a>PowerShell の使用

1. PowerShell の [Az モジュール](/powershell/azure/new-azureps-module-az)の最新バージョンがインストールされていることを確認します。

1. [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) コマンドレットを使用して、Azure に対話的にサインインし、指示に従います。

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    ```

1. [New-AzAutomationWebhook](/powershell/module/az.automation/new-azautomationwebhook) コマンドレットを使用して、Automation Runbook の Webhook を作成します。 変数に適切な値を指定し、スクリプトを実行します。

    ```powershell
    # Initialize variables with your relevant values
    $resourceGroup = "resourceGroupName"
    $automationAccount = "automationAccountName"
    $runbook = "runbookName"
    $psWebhook = "webhookName"
    
    # Create webhook
    $newWebhook = New-AzAutomationWebhook `
        -ResourceGroup $resourceGroup `
        -AutomationAccountName $automationAccount `
        -Name $psWebhook `
        -RunbookName $runbook `
        -IsEnabled $True `
        -ExpiryTime "12/31/2022" `
        -Force
    
    # Store URL in variable; reveal variable
    $uri = $newWebhook.WebhookURI
    $uri
    ```

   出力は、`https://ad7f1818-7ea9-4567-b43a.webhook.wus.azure-automation.net/webhooks?token=uTi69VZ4RCa42zfKHCeHmJa2W9fd` のような URL になります

1. PowerShell コマンドレット [Get-AzAutomationWebhook](/powershell/module/az.automation/get-azautomationwebhook) を使用して Webhook を確認することもできます。

    ```powershell
    Get-AzAutomationWebhook `
        -ResourceGroup $resourceGroup `
        -AutomationAccountName $automationAccount `
        -Name $psWebhook
    ```

### <a name="using-rest"></a>REST の使用

PUT コマンドについては、「[Webhook - 作成または更新](/rest/api/automation/webhook/create-or-update)」に記載されています。 この例では、PowerShell コマンドレット [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) を使用して PUT 要求を送信します。

1. `webhook.json` という名前のファイルを作成し、次のコードを貼り付けます。

    ```json
    {
      "name": "RestWebhook",
      "properties": {
        "isEnabled": true,
        "expiryTime": "2022-03-29T22:18:13.7002872Z",
        "runbook": {
          "name": "runbookName"
        }
      }
    }
    ```

   実行する前に、**runbook:name** プロパティの値を Runbook の実際の名前に変更してください。 これらのプロパティの詳細については、「[Webhook のプロパティ](#webhook-properties)」を参照してください。

1. PowerShell の [Az モジュール](/powershell/azure/new-azureps-module-az)の最新バージョンがインストールされていることを確認します。

1. [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) コマンドレットを使用して、Azure に対話的にサインインし、指示に従います。

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    ```

1. 変数に適切な値を指定し、スクリプトを実行します。

    ```powershell
    # Initialize variables
    $subscription = "subscriptionID"
    $resourceGroup = "resourceGroup"
    $automationAccount = "automationAccount"
    $runbook = "runbookName"
    $restWebhook = "webhookName"
    $file = "path\webhook.json"

    # consume file
    $body = Get-Content $file
    
    # Craft Uri
    $restURI = "https://management.azure.com/subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Automation/automationAccounts/$automationAccount/webhooks/$restWebhook`?api-version=2015-10-31"
    ```

1. 次のスクリプトを実行して、アクセス トークンを取得します。 アクセス トークンの有効期限が切れている場合は、スクリプトを再実行する必要があります。

    ```powershell
    # Obtain access token
    $azContext = Get-AzContext
    $azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
    $profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
    $token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
    $authHeader = @{
        'Content-Type'='application/json'
        'Authorization'='Bearer ' + $token.AccessToken
    }
    ```

1. 次のスクリプトを実行して、REST API を使用して Webhook を作成します。

    ```powershell
    # Invoke the REST API
    # Store URL in variable; reveal variable
    $response = Invoke-RestMethod -Uri $restURI -Method Put -Headers $authHeader -Body $body
    $webhookURI = $response.properties.uri
    $webhookURI
    ```

   出力は、`https://ad7f1818-7ea9-4567-b43a.webhook.wus.azure-automation.net/webhooks?token=uTi69VZ4RCa42zfKHCeHmJa2W9fd` のような URL になります

1. [Webhook - Get](/rest/api/automation/webhook/get) を使用して、名前を指定して Webhook を取得することもできます。 次の PowerShell コマンドを実行できます。

    ```powershell
    $response = Invoke-RestMethod -Uri $restURI -Method GET -Headers $authHeader
    $response | ConvertTo-Json
    ```

## <a name="use-a-webhook"></a>Webhook を使用する

この例では、PowerShell コマンドレット [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) を使用して、POST 要求を新しい Webhook に送信します。

1. Webhook 呼び出しの本文として Runbook に渡す値を準備します。 比較的単純な値の場合は、次のように値をスクリプト化できます。

    ```powershell
    $Names  = @(
                @{ Name="Hawaii"},
                @{ Name="Seattle"},
                @{ Name="Florida"}
            )
    
    $body = ConvertTo-Json -InputObject $Names
    ```

1. 大きなセットの場合は、ファイルを使用することをお勧めします。 `names.json` という名前のファイルを作成し、次のコードを貼り付けます。

    ```json
    [
        { "Name": "Hawaii" },
        { "Name": "Florida" },
        { "Name": "Seattle" }
    ]
    ```

    次の PowerShell コマンドを実行する前に、変数 `$file` の値を json ファイルへの実際のパスに変更してください。

    ```powershell
    # Revise file path with actual path
    $file = "path\names.json"
    $bodyFile = Get-Content -Path $file 
    ```

1. 次の PowerShell コマンドを実行して、REST API を使用して Webhook を呼び出します。

    ```powershell
    $response = Invoke-WebRequest -Method Post -Uri $webhookURI -Body $body -UseBasicParsing
    $response
    
    $responseFile = Invoke-WebRequest -Method Post -Uri $webhookURI -Body $bodyFile -UseBasicParsing
    $responseFile
    ```

   わかりやすくするために、本文を生成する 2 つの異なるメソッドに対して 2 つの呼び出しが行われています。 運用環境では 1 つのメソッドのみを使用してください。  出力は次のようになります (1 つの出力のみが表示されています)。

   :::image type="content" source="media/automation-webhooks/webhook-post-output.png" alt-text="Webhook 呼び出しからの出力。":::

    クライアントは、`POST` 要求から、次のいずれかのリターン コードを受け取ります。

    | コード | Text | 説明 |
    |:--- |:--- |:--- |
    | 202 |承認済み |要求が承認され、Runbook が正常にキューに入れられました。 |
    | 400 |正しくない要求 |次のいずれかの理由で要求が受け入れられませんでした。 <ul> <li>webhook の有効期限が切れている。</li> <li>webhook が無効になっている。</li> <li>URL 内のトークンが無効になっている。</li>  </ul> |
    | 404 |見つかりません |次のいずれかの理由で要求が受け入れられませんでした。 <ul> <li>Webhook が見つからなかった。</li> <li>Runbook が見つからなかった。</li> <li>アカウントが見つからなかった。</li>  </ul> |
    | 500 |内部サーバー エラー |URL は有効ですが、エラーが発生しました。 要求を再送信してください。 |

    要求が成功したと仮定した場合、Webhook の応答には、次に示すように JSON 形式のジョブ ID が含まれます。 ここに含まれるジョブ ID は 1 つですが、JSON 形式では将来の拡張が可能です。

    ```json
    {"JobIds":["<JobId>"]}
    ```

1. 出力を取得するには、PowerShell コマンドレット [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) を使用できます。 [Azure Automation API](/rest/api/automation/job) を使用することもできます。

    ```powershell
    #isolate job ID
    $jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
    
    # Get output
    Get-AzAutomationJobOutput `
        -AutomationAccountName $automationAccount `
        -Id $jobid `
        -ResourceGroupName $resourceGroup `
        -Stream Output
    ```

   出力は次のようになります。

   :::image type="content" source="media/automation-webhooks/webhook-job-output.png" alt-text="Webhook ジョブからの出力。":::

## <a name="update-a-webhook"></a>Webhook を更新する

Webhook が作成されると有効期間が 10 年に設定され、その期間が経過すると自動的に有効期限が切れます。 有効期限が切れた Webhook は、再アクティブ化できません。 削除してから再作成するしかありません。 有効期限に達していない Webhook については、有効期限を延長できます。 Webhook の有効期限を延長するには、次の手順を実行します。

1. Webhook が含まれている Runbook に移動します。
1. **[リソース]** で、 **[Webhook]** を選択し、有効期限を延長する Webhook を選択します。
1. **[Webhook]** ページで新しい有効期限の日付と時刻を選択し、 **[保存]** を選択します。

その他の可能な変更については、API 呼び出し [Webhook - Update](/rest/api/automation/webhook/update) および PowerShell コマンドレット [Set-AzAutomationWebhook](/powershell/module/az.automation/set-azautomationwebhook) を確認してください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Automation Runbook から Webhook を削除する例を次に示します。

- PowerShell を使用する場合、次のように [Remove-AzAutomationWebhook](/powershell/module/az.automation/remove-azautomationwebhook) コマンドレットを使用できます。 出力は返されません。

    ```powershell
    Remove-AzAutomationWebhook `
        -ResourceGroup $resourceGroup `
        -AutomationAccountName $automationAccount `
        -Name $psWebhook
    ```

- REST を使用する場合、次のように REST [Webhook - Delete](/rest/api/automation/webhook/delete) API を使用できます。

    ```powershell
    Invoke-WebRequest -Method Delete -Uri $restURI -Headers $authHeader
    ```

   出力 `StatusCode        : 200` は、削除が成功したことを意味します。

## <a name="create-runbook-and-webhook-with-arm-template"></a>ARM テンプレートを使用して Runbook と Webhook を作成する

Automation Webhook は、[Azure Resource Manager](../azure-resource-manager/templates/overview.md) テンプレートを使用して作成することもできます。 このサンプル テンプレートでは、Automation アカウント、4 つの Runbook、および名前付き Runbook の Webhook を作成します。

1. `webhook_deploy.json` という名前のファイルを作成し、次のコードを貼り付けます。

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "automationAccountName": {
                "type": "String",
                "metadata": {
                    "description": "Automation account name"
                }
            },
            "webhookName": {
                "type": "String",
                "metadata": {
                    "description": "Webhook Name"
                }
            },
            "runbookName": {
                "type": "String",
                "metadata": {
                    "description": "Runbook Name for which webhook will be created"
                }
            },
            "WebhookExpiryTime": {
                "type": "String",
                "metadata": {
                    "description": "Webhook Expiry time"
                }
            },
            "_artifactsLocation": {
                "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/",
                "type": "String",
                "metadata": {
                    "description": "URI to artifacts location"
                }
            }
        },
        "resources": [
            {
                "type": "Microsoft.Automation/automationAccounts",
                "apiVersion": "2020-01-13-preview",
                "name": "[parameters('automationAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "sku": {
                        "name": "Free"
                    }
                },
                "resources": [
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('runbookName')]",
                        "location": "[resourceGroup().location]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "Sample Runbook",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), 'scripts/AzureAutomationTutorialPython2.py')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "webhooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('webhookName')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]",
                            "[parameters('runbookName')]"
                        ],
                        "properties": {
                            "isEnabled": true,
                            "expiryTime": "[parameters('WebhookExpiryTime')]",
                            "runbook": {
                                "name": "[parameters('runbookName')]"
                            }
                        }
                    }
                ]
            }
        ],
        "outputs": {
            "webhookUri": {
                "type": "String",
                "value": "[reference(parameters('webhookName')).uri]"
            }
        }
    }
    ```

1. 次の PowerShell コード サンプルでは、お使いのマシンからテンプレートをデプロイします。 変数に適切な値を指定し、スクリプトを実行します。

    ```powershell
    $resourceGroup = "resourceGroup"
    $templateFile = "path\webhook_deploy.json"
    $armAutomationAccount = "automationAccount"
    $armRunbook = "ARMrunbookName"
    $armWebhook = "webhookName"
    $webhookExpiryTime = "12-31-2022"
    
    New-AzResourceGroupDeployment `
        -Name "testDeployment" `
        -ResourceGroupName $resourceGroup `
        -TemplateFile $templateFile `
        -automationAccountName $armAutomationAccount `
        -runbookName $armRunbook `
        -webhookName $armWebhook `
        -WebhookExpiryTime $webhookExpiryTime
    ```

   > [!NOTE]
   > セキュリティ上の理由から、URI はテンプレートが初めてデプロイされたときにのみ返されます。

## <a name="next-steps"></a>次のステップ

* アラートから Runbook をトリガーするには、「[Azure Automation Runbook をトリガーするアラートを使用する](automation-create-alert-triggered-runbook.md)」を参照してください。