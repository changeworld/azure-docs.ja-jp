---
title: Azure Monitor のサインイン ログ スキーマ | Microsoft Docs
description: Azure Monitor で使用する Azure AD サインイン ログ スキーマについて説明します
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 08/26/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d0cc6d793c70f9466a09c9ad54e3588295da5ae
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2021
ms.locfileid: "122969159"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor"></a>Azure Monitor での Azure AD サインイン ログ スキーマを解釈する

この記事では、Azure Monitor での Azure Active Directory (Azure AD) サインイン ログ スキーマについて説明します。 サインインに関連する情報のほとんどは、`records` オブジェクトの *Properties* 属性で提供されます。


```json
{
  "time": "2019-03-12T16:02:15.5522137Z",
  "resourceId": "/tenants/<TENANT ID>/providers/Microsoft.aadiam",
  "operationName": "Sign-in activity",
  "operationVersion": "1.0",
  "category": "SignInLogs",
  "tenantId": "<TENANT ID>",
  "resultType": "50140",
  "resultSignature": "None",
  "resultDescription": "This error occurred due to 'Keep me signed in' interrupt when the user was signing-in.",
  "durationMs": 0,
  "callerIpAddress": "<CALLER IP ADDRESS>",
  "correlationId": "a75a10bd-c126-486b-9742-c03110d36262",
  "identity": "Timothy Perkins",
  "Level": 4,
  "location": "US",
  "properties": 
       {
    "id": "0231f922-93fa-4005-bb11-b344eca03c01",
    "createdDateTime": "2019-03-12T16:02:15.5522137+00:00",
    "userDisplayName": "Timothy Perkins",
    "userPrincipalName": "<USER PRINCIPAL NAME>",
    "userId": "<USER ID>",
    "appId": "<APPLICATION ID>",
    "appDisplayName": "Azure Portal",
    "ipAddress": "<IP ADDRESS>",
    "status": {
      "errorCode": 50140,
      "failureReason": "This error occurred due to 'Keep me signed in' interrupt when the user was signing-in."
    },
    "clientAppUsed": "Browser",
    "userAgent": "<USER AGENT>",
    "deviceDetail":
   {
     "deviceId": "8bfcb982-6856-4402-924c-ada2486321cc",
     "operatingSystem": "Windows 10",
     "browser": "Chrome 72.0.3626"
     },
    "location":
    {
      "city": "Bellevue",
      "state": "Washington",
      "countryOrRegion": "US",
      "geoCoordinates": 
     {
        "latitude": 45,
        "longitude": 122
      }
    },
    "correlationId": "a75a10bd-c126-486b-9742-c03110d36262",
    "conditionalAccessStatus": "notApplied",
    "appliedConditionalAccessPolicies": [
      {
        "id": "ae11ffaa-9879-44e0-972c-7538fd5c4d1a",
        "displayName": "HR app access policy",
        "enforcedGrantControls": [
          "Mfa"
        ],
        "enforcedSessionControls": [],
        "result": "notApplied",
        "conditionsSatisfied": 0,
        "conditionsNotSatisfied": 0
      },
      {
        "id": "b915a70b-2eee-47b6-85b6-ff4f4a66256d",
        "displayName": "MFA for all but global support access",
        "enforcedGrantControls": [],
        "enforcedSessionControls": [],
        "result": "notEnabled",
        "conditionsSatisfied": 0,
        "conditionsNotSatisfied": 0
      },
      {
        "id": "830f27fa-67a8-461f-8791-635b7225caf1",
        "displayName": "Header Based Application Control",
        "enforcedGrantControls": [
          "Mfa"
        ],
        "enforcedSessionControls": [],
        "result": "notApplied",
        "conditionsSatisfied": 0,
        "conditionsNotSatisfied": 0
      },
      {
        "id": "8ed8d7f7-0a2e-437b-b512-9e47bed562e6",
        "displayName": "MFA for everyones",
        "enforcedGrantControls": [],
        "enforcedSessionControls": [],
        "result": "notEnabled",
        "conditionsSatisfied": 0,
        "conditionsNotSatisfied": 0
      },
      {
        "id": "52924e0f-798b-4afd-8c42-49055c7d6395",
        "displayName": "Device compliant",
        "enforcedGrantControls": [],
        "enforcedSessionControls": [],
        "result": "notEnabled",
        "conditionsSatisfied": 0,
        "conditionsNotSatisfied": 0
      }
    ],
    "originalRequestId": "f2f0a254-f831-43b9-bcb0-2646fb645c00",
    "isInteractive": true,
    "authenticationProcessingDetails": [
      {
        "key": "Login Hint Present",
        "value": "True"
      }
    ],
    "networkLocationDetails": [],
    "processingTimeInMilliseconds": 238,
    "riskDetail": "none",
    "riskLevelAggregated": "none",
    "riskLevelDuringSignIn": "none",
    "riskState": "none",
    "riskEventTypes": [],
    "riskEventTypes_v2": [],
    "resourceDisplayName": "Office 365 SharePoint Online",
    "resourceId": "00000003-0000-0ff1-ce00-000000000000",
    "resourceTenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "homeTenantId": "<USER HOME TENANT ID>",
    "tokenIssuerName": "",
    "tokenIssuerType": "AzureAD",
    "authenticationDetails": [
      {
        "authenticationStepDateTime": "2019-03-12T16:02:15.5522137+00:00",
        "authenticationMethod": "Previously satisfied",
        "succeeded": true,
        "authenticationStepResultDetail": "First factor requirement satisfied by claim in the token",
        "authenticationStepRequirement": "Primary authentication",
        "StatusSequence": 0,
        "RequestSequence": 0
      },
      {
        "authenticationStepDateTime": "2021-08-12T15:48:12.8677211+00:00",
        "authenticationMethod": "Previously satisfied",
        "succeeded": true,
        "authenticationStepResultDetail": "MFA requirement satisfied by claim in the token",
        "authenticationStepRequirement": "Multi-factor authentication"
      }
    ],
    "authenticationRequirementPolicies": [
      {
        "requirementProvider": "multiConditionalAccess",
        "detail": "Conditional Access"
      }
    ],
    "authenticationRequirement": "multiFactorAuthentication",
    "alternateSignInName": "<ALTERNATE SIGN IN>",
    "signInIdentifier": "<SIGN IN IDENTIFIER>",
    "servicePrincipalId": "",
    "userType": "Member",
    "flaggedForReview": false,
    "isTenantRestricted": false,
    "autonomousSystemNumber": 8000,
    "crossTenantAccessType": "none",
    "privateLinkDetails": {},
    "ssoExtensionVersion": ""
    }
}

```


