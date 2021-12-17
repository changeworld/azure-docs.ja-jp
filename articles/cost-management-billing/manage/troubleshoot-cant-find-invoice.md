---
title: Azure portal で請求書が表示されないことをトラブルシューティングする
description: Azure portal で請求書を表示しようとする際の問題を解決します。
services: cost-management-billing
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 06/25/2021
ms.author: banders
ms.openlocfilehash: 6f90d077b9cb7e1836170084c683e47cdafc480e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131464682"
---
# <a name="troubleshoot-issues-while-trying-to-view-invoice-in-the-azure-portal"></a>Azure portal で請求書を表示しようとする際の問題をトラブルシューティングする

Azure portal で請求書を表示しようとして、問題が発生することがあります。 この短いガイドでは一般的な問題について説明します。
 
## <a name="common-issues-and-solutions"></a>一般的な問題と解決方法

#### <a name="you-see-the-message-we-cant-display-the-invoices-for-your-subscription-this-typically-happens-when-you-sign-in-with-an-email-which-doesnt-have-access-to-view-invoices-check-youve-signed-in-with-the-correct-email-address-if-you-are-still-seeing-the-error-see-why-you-might-not-see-an-invoice"></a><a name="subnotfound"></a> 次のメッセージが表示される: "サブスクリプションの請求書を表示できません。 これは通常、請求書を表示するためのアクセス権がないメール アドレスを使用してサインインしたときに発生します。 正しいメール アドレスを使用してサインインしたことを確認してください。 引き続きエラーが表示される場合は、「請求書が表示されない理由」を参照してください。"

これは、サインインに使用した ID にサブスクリプションへのアクセス権がない場合に発生します。

この問題を解決するには、次のいずれかのオプションを試してください。 

**正しいメール アドレスを使用してサインインしていることを確認する:**

サブスクリプションのアカウント管理者ロールを持つメール アドレスのみが請求書を表示できます。 正しいメール アドレスを使用してサインインしていることを確認します。 メール アドレスは、請求書が生成されたときに受信した電子メールに表示されます。  

  ![請求書の電子メールを示すスクリーンショット](./media/troubleshoot-cant-find-invoice/invoice-email.png)

**正しいアカウントを使用してサインインしていることを確認する:**

お客様によっては、2 つのアカウント (職場または学校アカウントと個人用アカウント) に同じメール アドレスを使用している場合があります。 一般に、請求書を表示するアクセス許可があるのは、そのどちらか一方のアカウントのみです。 メール アドレスに対して 2 つのアカウントがある場合があります。 アクセス許可がないアカウントでサインインした場合、請求書は表示されません。 複数のアカウントがあるかどうかを確認し、別のアカウントを使用するには、次の手順に従います。

1. プライベートまたはシークレット ウィンドウで [Azure portal](https://portal.azure.com) にサインインします。
1. 同じメール アドレスのアカウントが複数ある場合は、 **[職場または学校アカウント]** または **[個人用アカウント]** のいずれかを選択するように求められます。 いずれかのアカウントを選択してから、[こちらの指示に従って請求書を表示](../understand/download-azure-invoice.md#download-your-mosp-azure-subscription-invoice)します。  

    ![アカウントの選択を示すスクリーンショット](./media/troubleshoot-cant-find-invoice/two-accounts.png)

1. なおも Azure portal で請求書を表示できない場合は、他のアカウントを試してください。

**正しい Azure Active Directory (AAD) テナントにサインインしていることを確認:**

課金アカウントおよびサブスクリプションは AAD テナントに関連付けられます。 間違ったテナントにサインインした場合、ご利用のサブスクリプションの請求書は表示されません。 Azure portal でテナントを切り替えるには、次の手順を試してください。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ページの右上で自分のメール アドレスを選択します。
1. [ディレクトリの切り替え] を選択します。  

    ![[ディレクトリの切り替え] の選択を示すスクリーンショット](./media/troubleshoot-cant-find-invoice/select-switch-tenant.png)

1. [すべてのディレクトリ] セクションからテナントを選択します。 [すべてのディレクトリ] セクションが表示されない場合、複数のテナントへのアクセス権がありません。  

    ![別のディレクトリの選択を示すスクリーンショット](./media/troubleshoot-cant-find-invoice/select-another-tenant.png)

#### <a name="you-couldnt-find-the-invoice-that-you-see-on-your-credit-card-statement"></a><a name="cantsearchinvoice"></a>クレジット カード明細書に表示される請求が見つからない

クレジット カードで **Microsoft Gxxxxxxxxx** に対する請求があります。 他のすべての請求書はポータルで見つかりますが、Gxxxxxxxxx は見つかりません。 これは、請求書が別のサブスクリプションまたは課金プロファイルに属している場合に発生します。 次の手順に従って、請求書を表示します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. Azure portal の検索バーで請求書番号を検索します。
1. **[View your invoice]\(請求書の表示\)** を選択します。  

    ![請求書の検索を示すスクリーンショット](./media/troubleshoot-cant-find-invoice/search-invoice.png)

## <a name="contact-us-for-help"></a>お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。

## <a name="next-steps"></a>次のステップ

- [Azure の請求書を表示してダウンロードする](../understand/download-azure-invoice.md)
- [Azure の使用量と料金の表示とダウンロード](../understand/download-azure-daily-usage.md)
- [Azure portal の "サブスクリプションが見つかりません" サインイン エラー](no-subscriptions-found.md)