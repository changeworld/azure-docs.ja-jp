---
title: Azure Active Directory Identity Protection 用の Microsoft Graph | Microsoft Docs
description: Microsoft Graph に対するクエリを通じて、Azure Active Directory から一連のリスク イベントとその関連情報を取得する方法を説明します。
services: active-directory
keywords: azure active directory identity protection, リスク イベント, 脆弱性, セキュリティ ポリシー, Microsoft Graph
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: fa109ba7-a914-437b-821d-2bd98e681386
ms.service: active-directory
ms.component: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: markvi
ms.reviewer: nigu
ms.custom: seohack1
ms.openlocfilehash: 3bdf44e0a1cf0ccda6d015fa3683964f3530d4af
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "40003485"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Azure Active Directory Identity Protection と Microsoft Graph の基本
Microsoft Graph は、Microsoft の統合 API エンドポイントであり、[Azure Active Directory Identity Protection](../active-directory-identityprotection.md) API のホームです。 最初の API である **identityRiskEvents** を使用して、Microsoft Graph に対して一連の[リスク イベント](../reports-monitoring/concept-risk-events.md)とその関連情報のクエリを実行できます。 この記事では、この API クエリの基礎について説明します。 さらに踏み込んだ概要や詳しい解説、Graph Explorer の利用については、[Microsoft Graph のサイト](https://graph.microsoft.io/)を参照してください。


Microsoft Graph を介して Identity Protection のデータにアクセスするには、次の 4 つのステップがあります。

1. ドメイン名を取得します。
2. 新しいアプリ登録を作成します。 
2. このシークレットとその他いくつかの情報を使って Microsoft Graph に本人性を証明し、認証トークンを取得します。 
3. このトークンを使って API エンドポイントに要求を行い、Identity Protection データを取得します。

以降の手順を開始する前に次の情報が必要となります。

* Azure AD にアプリケーションを作成するための管理者特権
* テナントのドメインの名前 (例: contoso.onmicrosoft.com)


## <a name="retrieve-your-domain-name"></a>ドメイン名の取得 

1. Azure Portal に管理者として[サインイン](https://portal.azure.com)します。 

2. 左のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Creating an application](./media/graph-get-started/41.png)


3. **[管理]** セクションで、**[プロパティ]** をクリックします。

    ![Creating an application](./media/graph-get-started/42.png)

4. ドメイン名をコピーします。


## <a name="create-a-new-app-registration"></a>新しいアプリ登録の作成

1. **[Active Directory]** ページの **[管理]** セクションで、**[アプリの登録]** をクリックします。

    ![Creating an application](./media/graph-get-started/42.png)


2. 上部のメニューで、**[新しいアプリケーションの登録]** をクリックします。
   
    ![Creating an application](./media/graph-get-started/43.png)

3. **[作成]** ページで、以下の手順を実行します。
   
    ![Creating an application](./media/graph-get-started/44.png)

    a. **[名前]** ボックスにアプリケーションの名前 (例: AADIP Risk Event API Application) を入力します。
   
    b. **[種類]** として **[Web アプリケーションや Web API]** を選択します。
   
    c. **[サインオン URL]** ボックスに、「`http://localhost`」と入力します。

    d. **Create** をクリックしてください。

4. **[設定]** ページを開くには、アプリケーションの一覧で、新しく作成したアプリの登録をクリックします。 

5. **[アプリケーション ID]** をコピーします。


## <a name="grant-your-application-permission-to-use-the-api"></a>API を使用するためのアクセス許可をアプリケーションに付与します。

1. **[設定]** ページで、**[必要なアクセス許可]** をクリックします。
   
    ![Creating an application](./media/graph-get-started/15.png)

2. **[必要なアクセス許可]** ページで、上部ツール バーの **[追加]** をクリックします。
   
    ![Creating an application](./media/graph-get-started/16.png)
   
3. **[API アクセスの追加]** ページで、**[API を選択します]** をクリックします。
   
    ![Creating an application](./media/graph-get-started/17.png)

4. **[API を選択します]** ページで、**[Microsoft Graph]** を選んで、**[選択]** をクリックします。
   
    ![Creating an application](./media/graph-get-started/18.png)

5. **[API アクセスの追加]** ページで、**[アクセス許可を選択します]** をクリックします。
   
    ![Creating an application](./media/graph-get-started/19.png)

6. **[アクセスの有効化]** ページで、**[すべての ID リスク イベント情報の読み取り]** をクリックし、**[選択]** をクリックします。
   
    ![Creating an application](./media/graph-get-started/20.png)

7. **[API アクセスの追加]** ページで、**[完了]** をクリックします。
   
    ![Creating an application](./media/graph-get-started/21.png)

8. **[必要なアクセス許可]** ページで **[アクセス許可の付与]** をクリックし、**[はい]** をクリックします。
   
    ![Creating an application](./media/graph-get-started/22.png)



## <a name="get-an-access-key"></a>アクセス キーを取得する

1. **[設定]** ページで、**[キー]** をクリックします。
   
    ![Creating an application](./media/graph-get-started/23.png)

2. **[キー]** ページで、次の手順を実行します。
   
    ![Creating an application](./media/graph-get-started/24.png)

    a. **[キーの説明]** テキスト ボックスに、説明を入力します (例: "*AADIP リスク イベント*")。
    
    b. **[期間]** として、**[1 年]** を選びます。

    c. **[Save]** をクリックします。
   
    d. キーの値をコピーし、安全な場所に貼り付けます。   
   
   > [!NOTE]
   > このキーを紛失した場合、このセクションに戻って新しいキーを作成する必要があります。 このキーはだれにも渡さないように注意してください。このキーがあれば、だれでもデータにアクセスすることができます。
   > 
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Microsoft Graph に本人であることを証明して Identity Risk Events API を照会する
この時点で次の情報が揃っている必要があります。

- テナントのドメインの名前

- クライアント ID 

- キー 


本人確認を行うために、post 要求を `https://login.microsoft.com` に送信します。要求本文に次のパラメーターを指定してください。

- grant_type: “**client_credentials**”

-  リソース: "**https://graph.microsoft.com**"

- client_id: \<クライアント ID\>

- client_secret: \<キー\>


認証に成功すると、認証トークンが返されます。  
API を呼び出すためには、次のパラメーターを持つヘッダーを作成します。

    `Authorization`=”<token_type> <access_token>"


トークンの種類とアクセス トークンは、認証時に返されたトークンで確認できます。

このヘッダーは、API URL (`https://graph.microsoft.com/beta/identityRiskEvents`) に対する要求として送信します。

成功した場合の応答には、一連の ID リスク イベントとその関連データが OData JSON 形式で格納されます。必要に応じてこのデータを解析し、処理してください。

以下に示したコードは、PowerShell を使って認証と API 呼び出しを行う例です。  
該当するクライアント ID、秘密キー、テナントのドメインを追加してください。

    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 


## <a name="next-steps"></a>次の手順

以上、Microsoft Graph の API を呼び出す基本的な方法を紹介しました。  
これを応用すれば、思いどおりに ID リスク イベントを照会し、そのデータを活用することができます。

Microsoft Graph に関する詳細情報のほか、Graph API を使ったアプリケーションの構築方法については、こちらの[ドキュメント](https://graph.microsoft.io/docs)と [Microsoft Graph](https://graph.microsoft.io/) のサイトをご覧ください。 また、[Azure AD Identity Protection API](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root) のページでは、Graph で利用できる Identity Protection の全 API の一覧を掲載しています。ぜひお気に入りに登録してご利用ください。 API を介した Identity Protection の操作方法が新たに追加された場合、このページに反映していく予定です。

関連情報については、以下をご覧ください。

-  [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

-  [Azure Active Directory Identity Protection で検出されるリスク イベントの種類](../reports-monitoring/concept-risk-events.md)

- [Microsoft Graph](https://graph.microsoft.io/)

- [Microsoft Graph の概要](https://graph.microsoft.io/docs)

- [Azure AD Identity Protection Service Root](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)

