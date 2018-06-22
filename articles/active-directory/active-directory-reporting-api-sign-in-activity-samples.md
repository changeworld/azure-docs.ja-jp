---
title: Azure Active Directory サインイン アクティビティ レポート API のサンプル | Microsoft Docs
description: Azure Active Directory Reporting API の概要について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: c41c1489-726b-4d3f-81d6-83beb932df9c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/31/2018
ms.author: dhanyahk;rolyon
ms.reviewer: dhanyahk
ms.openlocfilehash: 466755d7d1cc7fbf4006826ac849b74ba306bae9
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2018
ms.locfileid: "34698579"
---
# <a name="azure-active-directory-sign-in-activity-report-api-samples"></a>Azure Active Directory サインイン アクティビティ レポート API のサンプル
この記事は Azure Active Directory のレポート API に関する記事のコレクションの一部です。  
Azure AD レポートは、コードまたは関連ツールを使用してサインイン アクティビティ データにアクセスできるようにする API を提供します。  
この記事では、**サインイン アクティビティ API**のサンプル コードを提供します。

参照:

* [監査ログ](active-directory-reporting-azure-portal.md#activity-reports)に関する記事
* [Azure Active Directory Reporting API の概要](active-directory-reporting-api-getting-started.md) 」。


## <a name="prerequisites"></a>前提条件
この記事に掲載されているサンプルを使用するには、事前に、[Azure AD レポート API にアクセスするための前提条件](active-directory-reporting-api-prerequisites.md)を完了する必要があります。  

## <a name="powershell-script"></a>PowerShell スクリプト

```powershell

# This script will require the Web Application and permissions setup in Azure Active Directory
$clientID       = "<appid>"             # ApplicationId
$clientSecret   = "<key>"         # Should be a ~44 character string insert your info here
$loginURL       = "https://login.windows.net/"
$tenantdomain   = "<domain>"            # For example, contoso.onmicrosoft.com
$msgraphEndpoint = "https://graph.microsoft.com"
$countOfSignInDocsToBeSavedInAFile = 2000
    
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$msgraphEndpoint;client_id=$clientID;client_secret=$clientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
    
if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    
    $url = "$msgraphEndpoint/beta/auditLogs/signIns"
    Write-Output "Fetching data using Uri: $url"
    $i=0
    $docCount=0
    Do{
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        $jsonReport = ($myReport.Content | ConvertFrom-Json).value
        $fetchedRecordCount = $jsonReport.Count
        $docCount = $docCount + $fetchedRecordCount
        $totalFetchedRecordCount = $totalFetchedRecordCount + $fetchedRecordCount
        Write-Output "Fetched $fetchedRecordCount records and saved into SignIns$i.json"
        if($docCount -le $countOfSignInDocsToBeSavedInAFile)
        {
            $myReport.Content | Out-File -FilePath SignIns$i.json -append  -Force       
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




## <a name="executing-the-script"></a>スクリプトの実行
スクリプトの編集が完了したら、実行して、サインイン ログ レポートから予期したデータが返されることを確認します。

スクリプトからは、サインイン レポートの出力が JSON 形式で返されます。 また、同じ出力内容を使って `SignIns.json` ファイルも作成されます。 他のレポートからデータを返すようにスクリプトを変更してテストしたり、必要のない出力形式をコメント化したりできます。

## <a name="next-steps"></a>次の手順
* この記事に掲載されているサンプルをカスタマイズしますか。 [Azure Active Directory サインイン アクティビティ レポート API リファレンス](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)を確認してください。 
* Azure Active Directory Reporting API の使用に関する網羅的な概要を参照するには、 [Azure Active Directory Reporting API の概要](active-directory-reporting-api-getting-started.md)を参照してください。
* Azure Active Directory のレポート作成に関する詳細については、「 [Azure Active Directory レポート ガイド](active-directory-reporting-guide.md)」を参照してください。  

