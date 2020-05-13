---
title: Marketplace の測定サービスの認証方法 |Azure Marketplace
description: Azure Marketplace でサポートされている測定サービスの認証方法。
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/03/2020
ms.openlocfilehash: 31b9d4d57e38adcd079082a4f32770c4cbc8fbb3
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/03/2020
ms.locfileid: "82736114"
---
# <a name="marketplace-metering-service-authentication-strategies"></a>Marketplace の測定サービスの認証方法

Marketplace の測定サービスでは、次の 2 つの認証方法をサポートしています。

* [Azure AD セキュリティ トークン](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
* [マネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 

Marketplace の測定サービスで各種認証方法を使用してカスタム メーターを安全に送信するタイミングとその方法について説明します。

## <a name="using-the-azure-ad-security-token"></a>Azure AD セキュリティ トークンの使用

適用可能なプランの種類は、SaaS と、マネージド アプリケーション プラン タイプ対応の Azure アプリケーションです。  

カスタム メーターを送信するには、定義済みの固定アプリケーション ID を使用して認証を行います。

SaaS プランの場合、使用可能なオプションは Azure AD のみです。

マネージド アプリケーション プラン対応の Azure アプリケーションの場合、次のケースでこの方法の使用を検討する必要があります。

* バックエンド サービスと通信するためのメカニズムが既にあり、中央のサービスからカスタム メーターを出力できるようにこのメカニズムを拡張する必要がある場合。
* 複雑なカスタム メーター ロジックを使用している場合。  このロジックは、マネージド アプリケーションのリソースではなく、中央の場所で実行してください。

アプリケーションを登録したら、Azure AD セキュリティ トークンをプログラムで要求できます。 発行元は、このトークンを使用し、それを解決するための要求を行うことが期待されます。

これらのトークンの詳細については、「[Azure Active Directory アクセス トークン](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)」を参照してください。

### <a name="get-a-token-based-on-the-azure-ad-app"></a>Azure AD アプリに基づいたトークンの取得

#### <a name="http-method"></a>HTTP メソッド

**POST**

#### <a name="request-url"></a>*要求 URL*

**`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`**

#### <a name="uri-parameter"></a>*URI パラメーター*

|  **パラメーター名** |  **必須**  |  **説明**          |
|  ------------------ |--------------- | ------------------------  |
|  `tenantId`         |   True         | 登録された Azure AD アプリケーションのテナント ID。   |
| | | |

#### <a name="request-header"></a>*要求ヘッダー*

|  **ヘッダー名**    |  **必須**  |  **説明**          |
|  ------------------ |--------------- | ------------------------  |
|  `Content-Type`     |   True         | 要求に関連付けられたコンテンツの種類。 既定値は `application/x-www-form-urlencoded` です。  |
| | | |

#### <a name="request-body"></a>*要求本文*

|  **プロパティ名**  |  **必須**  |  **説明**          |
|  ------------------ |--------------- | ------------------------  |
|  `Grant_type`       |   True         | 付与タイプ。 既定値は `client_credentials` です。 |
|  `Client_id`        |   True         | Azure AD アプリに関連付けられているクライアントまたはアプリの識別子。|
|  `client_secret`    |   True         | Azure AD アプリに関連付けられているパスワード。  |
|  `Resource`         |   True         | トークンを要求されたターゲット リソース。 既定値は `20e940b3-4c77-4b0b-9a53-9e16a1b010a7` です。  |
| | | |

#### <a name="response"></a>*Response*

|  **名前**    |  **Type**  |  **説明**          |
|  ------------------ |--------------- | ----------------------  |
|  `200 OK`     |   `TokenResponse`    | 要求成功。  |
| | | |

#### <a name="tokenresponse"></a>*TokenResponse*

サンプル応答トークン:

```JSON
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

## <a name="using-the-azure-managed-identities-token"></a>Azure マネージド ID トークンの使用

適用可能なプランの種類は、マネージド アプリケーション プラン タイプ対応の Azure アプリケーションです。

この方法を使用すると、デプロイされたリソース ID で認証を行って、カスタム メーターの使用状況イベントを送信できます。  デプロイの境界内で使用状況を出力するコードを埋め込むことができます。

>[!Note]
>発行元は、使用状況を出力するリソースがロックされていることを確認し、リソースが改ざんされないようにする必要があります。

マネージド アプリケーションには、Virtual Machines から Azure Functions まで、さまざまな種類のリソースを含めることができます。  さまざまなサービスでマネージド ID を使用して認証する方法について詳しくは、「[Azure リソースのマネージド ID を使用する方法](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-can-i-use-managed-identities-for-azure-resources)」をご覧ください。

たとえば、Windows VM を使用して認証を行うには、次の手順に従います。

1. 次のいずれかの方法を使用して、マネージド ID が構成されていることを確認します。
    * [Azure portal UI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)
    * [CLI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm)
    * [PowerShell](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm)
    * [Azure Resource Manager テンプレート](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm)
    * [REST](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-rest-vm#system-assigned-managed-identity)
    * [Azure SDK](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm)

1. システム ID を使用して Marketplace の測定サービス アプリケーション ID (`20e940b3-4c77-4b0b-9a53-9e16a1b010a7`) 用のアクセス トークンを取得し、RDP で VM に接続し、PowerShell コンソールを開いて、次のコマンドを実行します

    ```powershell
    # curl is an alias to Web-Invoke PowerShell command
    # Get system identity access tokenn
    $MetadataUrl = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F"
    $Token = curl -H @{"Metadata" = "true"} $MetadataUrl | Select-Object -Expand Content | ConvertFrom-Json
    $Headers = @{}
    $Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
    ```

1. 現在のリソース グループの "ManagedBy" プロパティからマネージド アプリ ID を取得します

    ```powershell
    # Get subscription and resource group
    $metadata = curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-06-01 | select -ExpandProperty Content | ConvertFrom-Json 
    
    # Make sure the system identity has at least reader permission on the resource group
    $managementUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "?api-version=2019-10-01"
    $resourceGroupInfo = curl -Headers $Headers $managementUrl | select -ExpandProperty Content | ConvertFrom-Json
    $managedappId = $resourceGroupInfo.managedBy 
    ```

1. Marketplace の測定サービスでは、`resourceID` の使用状況 (マネージド アプリケーションの場合は `resourceUsageId` の使用状況も) をレポートする必要があります。

    ```powershell
    # Get resourceUsageId from the managed app
    $managedAppUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "/providers/Microsoft.Solutions/applications/" + $managedappId + "\?api-version=2019-07-01"
    $ManagedApp = curl $managedAppUrl -H $Headers | Select-Object -Expand Content | ConvertFrom-Json
    # Use this resource ID to emit usage 
    $resourceUsageId = $ManagedApp.properties.billingDetails.resourceUsageId
    ```

1. [Marketplace の測定サービス API](https://review.docs.microsoft.com/azure/marketplace/partner-center-portal/marketplace-metering-service-apis?branch=pr-en-us-101847) を使用して、使用状況を出力します。

## <a name="next-steps"></a>次のステップ

* [Azure アプリケーション オファーを作成する](./create-new-azure-apps-offer.md)