---
title: HR プロビジョニングに関する書き戻しに関する問題のトラブルシューティング
description: HR プロビジョニングの書き戻しに関する問題を解決する方法について説明します。
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: troubleshooting
ms.workload: identity
ms.date: 10/27/2021
ms.author: kenwith
ms.reviewer: chmutali
ms.openlocfilehash: 1d40b2738e52866ebc47bbe43b673c52b774619f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324538"
---
# <a name="troubleshoot-hr-write-back-issues"></a>HR の書き戻しに関する問題のトラブルシューティング

## <a name="null-and-empty-values-not-processed-as-expected"></a>Null 値と空の値が予期したとおりに処理されませんでした
**適用対象:**
* Workday Writeback
* SAP SuccessFactors Writeback

| トラブルシューティング | 詳細 |
|-- | -- |
| **問題点** | 書き戻しアプリが正常に構成されました。 Azure AD から null または空の値を取得しています。 プロビジョニング サービスは、HR アプリで対応する電子メールまたは電話番号の値をクリアする必要があります。 しかし、操作は失敗します。 |
| **原因** | プロビジョニング サービスには、null 値処理の既定のロジックがありません。 プロビジョニング サービスは、ソース アプリから空の文字列を取得すると、その値を対象アプリに "その他" としてフローしようとします。 Workday または SuccessFactors が空の値を処理できない場合は、エラーが返されます。 |
| **解像度** | 次の推奨に従って、式マッピングを使用する属性マッピングを更新します。 |

**推奨される解決方法**

  たとえば、SAP SuccessFactors 属性 `businessPhoneNumber` にマッピングされている属性 `telephoneNumber` は、Azure AD では null または空です。 
  * オプション 1: [IIF](functions-for-customizing-application-data.md#iif)、[IsNullOrEmpty](functions-for-customizing-application-data.md#isnullorempty)、[Coalesce](functions-for-customizing-application-data.md#coalesce)、[IsPresent](functions-for-customizing-application-data.md#ispresent) などの関数を使用する空または null の値をチェックする式を定義し、空でないリテラルの値 (この場合は 000-000-0000 など) を渡します。 
  
     `IIF(IsNullOrEmpty([telephoneNumber]),"000-000-0000",[telephoneNumber])`

  * オプション 2: 関数 [IgnoreFlowIfNullOrEmpty](functions-for-customizing-application-data.md#ignoreflowifnullorempty) を使用して、SuccessFactors に送信されるペイロード内の空の属性または null 属性を削除します。 
  
     `IgnoreFlowIfNullOrEmpty([telephoneNumber])` 



## <a name="next-steps"></a>次のステップ

* [Azure AD と Workday の統合シナリオと Web サービス呼び出しについて](workday-integration-reference.md)
* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](check-status-user-account-provisioning.md)
