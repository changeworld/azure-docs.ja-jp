---
title: 'クイック スタート: REST API を使用してラボを作成する'
description: このクイック スタートでは、Azure REST API を使用して Azure DevTest Labs でラボを作成します。
ms.topic: quickstart
ms.date: 10/27/2021
ms.openlocfilehash: 83a1509f36f53d51f63f7dbc39ea2d1f6794dc54
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478328"
---
# <a name="quickstart-create-a-lab-in-azure-devtest-labs-using-azure-rest-api"></a>クイック スタート: Azure REST API を使用して Azure DevTest Labs でラボを作成する

Azure REST API を使用して Azure DevTest Labs での作業を開始します。 Azure DevTest Labs には Azure 仮想マシン (VM) やネットワークなどのリソースのグループが含まれます。 このインフラストラクチャを使用すると、制限とクォータを指定することで、これらのリソースをより上手く管理できます。  Azure REST API を使用すると、Azure プラットフォームでホストされているサービスに対する操作を管理できます。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- インストール済みの PowerShell [Az モジュール](/powershell/azure/new-azureps-module-az)。 最新バージョンを使用していることを確認します。 必要であれば、`Update-Module -Name Az` を実行します。

## <a name="prepare-request-body"></a>要求本文を準備する

REST 呼び出しで使用される[要求本文](/rest/api/dtl/labs/create-or-update.md#request-body)を準備します。

以下の JSON 構文をコピーして、`body.json` という名前のファイルに貼り付けます。 ローカル コンピューターまたは Azure ストレージ アカウントにファイルを保存します。

```json
{
  "properties": {
    "labStorageType": "Standard"
  },
  "location": "westus2",
  "tags": {
    "Env": "alpha"
  }
}
```

## <a name="sign-in-to-your-azure-subscription"></a>Azure サブスクリプションにサインインします。

1. 変数に適切な値を指定し、スクリプトを実行します。

    ```powershell
    $subscription = "subscriptionID"
    $resourceGroup = "resourceGroupName"
    $labName = "labName"
    $file = "path\body.json"
    ```

1. ワークステーションから、PowerShell [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) コマンドレットを使用して Azure サブスクリプションにログインし、画面上の指示に従います。

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Set-AzContext -SubscriptionId $subscription
    ```

## <a name="build-request-body-for-submission"></a>送信用の要求本文を作成する

PUT 要求の構文は次のとおりです。<br>
 `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{name}?api-version=2018-09-15`.

 次の PowerShell スクリプトを実行して、要求値をパラメーターに渡します。 要求本文の内容もパラメーターに渡されます。

```powershell
# build URI
$URI = "https://management.azure.com/subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.DevTestLab/labs/$labName`?api-version=2018-09-15"

# build body
$body = Get-Content $file
```

## <a name="obtain-an-authentication-token"></a>認証トークンを取得する

次のコマンドを使用して、認証トークンを取得します。

```powershell
$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}
```

## <a name="invoke-the-rest-api"></a>REST API の呼び出し

次のコマンドを使用して、REST APIを呼び出し、応答を確認します。

```powershell
# Invoke the REST API
$response = Invoke-RestMethod -Uri $URI -Method PUT -Headers $authHeader -Body $body

# Review output
$response | ConvertTo-Json
```

応答は、次のテキストのようになります。

```output
{
    "properties":  {
                       "labStorageType":  "Standard",
                       "mandatoryArtifactsResourceIdsLinux":  [

                                                              ],
                       "mandatoryArtifactsResourceIdsWindows":  [

                                                                ],
                       "createdDate":  "2021-10-27T20:22:49.7495913+00:00",
                       "premiumDataDisks":  "Disabled",
                       "environmentPermission":  "Reader",
                       "announcement":  {
                                            "title":  "",
                                            "markdown":  "",
                                            "enabled":  "Disabled",
                                            "expired":  false
                                        },
                       "support":  {
                                       "enabled":  "Disabled",
                                       "markdown":  ""
                                   },
                       "provisioningState":  "Creating",
                       "uniqueIdentifier":  "uniqueID"
                   },
    "id":  "/subscriptions/ContosoID/resourcegroups/groupcontoso/providers/microsoft.devtestlab/labs/myotherlab",

    "name":  "myOtherLab",
    "type":  "Microsoft.DevTestLab/labs",
    "location":  "westus2",
    "tags":  {
                 "Env":  "alpha"
             }
}
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このラボを引き続き使用しない場合は、次の手順を使用して削除します。

1. 変数に適切な値を指定し、スクリプトを実行します。

    ```powershell
    $subscription = "subscriptionID"
    $resourceGroup = "resourceGroupName"
    $labName = "labName"
    ```

1. 次のスクリプトを実行して、名前付きラボを Azure DevTest Labs から削除します。

    ```powershell
    # build URI
    $URI = "https://management.azure.com/subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.DevTestLab/labs/$labName`?api-version=2018-09-15"
    
    # obtain access token
    $azContext = Get-AzContext
    $azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
    $profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
    $token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
    $authHeader = @{
        'Content-Type'='application/json'
        'Authorization'='Bearer ' + $token.AccessToken
    }
    
    # Invoke the REST API
    Invoke-RestMethod -Uri $URI -Method DELETE -Headers $authHeader
    ```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Azure REST API を使用してラボを作成しました。 ラボにアクセスする方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [チュートリアル: ラボにアクセスする](../tutorial-use-custom-lab.md)
