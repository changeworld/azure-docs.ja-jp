---
title: Azure Managed Applications と通知
description: マネージド アプリケーション インスタンスでの作成、更新、削除、およびエラーに関する通知を受け取るように、Webhook エンドポイントを使用して Managed Application を構成します。
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.reviewer: ''
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: a00e5be4493b8c8116e2925e88a3ce4bf8cfb722
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2019
ms.locfileid: "74085199"
---
# <a name="azure-managed-applications-with-notifications"></a>Azure Managed Applications と通知

Azure Managed Application の通知を使用することで、パブリッシャーは、マネージド アプリケーション インスタンスのライフサイクル イベントに基づいてアクションを自動化することができます。 パブリッシャーは、カスタム通知 Webhook エンドポイントを指定して、新規および既存のマネージド アプリケーション インスタンスに関するイベント通知を受け取ることができます。 それにより、パブリッシャーは、アプリケーションのプロビジョニング、更新、および削除時のカスタム ワークフローを設定することができます。

## <a name="getting-started"></a>使用の開始
マネージド アプリケーションの受信を開始するには、パブリック HTTPS エンドポイントを起動し、サービス カタログ アプリケーション定義または Marketplace オファーを発行するときにそれを指定します。

次に、スピーディな起動と実行のために推奨される一連の手順を示します。
1. 受信 POST 要求をログに記録し、`200 OK` を返すパブリック HTTPS エンドポイントを起動します。
2. 次に説明するように、そのエンドポイントをサービス カタログ アプリケーション定義または Marketplace オファーに追加します。
3. アプリケーション定義または Marketplace オファーを参照するマネージド アプリケーション インスタンスを作成します。
4. 通知が正常に受信されていることを確認します。
5. 下の「**エンドポイントの認証**」セクションの説明に従って承認を有効にします。
6. 下の「**通知スキーマ**」のドキュメントに従って、通知要求を解析し、通知に基づいてビジネス ロジックを実装します。

## <a name="adding-service-catalog-application-definition-notifications"></a>サービス カタログ アプリケーション定義の通知の追加
#### <a name="azure-portal"></a>Azure ポータル
開始するには、「[Azure portal 経由でサービス カタログ アプリケーションを発行する](./publish-portal.md)」をお読みください。

![ポータルでのサービス カタログ アプリケーション定義の通知](./media/publish-notifications/service-catalog-notifications.png)
#### <a name="rest-api"></a>REST API

> [!NOTE]
> 現在、アプリケーション定義プロパティの **notificationEndpoints** の一部としてサポートされているのは 1 つのエンドポイントだけです

``` JSON
    {
      "properties": {
        "isEnabled": true,
        "lockLevel": "ReadOnly",
        "displayName": "Sample Application Definition",
        "description": "Notification-enabled application definition.",
        "notificationPolicy": {
            "notificationEndpoints": [
                {
                    "uri": "https://isv.azurewebsites.net:1214?sig=unique_token"
                }
            ]
        },
        "authorizations": [
          {
            "principalId": "d6b7fbd3-4d99-43fe-8a7a-f13aef11dc18",
            "roleDefinitionId": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635"
          },
        ...

```
## <a name="adding-marketplace-managed-application-notifications"></a>Marketplace マネージド アプリケーション通知の追加
詳細については、「[Azure アプリケーション オファーを作成する](../marketplace/cloud-partner-portal/azure-applications/cpp-create-offer.md)」を参照してください。

