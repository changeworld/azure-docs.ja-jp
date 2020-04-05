---
title: Azure サブスクリプションの請求書による支払い
description: Azure サブスクリプションを請求書で支払う方法について説明します。
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 12ad1f09c988171b0d6212d79ca61d93acf92d80
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "77199604"
---
# <a name="pay-for-your-azure-subscription-by-invoice"></a>Azure サブスクリプションの請求書による支払い

請求書による支払いに切り替えた場合、請求日から 30 日以内に小切手/電信送金で請求金額を支払うことになります。 Azure サブスクリプションの支払いを請求書で行う対象となるには、Azure サポートに要求を送信します。 要求が承認されると、[Azure portal](https://portal.azure.com) で請求書支払い (小切手/電信送金) に切り替えられます。

> [!IMPORTANT]
> * 請求書支払い (小切手/電信送金) は、会社を代表して Azure を使用しているお客様にのみ提供されます。
> * 請求書支払いに切り替える前に、すべての未払いの料金を支払います。
> * 現在、中国のグローバル Azure では、請求書支払いはサポートされていません。

## <a name="request-to-pay-by-invoice"></a>請求書による支払いを要求する

1. [Azure portal](https://portal.azure.com) にアクセスして、サポート リクエストを発行します。 **[ヘルプとサポート]** を検索して選択します。

    ![Microsoft Azure portal で [ヘルプとサポート] を検索する](./media/pay-by-invoice/search-for-help-and-support.png)

2. **[新しいサポート リクエスト]** を選択します。

    ![[新しいサポート リクエスト] リンク、[ヘルプとサポート] 画面、Microsoft Azure portal](./media/pay-by-invoice/help-and-support.png)

2. **[問題の種類]** で **[課金]** を選択します。 *[問題の種類]* は、サポート要求のカテゴリです。 請求書で支払うサブスクリプションを選択します。サポート プランを選択し、 **[次へ]** を選択します。

3. **[お支払]** を **[問題の種類]** として選択します。 *[問題の種類]* は、サポート要求のサブカテゴリです。

4. **[Switch to Pay by Invoice]\(請求書による支払いに切り替える\)** を **[問題のサブタイプ]** として選択します。

5. **[詳細]** ボックスに次の情報を入力し、 **[次へ]** を選択します。

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

    - **[会社名]** と **[会社の住所]** は、Azure アカウントに指定した情報と一致させる必要があります。 情報を表示または更新するには、「[Azure アカウントのプロファイル情報を変更する](change-azure-account-profile.md)」を参照してください。
    - 与信限度額を承認する前に、Azure portal で請求連絡先情報を追加します。 連絡先の詳細を会社の買掛金または財務部門に関連付ける必要があります。 請求連絡先情報を更新するには、[Azure アカウント センター](https://account.azure.com/Profile)に移動します。

6. 連絡先情報と優先連絡方法を確認し、 **[作成]** を選択します。

必要とするクレジット総額によっては与信審査が必要になることがあります。この場合は、カスタマー サポートから与信審査申込書をお送りします。

## <a name="switch-to-invoice-pay-checkwire-transfer"></a>請求書支払い (小切手/電信送金) に切り替える

請求書による支払いが承認されると、Azure portal で請求書支払い (小切手/電信送金) に切り替えることができます。

Microsoft Online Services Program アカウントの場合は、Azure サブスクリプションを小切手/電信送金に切り替えることができます。 Microsoft 顧客契約を結んでいる場合は、課金プロファイルを小切手/電信送金に切り替えることができます。 [アカウントの種類を確認する方法をご覧ください](#check-access-to-a-microsoft-customer-agreement)。

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>Azure サブスクリプションを小切手/電信送金に切り替える

Azure サブスクリプションを請求書支払い (小切手/電信送金) に切り替えるには、次の手順に従います。 *請求書支払い (小切手/電信送金) に切り替えた後にクレジット カードに戻すことはできません*。

1. [Azure portal](https://portal.azure.com) にアクセスし、アカウント管理者としてサインインします。 **[コストの管理と請求]** を検索して選択します。

    ![Microsoft Azure portal で [コストの管理と請求] を検索する](./media/pay-by-invoice/search.png)

1. 請求書支払いに切り替えるサブスクリプションを選択します。
1. **[支払い方法]** を選択します。
1. コマンド バーで、 **[請求書による支払い]** ボタンを選択します。

    ![[請求書による支払い] ボタン、[支払い方法]、Microsoft Azure portal](./media/pay-by-invoice/pay-by-invoice.png)

### <a name="switch-billing-profile-to-checkwire-transfer"></a>課金プロファイルを小切手/電信送金に切り替える

課金プロファイルを小切手/電信送金に切り替えるには、次の手順に従います。 課金プロファイルの既定の支払い方法を変更できるのは、Azure にサインアップしたユーザーのみです。

1. [Azure portal](https://portal.azure.com) にアクセスして、課金情報を表示します。 **[コストの管理と請求]** を検索して選択します。
1. メニューから **[課金プロファイル]** を選択します。

    ![[課金プロファイル] メニュー項目、[コストの管理と請求]、Microsoft Azure portal](./media/pay-by-invoice/billing-profile.png)

1. 課金プロファイルを選択します。
1. **[課金プロファイル]** メニューで **[支払い方法]** を選択します。

   ![[支払い方法] メニュー項目、[課金プロファイル]、[コスト管理]、Microsoft Azure portal](./media/pay-by-invoice/billing-profile-payment-methods.png)

1. 小切手または電信送金で支払う資格があることを示すバナーを選択します。

    ![小切手または電信送金に切り替えるためのバナー、[支払い方法]、Microsoft Azure portal](./media/pay-by-invoice/customer-led-switch-to-invoice.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 顧客契約にアクセスできるかどうかを確認する
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート要求を作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

- 必要に応じて、[Azure アカウント センター](https://account.azure.com/Profile)で請求先連絡先情報を更新します。