## <a name="field-descriptions"></a>フィールドの説明

| フィールド名 | Key | 説明 |
| --- | --- | --- | 
| Time |  - | 日付と時刻 (UTC)。 |
| ResourceId | - | この値はマップされていないため、このフィールドを無視しても問題ありません。  |
| OperationName | - | サインインの場合、この値は常に *Sign-in activity* です。 |
| OperationVersion | - | クライアントによって要求された REST API のバージョン。 |
| Category | - | サインインの場合、この値は常に *SignIn* です。 | 
| TenantId | - | ログに関連付けられたテナント GUIdentity。 |
| ResultType | - | サインイン操作の結果は、成功した場合は `0`、失敗した場合は "*エラー コード*" です。 | 
| ResultSignature | - | この値は常に *None* です。 |
| ResultDescription | 該当なしまたは空白 | サインイン操作のエラーの説明を指定します。 |
| riskDetail | riskDetail | 危険なユーザー、サインイン、リスク検出の特定の状態の背後にある "理由" を提供します。 値には `none`、`adminGeneratedTemporaryPassword`、`userPerformedSecuredPasswordChange`、`userPerformedSecuredPasswordReset`、`adminConfirmedSigninSafe`、`aiConfirmedSigninSafe`、`userPassedMFADrivenByRiskBasedPolicy`、`adminDismissedAllRiskForUser`、`adminConfirmedSigninCompromised`、`unknownFutureValue` があります。 値 `none` は、ユーザーまたはサインインに対してこれまで何のアクションも行われていないことを意味します。 <br>**注:** このプロパティの詳細には Azure AD Premium P2 ライセンスが必要です。 その他のライセンスからは、値 `hidden` が返されます。 |
| riskEventTypes | riskEventTypes | サインインに関連するリスク検出の種類。 値には `unlikelyTravel`、`anonymizedIPAddress`、`maliciousIPAddress`、`unfamiliarFeatures`、`malwareInfectedIPAddress`、`suspiciousIPAddress`、`leakedCredentials`、`investigationsThreatIntelligence`、`generic`、`unknownFutureValue` があります。 |
| authProcessingDetails | Azure AD アプリ認証ライブラリ | ファミリ、ライブラリ、およびプラットフォームの情報が次の形式で含まれています: "Family: ADAL Library: ADAL.JS 1.0.0 Platform: JS" |
| authProcessingDetails | IsCAEToken | 値は True または False |
| riskLevelAggregated | riskLevel | 集計リスク レベル。 値には `none`、`low`、`medium`、`high`、`hidden`、`unknownFutureValue` があります。 値 `hidden` は、Azure AD Identity Protection に対してユーザーまたはサインインが有効にされなかったことを意味します。 **注:** このプロパティの詳細は、Azure AD Premium P2 のお客様のみが利用できます。 その他の顧客には、`hidden` が返されます。 |
| riskLevelDuringSignIn | riskLevel | サインイン中のリスク レベル。 値には `none`、`low`、`medium`、`high`、`hidden`、`unknownFutureValue` があります。 値 `hidden` は、Azure AD Identity Protection に対してユーザーまたはサインインが有効にされなかったことを意味します。 **注:** このプロパティの詳細は、Azure AD Premium P2 のお客様のみが利用できます。 その他の顧客には、`hidden` が返されます。 |
| riskState | riskState | 危険なユーザー、サインイン、リスク検出の状態を報告します。 値には、`none`、`confirmedSafe`、`remediated`、`dismissed`、`atRisk`、`confirmedCompromised`、`unknownFutureValue` があります。 |
| DurationMs | - | この値はマップされていないため、このフィールドを無視しても問題ありません。 |
| CallerIpAddress | - | 要求を行ったクライアントの IP アドレス。 | 
| CorrelationId | - | クライアントから渡される省略可能な GUID。 この値は、クライアント側の操作をサーバー側の操作と関連付けるために役立ちます。また、複数のサービスにまたがるログをトレースする場合に便利です。 |
| ID | - | 要求を行ったときに提示されたトークンからの ID。 ユーザー アカウント、システム アカウント、またはサービス プリンシパルです。 |
| Level | - | メッセージの種類を指定します。 監査の場合は常に *Informational* です。 |
| Location | - | サインイン アクティビティの場所を指定します。 |
| Properties | - | サインインに関連付けられたすべてのプロパティを一覧表示します。|



## <a name="next-steps"></a>次のステップ

* [Azure Monitor で監査ログのスキーマを解釈する](./overview-reports.md)
* [Azure でのプラットフォーム ログの詳細について読む](../../azure-monitor/essentials/platform-logs-overview.md)