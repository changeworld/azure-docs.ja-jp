---
title: 人事のプロビジョニングでのユーザー作成に関する問題のトラブルシューティング
description: 人事のプロビジョニングでのユーザー作成に関する問題をトラブルシューティングする方法について説明します
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: troubleshooting
ms.workload: identity
ms.date: 10/27/2021
ms.author: kenwith
ms.reviewer: chmutali
ms.openlocfilehash: 0c660221c230f884b43252cef25626e3d2b1d700
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324576"
---
# <a name="troubleshoot-hr-user-creation-issues"></a>人事でのユーザー作成に関する問題のトラブルシューティング

## <a name="creation-fails-due-to-null--empty-values"></a>null または空の値が原因で作成に失敗する 

**適用対象:**
* Workday からオンプレミスの Active Directory へのユーザー プロビジョニング
* Workday から Azure Active Directory へのユーザー プロビジョニング
* SAP SuccessFactors からオンプレミスの Active Directory へのユーザー プロビジョニング
* SAP SuccessFactors から Azure Active Directory へのユーザー プロビジョニング

| トラブルシューティング | 詳細 |
|-- | -- |
| **問題点** | 受信プロビジョニング アプリは正常に構成されました。 人事アプリから null または空の値を取得しています。 `InvalidAttributeSyntax-LdapErr: The syntax is invalid. The parameter is incorrect. Error in attribute conversion operation, data 0, v3839` というエラー メッセージが表示され、作成操作が失敗します。 |
| **原因** | プロビジョニング サービスには、null 値処理の既定のロジックがありません。 プロビジョニング サービスでは、ソース アプリから空の文字列を取得すると、対象アプリにその値を "そのまま" フローしようとします。 この場合、オンプレミスの Active Directory では空の文字列値の設定がサポートされないため、上記のエラーが表示されます。 |
| **解像度** | プロビジョニングのログを確認します。 null または空の文字列値を受け取るターゲット Active Directory の属性を識別します。 そのような属性の属性マッピングを更新して、式マッピングを使用します。 以下の推奨される解決方法を参照してください。 |

**推奨される解決方法**

  AD 属性 `jobTitle` にマッピングされている属性 `BusinessTitle` は、Workday では null または空である可能性があるとします。 
  * オプション 1: [IIF](functions-for-customizing-application-data.md#iif)、[IsNullOrEmpty](functions-for-customizing-application-data.md#isnullorempty)、[Coalesce](functions-for-customizing-application-data.md#coalesce)、[IsPresent](functions-for-customizing-application-data.md#ispresent) などの関数を使用して空または null の値をチェックする式を定義し、空でないリテラルの値を渡します。 
  
     `IIF(IsNullOrEmpty([BusinessTitle]),"N/A",[BusinessTitle])`

  * オプション 2: 関数 [IgnoreFlowIfNullOrEmpty](functions-for-customizing-application-data.md#ignoreflowifnullorempty) を使用して、オンプレミスの Active Directory/Azure AD に送信されるペイロード内の空の属性または null 属性を削除します。 
  
     `IgnoreFlowIfNullOrEmpty([BusinessTitle])` 


## <a name="next-steps"></a>次のステップ

* [Azure AD と Workday の統合シナリオと Web サービス呼び出しについて](workday-integration-reference.md)
* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](check-status-user-account-provisioning.md)

