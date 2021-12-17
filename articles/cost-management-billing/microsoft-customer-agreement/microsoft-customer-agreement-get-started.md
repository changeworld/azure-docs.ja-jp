---
title: Microsoft 顧客契約への同意後に重要となる次のステップ - Azure
description: この記事では、これから新しい Microsoft 顧客契約の下で Azure の課金とサブスクリプションを管理する方のために参考となる情報を提供しています。
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.subservice: microsoft-customer-agreement
ms.topic: conceptual
ms.date: 06/14/2021
ms.author: banders
ms.reviewer: baolcsva
ms.openlocfilehash: a4e5d3a46bf283f8c8f3ad7b5ad303b499dcd2ff
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724461"
---
# <a name="key-next-steps-after-accepting-your-microsoft-customer-agreement"></a>Microsoft 顧客契約への同意後に重要となる次のステップ

この記事では、これから新しい Microsoft 顧客契約の下で Azure の課金とサブスクリプションを管理する方のために参考となる情報を提供しています。

この契約には次のような利点があります。

- Azure の使用に対する支払い方法を柔軟に選択できる。
- 無料のツールでコストを把握したり、最適化したりしやすい。
- Azure.com での Azure 購入の管理を一元化できる。

## <a name="start-building-your-solutions-in-azure"></a>Azure でソリューションの構築を開始する

既存のサブスクリプションを Microsoft 顧客契約の課金プロファイルに移動しても、サービスは変更されず、サービスのダウンタイムも発生しません。 新しいお客様の場合は、Azure によって既定のサブスクリプションが自動的に作成されます。

- [既存の従量課金制サブスクリプションを移動する](../manage/mca-request-billing-ownership.md)。 課金所有権の譲渡を使用して、ご利用のサブスクリプションを新しい MCA 課金アカウントにリンクすることができます。
- [既存の EA サブスクリプションを移動する](../manage/mca-setup-account.md)。
- (まだ Azure サブスクリプションをお持ちでない場合) [追加の Azure サブスクリプションを作成する](../manage/create-subscription.md)。

サブスクリプションの移動後、管理下のユーザーに関して、サブスクリプションへのアクセスに変更が生じることはありません。 そのサブスクリプションで発生したすべての使用は、新しい契約の下で請求書がルーティングされます。
Azure サービスの利用を開始すると、毎月 5 日に Microsoft 顧客契約の下で新しい請求書が生成されます。お客様の既定の支払い方法は電信送金です。 [遅延が生じないように支払い方法を設定する方法について参照してください](../understand/pay-bill.md#wire-bank-details)。

## <a name="how-billing-works-under-the-agreement"></a>この契約における請求のしくみ

お客様またはその所属組織が Microsoft 顧客契約に署名すると、課金アカウントが自動的に作成されます。 Microsoft 顧客契約 (課金アカウント) を使用して、コストの追跡と課金の管理を行います。 既定では、Microsoft 顧客契約に同意したユーザーが課金アカウントの所有者になります。 そのユーザーには、アカウントの課金情報を管理するためのアクセス許可が与えられます。 このユーザーは、自分以外にも課金アカウントを表示したり管理したりするためのアクセス許可を持つユーザーを追加することができます。

- [Microsoft Azure 課金アカウントの概要](../understand/mca-overview.md)。
- [コストの整理](https://www.youtube.com/watch?v=7RxTfShGHwU)と[ニーズに応じた請求のカスタマイズ](../manage/mca-section-invoice.md)。
- [課金アカウントにアクセスできる](https://www.youtube.com/watch?v=9sqglBlKkho)ユーザーを確認し、[Azure での管理者ロールの動作](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks)について理解してください。

## <a name="update-your-po-and-tax-id-number"></a>PO 番号と税 ID 番号を更新する

[課金プロファイルで PO 番号を更新](../manage/change-azure-account-profile.md#update-a-po-number)し、サブスクリプションを移動した後、[税 ID を更新](../manage/change-azure-account-profile.md#update-your-tax-id)するようにしてください。 この税 ID は、免税の計算に使用され、請求書に表示されます。 [課金アカウントの設定を更新する方法の詳細](/microsoft-store/update-microsoft-store-for-business-account-settings)をご覧ください。


## <a name="confirm-payment-details"></a>支払いの詳細を確認する

従量課金制または Enterprise Agreement から Microsoft 顧客契約に移動すると、支払い方法が変わります。 次の表は、以前の支払い方法と新しい支払い方法を比較したものです。

| MCA の購入方法 | 以前の支払い方法 - クレジット カード | 以前の支払い方法 - 請求書 | MCA の下での新しい支払い方法 - クレジット カード | MCA の下での新しい支払い方法 - 請求書 |
| --- | --- | --- |--- |--- |
| Microsoft 担当者から |  | ✔  |  ✔ <sup>4</sup> | ✔ <sup>2</sup> |
| Azure Web サイト | ✔ | ✔ <sup>1</sup> | ✔ | ✔ <sup>3</sup> |

<sup>1</sup> リクエストによる。

<sup>2</sup> MCA でも引き続き支払いは請求書または電信送金で行いますが、送金先は別の銀行口座とする必要があります。 支払いの送金先については、「[請求金額を支払う](../understand/pay-bill.md#wire-bank-details)」を参照してください (一覧から国を選択してください)。

<sup>3</sup> 詳細については、「[Azure サブスクリプションの請求書による支払い](../manage/pay-by-invoice.md)」を参照してください。

<sup>4</sup> 詳細については、[Microsoft Azure の請求書の支払い](../understand/pay-bill.md#pay-now-in-the-azure-portal)に関するセクションを参照してください。

## <a name="complete-outstanding-payments"></a>未処理の支払いを完了する

以前使用していた[従量課金制](../understand/download-azure-invoice.md)または [EA](../manage/ea-portal-enrollment-invoices.md) 契約のサブスクリプションの請求書について、未処理の支払いがあれば、それらを完了する必要があります。 詳細については、[Azure における Microsoft 顧客契約の請求書の理解](../understand/mca-understand-your-invoice.md#billing-period)に関するセクションを参照してください。

## <a name="cancel-support-plan"></a>サポート プランをキャンセルする

[以前のサポート プランをキャンセル](../manage/mca-request-billing-ownership.md?toc=/azure/cost-management-billing/microsoft-customer-agreement/toc.json#cancel-a-prior-support-plan)する方法について説明します。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

- [請求書上の料金について確認する](https://www.youtube.com/watch?v=e2LGZZ7GubA)
- [ステップバイステップの請求書チュートリアルを実行する](../understand/review-customer-agreement-bill.md)