![ポータルでのサービス カタログ アプリケーション定義の通知](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>Event triggers (イベント トリガー)
次の表では、EventType と ProvisioningState の考えられるすべての組み合わせとそれらのトリガーについて説明します。

EventType | ProvisioningState | 通知のトリガー
---|---|---
PUT | 承認済み | 管理対象リソース グループが作成され、アプリケーションの PUT 後に正常にプロジェクションされた。 (管理対象リソース グループ内のデプロイが開始される前。)
PUT | 成功 | PUT 後に、マネージド アプリケーションのフル プロビジョニングが成功した。
PUT | 失敗 | 任意の時点でのアプリケーション インスタンス プロビジョニングの PUT の失敗。
PATCH | 成功 | タグ、JIT アクセス ポリシー、またはマネージド ID を更新するための、マネージド アプリケーション インスタンスに対する修正プログラムが正常に適用された後。
DELETE | 削除中 | ユーザーがマネージド アプリ インスタンスの DELETE を開始するとすぐ。
DELETE | Deleted | マネージド アプリケーションの完全な削除が正常に行われた後。
DELETE | 失敗 | 削除をブロックする、プロビジョニング解除プロセス中のエラーの後。
## <a name="notification-schema"></a>通知スキーマ
通知を処理するために Webhook エンドポイントを起動する場合は、ペイロードを解析して重要なプロパティを取得してから、通知に基づいて対応する必要があります。 サービス カタログと Marketplace マネージド アプリケーションの通知は両方とも多くの同じプロパティを使用しますが、以下に示すわずかな違いがあります。

#### <a name="service-catalog-application-notification-schema"></a>サービス カタログ アプリケーションの通知スキーマ
マネージド アプリケーション インスタンスのプロビジョニングが正常に完了した後のサービス カタログ通知の例を次に示します。
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>"    
}

```

プロビジョニングが失敗すると、エラーの詳細を含む通知が指定されたエンドポイントに送信されます。

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>",
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

#### <a name="marketplace-application-notification-schema"></a>Marketplace アプリケーションの通知スキーマ

マネージド アプリケーション インスタンスのプロビジョニングが正常に完了した後のサービス カタログ通知の例を次に示します。
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    }
}

```

プロビジョニングが失敗すると、エラーの詳細を含む通知が指定されたエンドポイントに送信されます。

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    },
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

パラメーター | 説明
---|---
eventType | 通知をトリガーしたイベントの種類。 (例: "PUT"、"PATCH"、"DELETE")
applicationId | 通知がトリガーされたマネージド アプリケーションの完全修飾リソース識別子。 
eventTime | 通知をトリガーしたイベントのタイムスタンプ。 (UTC ISO 8601 形式の日付と時刻。)
provisioningState | マネージド アプリケーション インスタンスのプロビジョニングの状態。 (例: "Succeeded"、"Failed"、"Deleting"、"Deleted")
error | *provisioningState が Failed の場合にのみ指定されます*。 エラー コード、メッセージ、およびエラーの原因となった問題の詳細が含まれます。
applicationDefinitionId | *サービス カタログ マネージド アプリケーションに対してのみ指定されます*。 マネージド アプリケーション インスタンスがプロビジョニングされたアプリケーション定義の完全修飾リソース識別子を表します。
プラン | *Marketplace マネージド アプリケーションに対してのみ指定されます*。 マネージド アプリケーション インスタンスのパブリッシャー、オファー、SKU、およびバージョンを表します。

## <a name="endpoint-authentication"></a>エンドポイントの認証
Webhook エンドポイントをセキュリティで保護し、通知の信頼性を確保するには、次のようにします。
- https://your-endpoint.com?sig=Guid のように、Webhook URI に加えて、クエリ パラメーターを指定します。 各通知で、クエリ パラメーター `sig` が予期された値 `Guid` を持っていることを簡単に確認します。
- applicationId を使用してマネージド アプリケーション インスタンスに対して GET を発行します。 一貫性を確保するために、provisioningState が通知の provisioningState と一致していることを確認します。

## <a name="notification-retries"></a>通知の再試行

マネージド アプリケーション通知サービスは、Webhook エンドポイントから通知に対して `200 OK` 応答を予期しています。 Webhook エンドポイントが HTTP エラーコード 500 以上または 429 を返した場合、あるいはエンドポイントに一時的に到達できない場合、通知サービスは再試行します。 Webhook エンドポイントが 10 時間以内に利用可能にならない場合、通知メッセージは削除され、再試行は停止されます。

