---
title: Azure portal を使用した Azure Stack Edge の順序付けに関する問題のトラブルシューティング
description: Azure Stack Edge の順序の問題をトラブルシューティングする方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 06/21/2021
ms.author: alkohli
ms.openlocfilehash: 46a7d0ed41b8f10874c19c8d70ddc5d8c5d7f197
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2021
ms.locfileid: "122608287"
---
# <a name="troubleshoot-your-azure-stack-edge-ordering-issues"></a>Azure Stack Edge の順序の問題をトラブルシューティングする

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure Stack Edge の順序の問題をトラブルシューティングする方法について説明します。

## <a name="unsupported-subscription-or-region"></a>サポートされていないサブスクリプションまたはリージョン

**エラーの説明:** Azure portal で、次のエラーが表示された場合:

*選択されたサブスクリプションまたはリージョンはサポートされていません。別のサブスクリプションまたはリージョンを選択してください。*

![サポートされていないサブスクリプションまたはリージョン](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-01.png)

**推奨されている解決方法:** [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/overview/sales-number/)、[クラウド ソリューション プロバイダー (CSP)](/partner-center/azure-plan-lp)、[Microsoft Azure スポンサープラン](https://azure.microsoft.com/offers/ms-azr-0036p/)など、サポートされているサブスクリプションを使用していることを確認してください。 従量課金制サブスクリプションはサポートされていません。 詳細については、[Azure Stack Edge リソースの前提条件](azure-stack-edge-deploy-prep.md#prerequisites)に関するページを参照してください。

Microsoft では、サブスクリプションの種類のアップグレードがケース バイ ケースで許可される可能性があります。 [Microsoft サポート](https://azure.microsoft.com/support/options/)までご連絡ください。お客様のニーズを理解し、これらの制限を適切に調整できるようにいたします。

## <a name="selected-subscription-type-not-supported"></a>選択されたサブスクリプションの種類はサポートされていません

**エラー:** EA、CSP、またはスポンサー サブスクリプションを所有しており、次のエラーが表示されます。

*選択されたサブスクリプションの種類はサポートされていません。 サポートされるサブスクリプションを使用していることを確認してください。 [詳細については、こちらを参照してください](azure-stack-edge-deploy-prep.md#prerequisites)。 サポートされているサブスクリプションの種類を使用する場合は、次を確認してください。
 
- クラシック ポータルを使用して注文するときに、`Microsoft.DataBoxEdge` プロバイダーが登録されている。
- Azure Edge Hardware Center (プレビュー) を使用して注文するときに、`Microsoft.EdgeOrder` プロバイダーが登録されている。
 
登録する方法については、「[リソース プロバイダーを登録する](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)」を参照してください。*

**推奨されている解決方法:** 次の手順に従って Azure Stack Edge リソース プロバイダーを登録します。

1. Azure portal で、 **[ホーム]**  >  **[サブスクリプション]** に移動します。

2. デバイスを注文するために使用するサブスクリプションを選択します。

3. **[リソース プロバイダー]** を選択し、**Microsoft.DataBoxEdge** を検索します。

    ![リソース プロバイダーの登録](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-02.png)

リソース プロバイダーを登録するための所有者または共同作成者のアクセス権がない場合は、次のエラーが表示されます。*次のリソース プロバイダーを登録するためのアクセス許可がサブスクリプション &lt;サブスクリプション名&gt; にありません:Microsoft.DataBoxEdge*

詳細については、「[リソース プロバイダーを登録する](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)」を参照してください。

## <a name="resource-provider-not-registered-for-subscription"></a>リソース プロバイダーがサブスクリプションに登録されていない

**エラー:** Azure portal で、Azure Stack Edge または Data Box Gateway を使用するサブスクリプションを選択すると、次のいずれかのエラーが表示されます。

*リソース プロバイダー Microsoft.DataBoxEdge はサブスクリプション &lt;サブスクリプション名&gt; に登録されておらず、サブスクリプション &lt;サブスクリプション名&gt; のリソース プロバイダーを登録するためのアクセス許可がありません*。

*リソース プロバイダー: Microsoft.EdgeOrder はサブスクリプション &lt;サブスクリプション名&gt; に登録されておらず、サブスクリプション &lt;サブスクリプション名&gt; のリソース プロバイダーを登録するためのアクセス許可がありません*。

**推奨されている解決方法:** サブスクリプションのアクセスを昇格させるか、またはリソース プロバイダーを登録するための所有者または共同作成者のアクセス権を持つだれかを見つけます。

## <a name="resource-disallowed-by-policy"></a>リソースがポリシーにより許可されない

**エラー:** Azure portal で、リソース プロバイダーを登録しようとすると、次のエラーが表示されます。

*リソース &lt;リソース名&gt; はポリシーにより許可されませんでした。(コード:RequestDisallowedByPolicy)。イニシアティブ: 一般に不要なリソースの種類を拒否します。ポリシー:許可されていないリソースの種類。*

**推奨される解決策:** このエラーは、既存の Azure Policy の割り当てによってリソースの作成がブロックされるために発生します。 Azure Policy の定義と割り当ては、Azure リソースの使用または作成中のコンプライアンスを確保するために組織のシステム管理者によって設定されます。 このようなポリシーの割り当てのいずれかが Azure Stack Edge リソースの作成をブロックしている場合は、システム管理者に連絡して Azure Policy の定義を編集してください。

## <a name="next-steps"></a>次の手順

* [Azure Stack Edge に関する問題をトラブルシューティングする](azure-stack-edge-gpu-troubleshoot.md)方法の詳細について学習します。
