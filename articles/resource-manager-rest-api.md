<properties
   pageTitle="Resource Manager REST API| Microsoft Azure"
   description="Resource Manager REST API の認証の概要と使用例"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="navale;tomfitz;"/>
   
# Resource Manager REST API

> [AZURE.SELECTOR]
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [Azure CLI](xplat-cli-azure-resource-manager.md)
- [ポータル](./azure-portal/resource-group-portal.md)
- [REST API](resource-manager-rest-api.md)

Azure Resource Manager、デプロイ済みのテンプレート、構成済みのストレージ アカウントを呼び出す裏では、必ず Azure Resource Manager の RESTful API が 1 回以上呼び出されます。このトピックでは、これらの API と、SDK を一切使わずにこれらの API を呼び出す方法について説明します。これは、Azure へのすべての要求を完全に制御したい場合や、優先言語の SDK が利用できないか、実行したい操作がその SDK でサポートされていない場合に、非常に役に立つ可能性があります。

この記事では Azure で公開されているすべての API については説明しませんが、その一部を例として取り上げ、先に進んだり、掘り下げたりできるようにします。基本を理解できたら、次に進み、「[Azure Resource Manager REST API Reference (Azure Resource Manager REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn790568.aspx)」で残りの API の使い方を調べることができます。

## 認証
ARM の認証は、Azure Active Directory (AD) によって処理されます。任意の API に接続するには、最初に Azure AD で認証を行って認証トークンを受信する必要があります。この認証トークンは、すべての要求に対して渡すことができます。ここでは REST API の直接的で純粋な呼び出しについて説明しますが、読者が通常のユーザー名とパスワードを使用した認証 (ポップアップ画面でユーザー名とパスワードの入力が求められるような認証) や、2 要素認証のシナリオで利用される他の認証メカニズムによる認証を望んでいないということも想定しています。したがって、Azure AD アプリケーションと、ログインに使用するサービス プリンシパルを作成します。ただし、Azure AD は複数の認証手順をサポートしており、そのどれを使っても、後続の API 要求に必要な認証トークンを取得できることに注意してください。詳しい手順については、[Azure AD のアプリケーションとサービス プリンシパルの作成](./resource-group-create-service-principal-portal.md)に関するページを参照してください。

### アクセス トークンの生成 
Azure AD に対する認証は、login.microsoftonline.com にある Azure AD を呼び出すことで行われます。認証のためには次の情報が必要です。

* Azure AD テナント ID (ログインに使用している Azure AD の名前。会社名と同じである場合が多いものの、必ずしもそうとは限りません)
* アプリケーション ID (Azure AD アプリケーションの作成中に取得したもの)
* パスワード (Azure AD アプリケーションの作成中に選択したもの)

以下の HTTP 要求で、"Azure AD Tenant ID"、"Application ID"、"Password" を適切な値に置き換えてください。

**一般的な HTTP 要求:**

```HTTP
POST /<Azure AD Tenant ID>/oauth2/token?api-version=1.0 HTTP/1.1 HTTP/1.1
Host: login.microsoftonline.com
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.core.windows.net%2F&client_id=<Application ID>&client_secret=<Password>
```

この要求により、(認証に成功した場合は) 次のような応答が返されます。

```json
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1448199959",
  "not_before": "1448196059",
  "resource": "https://management.core.windows.net/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhb...86U3JI_0InPUk_lZqWvKiEWsayA"
}
```
(読みやすくするために、上記の応答の access\_token は短縮されています)

**Bash を使ったアクセス トークンの生成:**

```console
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=client_credentials&resource=https://management.core.windows.net&client_id=<application id>&client_secret=<password you selected for authentication>" https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0
```

**PowerShell を使ったアクセス トークンの生成:**

```powershell
Invoke-RestMethod -Uri https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0 -Method Post
 -Body @{"grant_type" = "client_credentials"; "resource" = "https://management.core.windows.net/"; "client_id" = "<application id>"; "client_secret" = "<password you selected for authentication>" }
```

応答には、アクセス トークン、そのトークンの有効期限に関する情報、そのトークンを使えるリソースに関する情報が含まれます。前の HTTP 呼び出しで受信したアクセス トークンは、ARM API に対するすべての要求で、"Bearer YOUR\_ACCESS\_TOKEN" 値を含む "Authorization" というヘッダーとして渡す必要があります。"Bearer" とアクセス トークンの間のスペースに注意してください。

上記の HTTP の結果からわかるとおり、トークンは特定の期間、有効です。その期間中は、トークンをキャッシュし、同じトークンを再利用する必要があります。API 呼び出しごとに Azure AD に対して認証を行うことができたとしても、著しく非効率です。

## ARM REST API の呼び出し

[Azure Resource Manager REST API については、こちらをご覧ください](https://msdn.microsoft.com/library/azure/dn790568.aspx)。このチュートリアルでは、Azure Resource Manager REST API の使い方を個別に取り上げたり、網羅的に説明したりしません。このドキュメントでは、一部の API のみを使って API の基本的な使い方を説明します。その後で、公式のドキュメントを紹介します。

### すべてのサブスクリプションの一覧表示

実行できる最も簡単な操作の 1 つに、アクセスできる利用可能なサブスクリプションの一覧表示があります。以下の要求には、アクセス トークンをヘッダーとして渡す方法が示されています。

(YOUR\_ACCESS\_TOKEN を実際のアクセス トークンに置き換えてください)

```HTTP
GET /subscriptions?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

この要求の結果、このサービス プリンシパルでアクセスできるサブスクリプションの一覧が返されます。

(次のサブスクリプション ID は読みやすいように短縮されています)

```json
{
  "value": [
    {
      "id": "/subscriptions/3a8555...555995",
      "subscriptionId": "3a8555...555995",
      "displayName": "Azure Subscription",
      "state": "Enabled",
      "subscriptionPolicies": {
        "locationPlacementId": "Internal_2014-09-01",
        "quotaId": "Internal_2014-09-01"
      }
    }
  ]
}
```

### 特定のサブスクリプションに含まれる全リソース グループの一覧表示

ARM API で使用できるリソースは、いずれもリソース グループの入れ子になっています。以下の HTTP GET 要求を使って、サブスクリプションに含まれる既存のリソース グループを ARM に対して照会します。ここではどのような形でサブスクリプション ID が URL の一部として渡されているか、注目してください。

(YOUR\_ACCESS\_TOKEN と SUBSCRIPTION\_ID を実際のアクセス トークンとサブスクリプション ID に置き換えてください)

```HTTP
GET /subscriptions/SUBSCRIPTION_ID/resourcegroups?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

返される応答は、定義済みのリソース グループの有無によって変わります。定義済みのリソース グループがある場合は、その数が返されます。

(次のサブスクリプション ID は読みやすいように短縮されています)

```json
{
    "value": [
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/myfirstresourcegroup",
            "name": "myfirstresourcegroup",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/mysecondresourcegroup",
            "name": "mysecondresourcegroup",
            "location": "northeurope",
            "tags": {
                "tagname1": "My first tag"
            },
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```

### リソース グループの作成

ここまでは、ARM API に対して情報を照会するのみでしたが、ここからは、いくつかリソースを作成してみます。その中でも最もシンプルな、リソース グループから始めましょう。次の HTTP 要求は新しいリソース グループを指定したリージョン/場所に作成し、それに 1 つ以上のタグを追加します (以下のサンプルでは、タグを 1 つだけ追加しています)。

(YOUR\_ACCESS\_TOKEN、SUBSCRIPTION\_ID、RESOURCE\_GROUP\_NAME を実際のアクセス トークン、サブスクリプション ID、作成するリソース グループの名前に置き換えてください)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  }
}
```

要求に成功した場合は、次のような応答が返されます。

```json
{
  "id": "/subscriptions/3a8555...555995/resourceGroups/RESOURCE_GROUP_NAME",
  "name": "RESOURCE_GROUP_NAME",
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  },
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

