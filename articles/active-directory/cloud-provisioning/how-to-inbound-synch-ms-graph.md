---
title: MS Graph API を使用したクラウド プロビジョニングの受信同期
description: このトピックでは、Graph API のみを使用して受信同期を有効にする方法について説明します
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f308f46fc021a1d08f4065d48558a6dd71786c7c
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860357"
---
# <a name="inbound-synchronization-for-cloud-provisioning-using-ms-graph-api"></a>MS Graph API を使用したクラウド プロビジョニングの受信同期

次のドキュメントでは、MSGraph API のみを使用して 0 から同期プロファイルをレプリケートする方法について説明します。  
これを行う方法の構造は、次の手順で構成されています。  これらは次のとおりです。

- [基本的なセットアップ](#basic-setup)
- [サービス プリンシパルを作成する](#create-service-principals)
- [同期ジョブを作成する](#create-sync-job)
- [ターゲット ドメインを更新する](#update-targeted-domain)
- [同期ジョブを開始する](#start-sync-job)
- [状態をレビューする](#review-status)

これらの [Windows PowerShell 用 Microsoft Azure Active Directory モジュール](https://docs.microsoft.com/powershell/module/msonline/)のコマンドを使用して、運用テナントの同期を有効にします。これは、そのテナントの管理 Web サービスを呼び出すための前提条件となっています。

## <a name="basic-setup"></a>基本的なセットアップ

### <a name="enable-tenant-flags"></a>テナント フラグを有効にする

 ```PowerShell
 Connect-MsolService ('-AzureEnvironment <AzureEnvironmnet>')
 Set-MsolDirSyncEnabled -EnableDirSync $true
 ```
これら 2 つのコマンドのうちの 1 つには、Azure Active Directory 資格情報が必要です。 これらのコマンドレットを使用すると、テナントを暗黙的に識別し、同期のために有効にすることができます。

## <a name="create-service-principals"></a>サービス プリンシパルの作成
次に、[AD2AAD アプリケーションまたはサービス プリンシパル](https://docs.microsoft.com/graph/apiapplicationtemplate-instantiate?view=graph-rest-beta&tabs=http)を作成する必要があります

このアプリケーション ID 1a4721b3-e57f-4451-ae87-ef078703ec94 を使用する必要があります。 displayName は、ポータルで使用されている場合は AD ドメインの URL (例えば contoso.com) ですが、他の名前を付けても構いません。

 ```
 POST https://graph.microsoft.com/beta/applicationTemplates/1a4721b3-e57f-4451-ae87-ef078703ec94/instantiate
 Content-type: application/json
 {
    displayName: [your app name here]
 }
 ```


## <a name="create-sync-job"></a>同期ジョブを作成する
上記のコマンドの出力では、作成されたサービス プリンシパルの objectId が返されます。 この例では、objectId は 614ac0e9-a59b-481f-bd8f-79a73d167e1c です。  このサービス プリンシパルに同期ジョブを追加するには、Microsoft Graph を使用します。  

同期ジョブの作成に関するドキュメントについては、[こちら](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-post?view=graph-rest-beta&tabs=http)を参照してください。

上記の ID を記録していない場合は、次の MS Graph 呼び出しを実行してサービス プリンシパルを見つけることができます。 この呼び出しを行うには、Directory.Read.All 権限が必要です。
 
 `GET https://graph.microsoft.com/beta/servicePrincipals `

次に、出力からアプリ名を探します。

次の 2 つのコマンドを実行して、2 つのジョブを作成します。1 つはユーザーまたはグループのプロビジョニング用、もう 1 つはパスワード ハッシュ同期用です。 これは 2 回繰り返されている同じ要求ですが、テンプレート ID が異なります。


次の 2 つの要求を呼び出します。

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADProvisioning"
 } 
 ```

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADPasswordHash"
 }
 ```

両方を作成する場合は、2 回呼び出す必要があります。

戻り値の例 (プロビジョニングの場合):

 ```
HTTP 201/Created
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#servicePrincipals('614ac0e9-a59b-481f-bd8f-79a73d167e1c')/synchronization/jobs/$entity",
    "id": "AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da",
    "templateId": "ADDCInPassthrough",
    "schedule": {
        "expiration": null,
        "interval": "PT40M",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "quarantine": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "troubleshootingUrl": null,
        "progress": [],
        "synchronizedEntryCountByType": []
    }
}
```

## <a name="update-targeted-domain"></a>ターゲット ドメインを更新する
このテナントでは、サービス プリンシパルのオブジェクト識別子とアプリケーション識別子は次のようになります。

ObjectId:8895955e-2e6c-4d79-8943-4d72ca36878f AppId:00000014-0000-0000-c000-000000000000 DisplayName: testApp

この構成の対象となるドメインを更新する必要があります。そのため、このドメインのシークレットを更新してください。

使用するドメイン名が、オンプレミスのドメイン コントローラーに設定した URL と同じであることを確認します

 ```
 PUT https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 Content-type: application/json
 {
  "value": [
    {
      "key": "Domain",
       {"value":[{"key":"Domain","value":"{\"domain\":\"[YOUR_DOMAIN_NAME]\"}"}]}
    }
  ]
 }
 ```

予期される応答は次の通りです: HTTP 204/No content

ここで、強調表示されている "Domain" 値は、エントリが Azure Active Directory にプロビジョニングされるオンプレミスの Active Directory ドメインの名前です。

## <a name="start-sync-job"></a>同期ジョブを開始する
ジョブは、次のコマンドを使用して再度取得できます。

 `GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ ` 

ジョブを取得するためのドキュメントについては、[こちら](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-list?view=graph-rest-beta&tabs=http)を参照してください。 
 
ジョブを開始するには、最初の手順で作成したサービス プリンシパルの objectId と、ジョブを作成した要求から返されたジョブ識別子を使用して、この要求を発行します。

ジョブを開始する方法のドキュメントについては、[こちら](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-start?view=graph-rest-beta&tabs=http)を参照してください。 

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/start
 ```

予期される応答は次の通りです: HTTP 204/No content。

ジョブを制御するその他のコマンドについては、[こちら](https://docs.microsoft.com/graph/api/resources/synchronization-synchronizationjob?view=graph-rest-beta)に記載されています。
 
ジョブを再起動するには、以下を使用します。

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/restart
 {
   "criteria": {
       "resetScope": "Full"
   }
 }
 ```

## <a name="review-status"></a>状態をレビューする
次を使用してジョブの状態を取得します。

 ```
 GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ 
 ```

関連する詳細については、返されたオブジェクトの 'status' セクションを参照してください。

## <a name="next-steps"></a>次のステップ 

- [Azure AD Connect クラウド プロビジョニングとは](what-is-cloud-provisioning.md)
- [変換](how-to-transformation.md)
- [Azure AD の同期 API](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
