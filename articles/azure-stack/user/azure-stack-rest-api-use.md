---
title: Azure Stack API を使用する | Microsoft Docs
description: Azure から認証を取得して、Azure Stack に対して API 要求を行う方法を説明します。
services: azure-stack
documentationcenter: ''
author: cblackuk
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/02/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 3b89564bf17a9884640b51faa1c3966dce93f89a
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2018
ms.locfileid: "37346792"
---
<!--  cblackuk and charliejllewellyn. This is a community contribution by cblackuk-->

# <a name="use-the-azure-stack-api"></a>Azure Stack API を使用する

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

アプリケーション プログラミング インターフェイス (API) を使用して、Azure Stack クラウドへの VM の追加などの操作を自動化できます。

この API では、Microsoft Azure ログイン エンドポイントに対するクライアントの認証が必要です。 エンドポイントは、Azure Stack API に送信されるすべての要求のヘッダーで使用されるトークンを返します  Microsoft Azure では Oauth 2.0 を使用します。

この記事では、**cURL** ユーティリティを使用して Azure Stack 要求を作成する例を示します。 アプリケーション cURL は、データの転送にライブラリを使用するコマンドライン ツールです。 これらの例で説明するのは、Azure Stack API にアクセスするためのトークンを取得するプロセスです。 ほとんどのプログラミング言語に Oauth 2.0 ライブラリが用意されています。このライブラリでは、トークンの更新など、堅牢なトークン管理および処理タスクを行うことができます。

Azure Stack REST API を **cURL** などの汎用 REST クライアントと共に使用するプロセス全体をレビューすることは、基になる要求を把握するのに役立ち、応答ペイロードで受信する内容を確認できます。

