---
title: HR プロビジョニングでのユーザーの更新に関する問題のトラブルシューティング
description: HR プロビジョニングでのユーザーの更新に関する問題をトラブルシューティングする方法について説明します
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: troubleshooting
ms.workload: identity
ms.date: 10/27/2021
ms.author: kenwith
ms.reviewer: chmutali
ms.openlocfilehash: bdd4dbc7c0be9eec3a2df7bab4315ea8d8e59a37
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478379"
---
# <a name="troubleshoot-hr-user-update-issues"></a>HR ユーザーの更新に関する問題のトラブルシューティング

## <a name="null-and-empty-values-not-processed-as-expected"></a>null 値と空の値が想定どおりに処理されない
**適用対象:**
* Workday からオンプレミスの Active Directory へのユーザー プロビジョニング
* Workday から Azure Active Directory へのユーザー プロビジョニング
* SAP SuccessFactors からオンプレミスの Active Directory へのユーザー プロビジョニング
* SAP SuccessFactors から Azure Active Directory へのユーザー プロビジョニング

| | |
|-- | -- |
| **問題点** | 受信プロビジョニング アプリは正常に構成されました。 HR アプリから null 値または空の値を取得しています。 プロビジョニング サービスによって、オンプレミスの Active Directory または Azure AD の対応するターゲット属性値がクリアされると想定しています。 しかし、`InvalidAttributeSyntax-LdapErr: The syntax is invalid. The parameter is incorrect. Error in attribute conversion operation, data 0, v3839` というエラー メッセージが表示され、この操作が失敗します。 |
| **原因** | プロビジョニング サービスには、null 値を処理するための既定のロジックがありません。 プロビジョニング サービスでは、ソース アプリから空の文字列を取得すると、ターゲット アプリにその値を "そのまま" フローしようとします。 この場合、オンプレミスの Active Directory では空の文字列値の設定はサポートされていないため、上記のエラーが表示されます。 |
| **解像度** | プロビジョニングのログを確認します。 null 値または空の文字列値を受け取る、ターゲット Active Directory の属性を特定します。 該当する属性の属性マッピングを更新して、式マッピングを使用します。 以下の推奨される解決方法を参照してください。 |

**推奨される解決方法**

  たとえば、AD 属性 `jobTitle` にマップされている属性 `BusinessTitle` が、Workday では null または空であるとします。 
  * 方法 1: [IIF](functions-for-customizing-application-data.md#iif)、[IsNullOrEmpty](functions-for-customizing-application-data.md#isnullorempty)、[Coalesce](functions-for-customizing-application-data.md#coalesce)、[IsPresent](functions-for-customizing-application-data.md#ispresent) などの関数を使用して空の値または null 値をチェックする式を定義し、空白ではないリテラル値を渡します。 
  
     `IIF(IsNullOrEmpty([BusinessTitle]),"N/A",[BusinessTitle])`

  * 方法 2: [IgnoreFlowIfNullOrEmpty](functions-for-customizing-application-data.md#ignoreflowifnullorempty) 関数を使用して、オンプレミスの Active Directory または Azure AD に送信されるペイロード内の空の属性または null 属性を削除します。 
  
     `IgnoreFlowIfNullOrEmpty([BusinessTitle])` 

## <a name="some-workday-attribute-updates-are-missing"></a>一部の Workday 属性の更新が見つからない
**適用対象:**
* Workday からオンプレミスの Active Directory へのユーザー プロビジョニング
* Workday から Azure Active Directory へのユーザー プロビジョニング

| | |
|-- | -- |
| **問題点** | Workday 受信プロビジョニング アプリは正常に構成され、Workday テナント URL に正常に接続されています。 Workday からの特定の属性更新のフローに遅延が発生していること、または増分同期中に Workday からの属性変更が予想どおりに反映されない場合があることを確認しました。 |
| **原因** | 増分同期中、プロビジョニング アプリは Workday のトランザクション ログに対してプライマリ Worker エンティティの変更のクエリを実行し、Workday のトランザクション ログで追跡されている変更だけが処理されます。 <br> 設定の Workday 属性の変更が Workday のトランザクション ログで追跡されていない場合、Azure AD はその変更を取得できません。 たとえば、*LocalReference* Workday 属性は既定の属性マッピングの一部であり、XPATH `wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Local_Reference/wd:ID[@wd:type='Locale_ID']/text()` が含まれています。 この属性は、*Business_Site_Summary_Data* エンティティの一部であることに注意してください。 Workday のこの属性の値が変更されても、Workday のトランザクション ログには表示されません。 そのため、増分同期中にこの属性の新しい値が表示されるのは、同期間隔中にプライマリ Worker エンティティに関連付けられている属性も変更された場合に限られます。 |
| **解像度** | 特定の Workday 属性の変更が反映されないというこの動作が頻繁に発生する場合は、週 1 回または月 1 回の完全同期を定期的に実行することをお勧めします。 |

## <a name="user-match-with-extensionattribute-not-working"></a>extensionAttribute を使用したユーザー照合が機能しない
**適用対象:**
* Workday から Azure Active Directory へのユーザー プロビジョニング
* SAP SuccessFactors から Azure Active Directory へのユーザー プロビジョニング

| | |
|-- | -- |
| **問題点** | たとえば、Azure AD の *extensionAttribute3* を使用して従業員 ID を保存し、それを Workday の *WorkerID* または SuccessFactors の *personIdExternal* 属性にマップしてユーザーの照合を行うとします。 この構成では、プロビジョニング プロセスの照合手順が失敗します。 この問題は、ユーザーの作成と更新の両方に影響します。 |
| **原因** | **Azure AD Graph API** の `$filter` パラメーターでは、[extensionAttributes によるフィルター処理はサポート](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#filter)されていないため、Azure AD の *OnPremisesExtensionAttributes* (`extensionAttributes1-15`) を照合属性として使用することはできません。 |
| **解像度** | 照合属性のペアに Azure AD の *OnPremisesExtensionAttributes* (`extensionAttributes1-15`) を使用しないでください。 employeeID を使用します。 |



## <a name="next-steps"></a>次のステップ

* [Azure AD と Workday の統合シナリオと Web サービス呼び出しについて](workday-integration-reference.md)
* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](check-status-user-account-provisioning.md)

