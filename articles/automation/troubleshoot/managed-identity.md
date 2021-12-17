---
title: Azure Automation マネージド ID の問題のトラブルシューティング
description: この記事では、Automation アカウントでマネージド ID を使用した場合の問題のトラブルシューティングと解決方法について説明します。
services: automation
ms.subservice: ''
ms.date: 10/26/2021
ms.topic: troubleshooting
ms.openlocfilehash: f26c10468322992a5b3fbc302739ff827e841bba
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131443663"
---
# <a name="troubleshoot-azure-automation-managed-identity-issues"></a>Azure Automation マネージド ID の問題のトラブルシューティング

この記事では、Automation アカウントでマネージド ID を使用する際に発生する可能性がある問題の解決方法について説明します。 Automation アカウントでのマネージド ID の使用に関する一般的な情報については、[Azure Automation アカウントの認証の概要](../automation-security-overview.md#managed-identities)に関する記事をご覧ください。

## <a name="scenario-fail-to-get-msi-token-for-account"></a>シナリオ: アカウントの MSI トークンを取得できません

### <a name="issue"></a>問題

Automation アカウントでユーザー割り当てマネージド ID を操作すると、`Failed to get MSI token for account a123456b-1234-12a3-123a-aa123456aa0b` のようなエラーが表示されます。

### <a name="cause"></a>原因

Automation アカウントのシステム割り当てマネージド ID を有効にする前に、ユーザー割り当てマネージド ID を使用する。

### <a name="resolution"></a>解決方法

自分の Automation アカウント用のシステム割り当てマネージド ID を有効にします。 その後、ユーザー割り当てマネージド ID を使用します。  

## <a name="scenario-attempt-to-use-managed-identity-with-automation-account-fails"></a>シナリオ: Automation アカウントでマネージド ID を使用しようとすると失敗する

### <a name="issue"></a>問題

Automation アカウントでマネージド ID を使用しようとすると、次のようなエラーが発生します。

```error
Connect-AzureRMAccount : An error occurred while sending the request. At line:2 char:1 + Connect-AzureRMAccount -Identity + 
CategoryInfo : CloseError: (:) [Connect-AzureRmAccount], HttpRequestException + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

### <a name="cause"></a>原因

この問題の最も一般的な原因は、ID を使用する前に有効にしていないことです。 これを確認するには、影響を受ける Automation アカウントで次の PowerShell Runbook を実行します。

```powershell
resource= "?resource=https://management.azure.com/"
$url = $env:IDENTITY_ENDPOINT + $resource
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER)
$Headers.Add("Metadata", "True")

try
{
    $Response = Invoke-RestMethod -Uri $url -Method 'GET' -Headers $Headers
}
catch
{
    $StatusCode = $_.Exception.Response.StatusCode.value__
    $stream = $_.Exception.Response.GetResponseStream()
    $reader = New-Object System.IO.StreamReader($stream)
    $responseBody = $reader.ReadToEnd()
    
    Write-Output "Request Failed with Status: $StatusCode, Message: $responseBody"
}
```

問題が ID を使用する前に有効にしなかったことが原因である場合は、次のような結果が表示されます。

`Request Failed with Status: 400, Message: {"Message":"No managed identity was found for Automation account xxxxxxxxxxxx"}`

### <a name="resolution"></a>解決方法

マネージド ID サービスを使用するには、事前に Automation アカウントの ID を有効にする必要があります。 「[Azure Automation アカウントのマネージド ID を有効にする](../enable-managed-identity-for-automation.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

この記事で問題を解決できない場合は、追加のサポートを得るために、次のいずれかのチャネルをお試しください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる。 これは、Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐための、Microsoft Azure の公式アカウントです。
* Azure サポート インシデントを送信する。 [Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、 **[サポートを受ける]** を選択します。