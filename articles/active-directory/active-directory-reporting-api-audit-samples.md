---
title: Azure Active Directory レポートの監査 API の例 | Microsoft Docs
description: Azure Active Directory Reporting API の概要について説明します。
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: de8b8ec3-49b3-4aa8-93fb-e38f52c99743
ms.service: active-directory
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/30/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 240095b64f4b7b37a71f84ce8d7a3521e0ab7c03
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2018
ms.locfileid: "36223887"
---
# <a name="azure-active-directory-reporting-audit-api-samples"></a>Azure Active Directory レポートの監査 API の例
この記事は Azure Active Directory のレポート API に関する記事のコレクションの一部です。  
Azure AD レポートは、コードまたは関連ツールを使用して監査データにアクセスできるようにする API を提供します。
この記事では、**監査 API** のサンプル コードを提供します。

参照:

* [監査ログ](active-directory-reporting-azure-portal.md#activity-reports) に関する記事
* [Azure Active Directory Reporting API の概要](active-directory-reporting-api-getting-started.md) 」。

質問、問題点、またはフィードバックについては、 [AAD レポート ヘルプ](mailto:aadreportinghelp@microsoft.com)にお問い合わせください。


## <a name="prerequisites"></a>前提条件
この記事に掲載されているサンプルを使用するには、事前に、[Azure AD レポート API にアクセスするための前提条件](active-directory-reporting-api-prerequisites.md)を完了する必要があります。  

## <a name="known-issue"></a>既知の問題
EU リージョン内にテナントがある場合、アプリの認証は行われません。 この問題が解決されるまでは、回避策として、ユーザー認証を使用して監査 API にアクセスしてください。 

## <a name="powershell-script"></a>PowerShell スクリプト


```powershell

# This script will require the Web Application and permissions setup in Azure Active Directory
$clientID       = "<appid>"             # ApplicationId
$clientSecret   = "<key>"         # Should be a ~44 character string insert your info here
$loginURL       = "https://login.windows.net/"
$tenantdomain   = "<domain>"            # For example, contoso.onmicrosoft.com
$msgraphEndpoint = "https://graph.microsoft.com"
$countOfAuditDocsToBeSavedInAFile = 2000
    
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$msgraphEndpoint;client_id=$clientID;client_secret=$clientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
    
if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    
    $url = "$msgraphEndpoint/beta/auditLogs/directoryAudits"
    Write-Output "Fetching data using Uri: $url"
    $i=0
    $docCount=0
    Do{
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        $jsonReport = ($myReport.Content | ConvertFrom-Json).value
        $fetchedRecordCount = $jsonReport.Count
        $docCount = $docCount + $fetchedRecordCount
        $totalFetchedRecordCount = $totalFetchedRecordCount + $fetchedRecordCount
        Write-Output "Fetched $fetchedRecordCount records and saved into Audits$i.json"
        if($docCount -le $countOfAuditDocsToBeSavedInAFile)
        {
            $myReport.Content | Out-File -FilePath Audits$i.json -append  -Force        
        }
        else
        {           
            $docCount=0
            $i = $i+1
        }
            
        #Get url from next link
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'         
    }while($url -ne $null)
    Write-Output "Total Fetched record count is : $totalFetchedRecordCount"
                
} else {
    Write-Host "ERROR: No Access Token"
}

```

### <a name="executing-the-powershell-script"></a>PowerShell スクリプトの実行
スクリプトの編集が完了したら、実行して、監査ログ レポートから予期したデータが返されることを確認します。

スクリプトからは、監査レポートの出力が JSON 形式で返されます。 また、同じ出力内容を使って `Audits.json` ファイルも作成されます。 他のレポートからデータを返すようにスクリプトを変更してテストしたり、必要のない出力形式をコメント化したりできます。





## <a name="next-steps"></a>次の手順
* この記事に掲載されているサンプルをカスタマイズしますか。 [Azure Active Directory 監査 API リファレンス](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)を確認してください。 
* Azure Active Directory Reporting API の使用に関する網羅的な概要を参照するには、 [Azure Active Directory Reporting API の概要](active-directory-reporting-api-getting-started.md)を参照してください。
* Azure Active Directory のレポート作成に関する詳細については、「 [Azure Active Directory レポート ガイド](active-directory-reporting-guide.md)」を参照してください。  