この記事では、対話型ログインなどのトークンを取得したり、専用アプリ ID を作成したりするときに使用できる、すべてのオプションを取り上げているわけではありません。 これらのトピックの情報を取得するには、[Azure REST API リファレンス](https://docs.microsoft.com/rest/api/)のページを参照してください。

## <a name="get-a-token-from-azure"></a>Azure からトークンを取得する

コンテンツの種類 x-www-form-urlencoded を使用して書式設定された要求 "本文" を作成して、アクセス トークンを取得します。 自分の要求を Azure REST 認証とログイン エンドポイントに POST します。

### <a name="uri"></a>URI

```bash  
POST https://login.microsoftonline.com/{tenant id}/oauth2/token
```

**テナント ID** は次のいずれかです。

 - テナントのドメイン (`fabrikam.onmicrosoft.com` など)
 - テナントの ID (`8eaed023-2b34-4da1-9baa-8bc8c9d6a491` など)
 - テナント独立のキーの既定値: `common`

### <a name="post-body"></a>Post 本文

```bash  
grant_type=password
&client_id=1950a258-227b-4e31-a9cf-717495945fc2
&resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
&username=admin@fabrikam.onmicrosoft.com
&password=Password123
&scope=openid
```

各値:

 - **grant_type**  
    使用する認証スキームの種類。 この例では、値は `password` です

 - **resource**  
    トークンがアクセスするリソース。 リソースを見つけるには、Azure Stack 管理メタデータ エンドポイントにクエリを実行します。 **audiences** セクションを確認します

 - **Azure Stack 管理エンドポイント**  
    ```
    https://management.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-01
    ```

  > [!NOTE]  
  > 管理者がテナント API へのアクセスを試みる場合は、必ずテナント エンドポイント (`https://adminmanagement.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-011` など) を使用する必要があります。  

  たとえば、エンドポイントとして Azure Stack Development Kit を使用するとします。

    ```bash
    curl 'https://management.local.azurestack.external/metadata/endpoints?api-version=2015-01-01'
    ```

  応答:

  ```
  {
  "galleryEndpoint":"https://adminportal.local.azurestack.external:30015/",
  "graphEndpoint":"https://graph.windows.net/",
  "portalEndpoint":"https://adminportal.local.azurestack.external/",
  "authentication":{
      "loginEndpoint":"https://login.windows.net/",
      "audiences":["https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"]
      }
  }
  ```

### <a name="example"></a>例

  ```
  https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
  ```

  **client_id**

  この値は、既定値にハードコードされています。

  ```
  1950a258-227b-4e31-a9cf-717495945fc2
  ```

  特定のシナリオについては代替オプションを使用できます。

  
  | アプリケーション | ApplicationID |
  | --------------------------------------- |:-------------------------------------------------------------:|
  | LegacyPowerShell | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417 |
  | PowerShell | 1950a258-227b-4e31-a9cf-717495945fc2 |
  | WindowsAzureActiveDirectory | 00000002-0000-0000-c000-000000000000 |
  | VisualStudio | 872cd9fa-d31f-45e0-9eab-6e460a02d1f1 |
  | AzureCLI | 04b07795-8ddb-461a-bbee-02f9e1bf7b46 |

  **username**

  Azure Stack AAD アカウント。以下に例を示します。

  ```
  azurestackadmin@fabrikam.onmicrosoft.com
  ```

  **password**

  Azure Stack AAD 管理パスワード。

### <a name="example"></a>例

要求:

```
curl -X "POST" "https://login.windows.net/fabrikam.onmicrosoft.com/oauth2/token" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=1950a258-227b-4e31-a9cf-717495945fc2" \
--data-urlencode "grant_type=password" \
--data-urlencode "username=admin@fabrikam.onmicrosoft.com" \
--data-urlencode 'password=Password12345' \
--data-urlencode "resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"
```

応答:

```
{
  "token_type": "Bearer",
  "scope": "user_impersonation",
  "expires_in": "3599",
  "ext_expires_in": "0",
  "expires_on": "1512574780",
  "not_before": "1512570880",
  "resource": "https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155",
  "access_token": "eyJ0eXAiOi...truncated for readability..."
}
```

## <a name="api-queries"></a>API クエリ

アクセス トークンを取得したら、そのトークンは、自分の各 API 要求にヘッダーとして追加する必要があります。 そのためには、値 `Bearer <access token>` を含むヘッダー **authorization** を作成する必要があります。 例: 

要求:

```bash  
curl -H "Authorization: Bearer eyJ0eXAiOi...truncated for readability..." 'https://adminmanagement.local.azurestack.external/subscriptions?api-version=2016-05-01'
```

応答:

```bash  
offerId : /delegatedProviders/default/offers/92F30E5D-F163-4C58-8F02-F31CFE66C21B
id : /subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8
subscriptionId : 800c4168-3eb1-406b-a4ca-919fe7ee42e8
tenantId : 9fea4606-7c07-4518-9f3f-8de9c52ab628
displayName : Default Provider Subscription
state : Enabled
subscriptionPolicies : @{locationPlacementId=AzureStack}
```

### <a name="url-structure-and-query-syntax"></a>URL の構造とクエリ構文

汎用要求 URI の構成: {URI-scheme} :// {URI-host} / {resource-path} ? {query-string}

- **URI スキーム**:  
URI は、要求を送信するときに使用されるプロトコルです。 たとえば、`http` または `https` です。
- **URI ホスト**:  
ホストには、REST サービス エンドポイントがホストされているサーバーのドメイン名または IP アドレスを指定します (`graph.microsoft.com`、`adminmanagement.local.azurestack.external` など)。
- **リソース パス**:  
パスには、リソースまたはリソース コレクションを指定します。これらのリソースの選択を決定するときにサービスによって使用される複数のセグメントが含まれることがあります。 たとえば、`beta/applications/00003f25-7e1f-4278-9488-efc7bac53c4a/owners` を使用すると、アプリケーション コレクション内にある特定のアプリケーションの所有者一覧に対してクエリを実行できます。
- **クエリ文字列**:  
文字列には、API バージョン、リソースの選択条件など、シンプルな追加パラメーターを指定します。

## <a name="azure-stack-request-uri-construct"></a>Azure Stack 要求 URI コンストラクト

```
{URI-scheme} :// {URI-host} / {subscription id} / {resource group} / {provider} / {resource-path} ? {OPTIONAL: filter-expression} {MANDATORY: api-version}
```

### <a name="uri-syntax"></a>URI 構文

```
https://adminmanagement.local.azurestack.external/{subscription id}/resourcegroups/{resource group}/providers/{provider}/{resource-path}?{api-version}
```

### <a name="query-uri-example"></a>クエリ URI の例

```
https://adminmanagement.local.azurestack.external/subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8/resourcegroups/system.local/providers/microsoft.infrastructureinsights.admin/regionhealths/local/Alerts?$filter=(Properties/State eq 'Active') and (Properties/Severity eq 'Critical')&$orderby=Properties/CreatedTimestamp desc&api-version=2016-05-01"
```

## <a name="next-steps"></a>次の手順

Azure RESTful エンドポイントの使用の詳細については、「[Azure REST API Reference (Azure REST API リファレンス)](https://docs.microsoft.com/rest/api/)」を参照してください。
