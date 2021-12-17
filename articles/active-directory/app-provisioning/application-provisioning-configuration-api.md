---
title: Microsoft Graph API を使用してプロビジョニングを構成する
description: Microsoft Graph API を使用して自動プロビジョニングの構成を自動化し、時間を節約する方法について説明します。
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 06/03/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: b9d337af752d7b463ffc4922b903e4b6064c6881
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131028084"
---
# <a name="configure-provisioning-using-microsoft-graph-apis"></a>Microsoft Graph API を使用してプロビジョニングを構成する

Azure portal は、個々のアプリのプロビジョニングを一度に 1 つずつ構成する便利な方法です。 しかし、アプリケーションのインスタンスを複数作成する場合、または作成するインスタンスの数が数百にも及ぶ場合は、Microsoft Graph API を使用してアプリの作成と構成を自動化すると、より簡単です。 この記事では、API を使用してプロビジョニングの構成を自動化する方法について説明します。 この方法は、[アマゾン ウェブ サービス](../saas-apps/amazon-web-service-tutorial.md#configure-azure-ad-sso)などのアプリケーションで一般的に使用されています。

**Microsoft Graph API を使用してプロビジョニングの構成を自動化する手順の概要**


|手順  |詳細  |
|---------|---------|
|[手順 1. ギャラリー アプリケーションを作成する](#step-1-create-the-gallery-application)     |API クライアントにサインインする <br> ギャラリー アプリケーション テンプレートを取得する <br> ギャラリー アプリケーションを作成する         |
|[手順 2. テンプレートに基づいてプロビジョニング ジョブを作成する](#step-2-create-the-provisioning-job-based-on-the-template)     |プロビジョニング コネクタのテンプレートを取得する <br> プロビジョニング ジョブを作成する         |
|[手順 3. アクセスを承認する](#step-3-authorize-access)     |アプリケーションへの接続をテストする <br> 資格情報を保存する         |
|[手順 4. プロビジョニング ジョブを開始する](#step-4-start-the-provisioning-job)     |ジョブの開始         |
|[手順 5. プロビジョニングを監視する](#step-5-monitor-provisioning)     |プロビジョニング ジョブの状態を確認する <br> プロビジョニング ログを取得する         |

## <a name="step-1-create-the-gallery-application"></a>手順 1:ギャラリー アプリケーションを作成する

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>Microsoft Graph エクスプローラー (推奨)、Postman、または使用しているその他の API クライアントにサインインする

1. [Microsoft Graph エクスプローラー](https://developer.microsoft.com/graph/graph-explorer)を開始します。
1. [Sign-In with Microsoft]\(Microsoft アカウントでサインイン\) ボタンを選択し、Azure AD 全体管理者またはアプリ管理者の資格情報を使用してサインインします。
1. サインインに成功すると、左側のペインにユーザー アカウントの詳細が表示されます。

### <a name="retrieve-the-gallery-application-template-identifier"></a>ギャラリー アプリケーション テンプレート識別子を取得する
Azure AD アプリケーション ギャラリーのアプリケーションにはそれぞれ、そのアプリケーションのメタデータを記述する[アプリケーション テンプレート](/graph/api/applicationtemplate-list?tabs=http&view=graph-rest-beta&preserve-view=true)があります。 このテンプレートを使用して、管理用のテナントにアプリケーションとサービス プリンシパルのインスタンスを作成できます。 **AWS Single-Account Access** のアプリケーション テンプレートとその応答の識別子を取得し、**id** プロパティの値を記録します。これは、このチュートリアルの後の部分で使用します。

#### <a name="request"></a>Request

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates?$filter=displayName eq 'AWS Single-Account Access'
```
#### <a name="response"></a>Response

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationTemplate",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [
  {
    "id": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Single-Account Access",
        "homePageUrl": "http://aws.amazon.com/",
        "supportedSingleSignOnModes": [
             "password",
             "saml",
             "external"
         ],
         "supportedProvisioningTypes": [
             "sync"
         ],
         "logoUrl": "https://az495088.vo.msecnd.net/app-logo/aws_215.png",
         "categories": [
             "developerServices"
         ],
         "publisher": "Amazon",
         "description": "Federate to a single AWS account and use SAML claims to authorize access to AWS IAM roles. If you have many AWS accounts, consider using the AWS Single Sign-On gallery application instead."    

}
```

### <a name="create-the-gallery-application"></a>ギャラリー アプリケーションを作成する

前のステップで取得したアプリケーションのテンプレート ID を使用して、ご自身のテナント内にアプリケーションとサービス プリンシパルの[インスタンスを作成](/graph/api/applicationtemplate-instantiate?tabs=http&view=graph-rest-beta&preserve-view=true)します。

#### <a name="request"></a>Request


```msgraph-interactive
POST https://graph.microsoft.com/beta/applicationTemplates/{id}/instantiate
Content-type: application/json

{
  "displayName": "AWS Contoso"
}
```

#### <a name="response"></a>Response

```http
HTTP/1.1 201 OK
Content-type: application/json

{
    "application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63",
        "appId": "92653dd4-aa3a-3323-80cf-e8cfefcc8d5d",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Contoso",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "logoutUrl": null,
        "samlMetadataUrl": null,
    },
    "servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e",
        "appDisplayName": "AWS Contoso",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "appRoleAssignmentRequired": true,
        "displayName": "My custom name",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "servicePrincipalNames": [
            "93653dd4-aa3a-4323-80cf-e8cfefcc8d7d"
        ],
        "tags": [
            "WindowsAzureActiveDirectoryIntegratedApp"
        ],
    }
}
```

## <a name="step-2-create-the-provisioning-job-based-on-the-template"></a>手順 2. テンプレートに基づいてプロビジョニング ジョブを作成する

### <a name="retrieve-the-template-for-the-provisioning-connector"></a>プロビジョニング コネクタのテンプレートを取得する

ギャラリー内の、プロビジョニングが有効になっているアプリケーションには、構成を効率化するためのテンプレートがあります。 次の要求を使用して、[プロビジョニング構成のテンプレートを取得](/graph/api/synchronization-synchronizationtemplate-list?tabs=http&view=graph-rest-beta&preserve-view=true)します。 ID を指定する必要があることに注意してください。 この ID は前のリソース (この場合は servicePrincipal リソース) を示します。 

#### <a name="request"></a>Request

```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/templates
```

#### <a name="response"></a>Response
```http
HTTP/1.1 200 OK

{
    "value": [
        {
            "id": "aws",
            "factoryTag": "aws",
            "schema": {
                    "directories": [],
                    "synchronizationRules": []
                    }
        }
    ]
}
```

### <a name="create-the-provisioning-job"></a>プロビジョニング ジョブを作成する
プロビジョニングを有効にするには、まず[ジョブを作成する](/graph/api/synchronization-synchronizationjob-post?tabs=http&view=graph-rest-beta&preserve-view=true)必要があります。 プロビジョニング ジョブを作成するには、次の要求を使用します。 ジョブに使用するテンプレートを指定するときは、前の手順の templateId を使用します。

#### <a name="request"></a>Request

```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs
Content-type: application/json

{ 
    "templateId": "aws"
}
```

#### <a name="response"></a>Response
```http
HTTP/1.1 201 OK
Content-type: application/json

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "NotConfigured",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    }
}
```

## <a name="step-3-authorize-access"></a>手順 3. アクセスを承認する

### <a name="test-the-connection-to-the-application"></a>アプリケーションへの接続をテストする

サードパーティ アプリケーションとの接続をテストします。 クライアント シークレットとシークレット トークンを必要とするアプリケーションの例を次に示します。 アプリケーションにはそれぞれ独自の要件があります。 多くの場合、クライアント シークレットの代わりにベース アドレスがアプリケーションで使用されます。 アプリで必要な資格情報を確認するには、アプリケーションのプロビジョニング構成ページに移動し、開発者モードで **[テスト接続]** をクリックします。 ネットワーク トラフィックに、資格情報に使用されたパラメーターが表示されます。 資格情報の完全な一覧については、「[synchronizationJob: validateCredentials](/graph/api/synchronization-synchronizationjob-validatecredentials?tabs=http&view=graph-rest-beta&preserve-view=true)」を参照してください。 Azure Databricks などのほとんどのアプリケーションは、BaseAddress と SecretToken に依存しています。 BaseAddress は、Azure portal でテナントの URL として表されます。 

#### <a name="request"></a>Request
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{id}/validateCredentials

{ 
    "credentials": [ 
        { 
            "key": "ClientSecret", "value": "xxxxxxxxxxxxxxxxxxxxx" 
        },
        {
            "key": "SecretToken", "value": "xxxxxxxxxxxxxxxxxxxxx"
        }
    ]
}
```
#### <a name="response"></a>Response
```http
HTTP/1.1 204 No Content
```

### <a name="save-your-credentials"></a>資格情報を保存する

プロビジョニングを構成するには、Azure AD とアプリケーションの間に信頼を確立する必要があります。 サードパーティ アプリケーションへのアクセスを承認します。 クライアント シークレットとシークレット トークンを必要とするアプリケーションの例を次に示します。 アプリケーションにはそれぞれ独自の要件があります。 [API ドキュメント](/graph/api/synchronization-synchronizationjob-validatecredentials?tabs=http&view=graph-rest-beta&preserve-view=true)を参照して、使用可能なオプションを確認してください。 

#### <a name="request"></a>Request
```msgraph-interactive
PUT https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 

{ 
    "value": [ 
        { 
            "key": "ClientSecret", "value": "xxxxxxxxxxxxxxxxxxxxx"
        },
        {
            "key": "SecretToken", "value": "xxxxxxxxxxxxxxxxxxxxx"
        }
    ]
}
```

#### <a name="response"></a>Response
```http
HTTP/1.1 204 No Content
```

## <a name="step-4-start-the-provisioning-job"></a>手順 4: プロビジョニング ジョブを開始する
プロビジョニング ジョブが構成されたので、次のコマンドを使用して[ジョブを開始](/graph/api/synchronization-synchronizationjob-start?tabs=http&view=graph-rest-beta&preserve-view=true)します。 


#### <a name="request"></a>Request

```http
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/start
```


#### <a name="response"></a>Response
```http
HTTP/1.1 204 No Content
```


## <a name="step-5-monitor-provisioning"></a>手順 5: プロビジョニングを監視する

### <a name="monitor-the-provisioning-job-status"></a>プロビジョニング ジョブの状態を監視する

プロビジョニング ジョブが実行中になっているので、次のコマンドを使用して、現在のプロビジョニング サイクルの進行状況と、ターゲット システムで作成されたユーザーとグループの数などの現在までの統計情報を追跡します。 

#### <a name="request"></a>Request
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

#### <a name="response"></a>Response
```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "progress": [],
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    },
    "synchronizationJobSettings": [
      {
          "name": "QuarantineTooManyDeletesThreshold",
          "value": "500"
      }
    ]
}
```


### <a name="monitor-provisioning-events-using-the-provisioning-logs"></a>プロビジョニング ログを使用してプロビジョニング イベントを監視する
プロビジョニング ジョブの状態の監視に加えて、[プロビジョニング ログ](/graph/api/provisioningobjectsummary-list?tabs=http&view=graph-rest-beta&preserve-view=true)を使用して、発生しているすべてのイベントについてクエリを実行できます。 たとえば、特定のユーザーについてクエリを実行し、正常にプロビジョニングされたかどうかを判断します。

#### <a name="request"></a>Request
```msgraph-interactive
GET https://graph.microsoft.com/beta/auditLogs/provisioning
```
#### <a name="response"></a>Response
```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#auditLogs/provisioning",
    "value": [
        {
            "id": "gc532ff9-r265-ec76-861e-42e2970a8218",
            "activityDateTime": "2019-06-24T20:53:08Z",
            "tenantId": "7928d5b5-7442-4a97-ne2d-66f9j9972ecn",
            "cycleId": "44576n58-v14b-70fj-8404-3d22tt46ed93",
            "changeId": "eaad2f8b-e6e3-409b-83bd-e4e2e57177d5",
            "action": "Create",
            "durationInMilliseconds": 2785,
            "sourceSystem": {
                "id": "0404601d-a9c0-4ec7-bbcd-02660120d8c9",
                "displayName": "Azure Active Directory",
                "details": {}
            },
            "targetSystem": {
                "id": "cd22f60b-5f2d-1adg-adb4-76ef31db996b",
                "displayName": "AWS Contoso",
                "details": {
                    "ApplicationId": "f2764360-e0ec-5676-711e-cd6fc0d4dd61",
                    "ServicePrincipalId": "chc46a42-966b-47d7-9774-576b1c8bd0b8",
                    "ServicePrincipalDisplayName": "AWS Contoso"
                }
            },
            "initiatedBy": {
                "id": "",
                "displayName": "Azure AD Provisioning Service",
                "initiatorType": "system"
            }
            ]
       }
    ]
}
```
## <a name="see-also"></a>関連項目

- [同期に関する Microsoft Graph のドキュメントを確認する](/graph/api/resources/synchronization-overview?view=graph-rest-beta&preserve-view=true)
- [カスタム SCIM アプリと Azure AD の統合](./use-scim-to-provision-users-and-groups.md)
