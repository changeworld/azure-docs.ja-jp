---
title: Azure サブスクリプションの請求書による支払い | Microsoft Docs
description: Azure サブスクリプションを請求書で支払う方法について説明します
services: ''
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: banders
ms.openlocfilehash: 7caa7df3817c51d63dfa2838e4223d3fba88e529
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827356"
---
# <a name="pay-azure-subscription-by-invoice"></a>請求書によって Azure サブスクリプションの支払いを行う

請求書による支払いに切り替えた場合、請求日から 30 日以内に請求金額を支払うことになります。 Azure サブスクリプションの支払いを請求書で行う対象となるには、Azure サポートに要求を送信します。 要求が承認されると、[Azure portal](https://portal.azure.com) でサブスクリプションから請求書支払いに切り替えられます。

> [!IMPORTANT]
> * 請求書支払いはビジネス アカウントのみでご利用いただけます。
> * 請求書支払いに切り替える前に、すべての未払いの料金を支払う必要があります。
> * 支払請求書に切り替えた後にクレジット カードまたはデビット カードによる支払いに戻すことはできません。

## <a name="request-to-pay-by-invoice"></a>請求書による支払いを要求する

1. [Azure Portal](https://portal.azure.com/) にサインインします。 **[ヘルプとサポート]**  >  **[新しいサポート要求]** の順に選択します。

    ![[ヘルプとサポート] ボタン](./media/billing-how-to-pay-by-invoice/help-and-support.png)

2. [問題の種類] で **[課金]** を選択し、請求書で支払うサブスクリプションを選択します。サポート プランを選択し、 **[次へ]** を選択します。

3. **[問題の種類]** ボックスで **[Pay by Invoice (請求書による支払い)]** オプションを選択します。

4. **[詳細]** ボックスに次の情報を入力し、 **[次へ]** を選択します。

         New or existing customer:
         If existing, current payment method:
         Order ID (requesting for invoice option):
         Account Admins Live ID (or Org ID) (should be company domain):
         Commerce Account ID:
         Company Name (as registered under VAT or Government Website):
         Company Address (as registered under VAT or Government Website):
         Company Website:
         Country:
         TAX ID/ VAT ID:
         Company Established on (Year):
         Any prior business with Microsoft:
         Contact Name:
         Contact Phone:
         Contact Email:
         Justification on why you prefer Invoice option over credit card:

        For cores increase, provide the following additional information:

         (Old quota) Existing Cores:
         (New quota) Requested cores:
         Specific region & series of Subscription:

    - **[会社名]** と **[会社の住所]** は、Azure アカウントに指定した情報と一致させる必要があります。  情報を表示または更新するには、「[Azure アカウントのプロファイル情報を変更する](billing-how-to-change-azure-account-profile.md)」を参照してください。
    - 与信限度額を承認する前に、Azure portal で請求連絡先を追加する必要があります。 連絡先の詳細を会社の買掛金または財務部門に関連付ける必要があります。 請求連絡先を更新するには、[Azure アカウント センター](https://account.azure.com/Profile)に移動します。

5. 連絡先情報と優先連絡方法を確認し、 **[作成]** をクリックします。

必要とするクレジット総額によっては与信審査が必要になることがあります。この場合は、カスタマー サポートから与信審査申込書をお送りします。

## <a name="switch-to-invoice-pay"></a>請求書支払いに切り替える

請求書による支払いが承認されたら、[Azure portal](https://portal.azure.com) で Azure サブスクリプションから請求書支払い方法に切り替えられます。

1. [Azure Portal](https://portal.azure.com) にアカウント管理者としてサインインします。
1. **[コストの管理と請求]** で検索します。

    ![検索を示すスクリーンショット](./media/billing-how-to-pay-by-invoice/search.png)

1. 請求書支払いに切り替えるサブスクリプションを選択します。
1. **[支払い方法]** を選択します。
1. コマンド バーで、 **[請求書による支払い]** ボタンをクリックします。

    ![[請求書による支払い] ボタンを示したスクリーン ショット](./media/billing-how-to-pay-by-invoice/pay-by-invoice.png)

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。