これでリソース グループを Azure で正常に作成できました。ご利用ありがとうございます。

### ARM テンプレートを使ってリソース グループにリソースをデプロイする

ARM では、ARM テンプレートを使ってリソースをデプロイできます。ARM テンプレートには、いくつかのリソースとその依存関係が定義されます。このセクションは ARM テンプレートに精通している読者を想定しているため、API 呼び出しによりそのデプロイを始める方法のみを紹介します。ARM テンプレートに関する詳細なドキュメントはこちらにあります。

ARM テンプレートのデプロイは、他の API を呼び出す方法とそれほど違いはありません。重要な点は、テンプレートのデプロイには、その内容に応じてかなりの時間がかかる可能性があるという点です。API の呼び出しは単に戻るだけなので、開発者がデプロイが完了したタイミングを把握するためには、デプロイの状態を照会する必要があります。

この例では、[GitHub](https://github.com/Azure/azure-quickstart-templates) で一般公開されている ARM テンプレートを使います。このテンプレートを使うと、Linux VM が米国西部リージョンにデプロイされます。このテンプレートが GitHun などのパブリック リポジトリで公開されても、要求の一部で完全なテンプレートを渡すこともできます。使用するテンプレート内で使用される要求の一部として、パラメーター値を指定します。

(SUBSCRIPTION\_ID、RESOURCE\_GROUP\_NAME、DEPLOYMENT\_NAME、YOUR\_ACCESS\_TOKEN、GLOBALY\_UNIQUE\_STORAGE\_ACCOUNT\_NAME、ADMIN\_USER\_NAME、ADMIN\_PASSWORD、DNS\_NAME\_FOR\_PUBLIC\_IP を独自の要求に適した値に置き換えてください)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME/providers/microsoft.resources/deployments/DEPLOYMENT_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "properties": {
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json",
      "contentVersion": "1.0.0.0",
    },
    "mode": "Incremental",
    "parameters": {
        "newStorageAccountName": {
          "value": "GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME"
        },
        "adminUsername": {
          "value": "ADMIN_USER_NAME"
        },
        "adminPassword": {
          "value": "ADMIN_PASSWORD"
        },
        "dnsNameForPublicIP": {
          "value": "DNS_NAME_FOR_PUBLIC_IP"
        },
        "ubuntuOSVersion": {
          "value": "15.04"
        }
    }
  }
}
```

このドキュメントを読みやすくするために、この要求に対する非常に長い JSON の応答は省略されています。応答には、先ほど作成したテンプレート化されたデプロイに関する情報が含まれます。

<!---HONumber=AcomDC_0921_2016-->