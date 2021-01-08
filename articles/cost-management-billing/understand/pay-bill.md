---
title: Microsoft Azure の請求書の支払いを行う
description: Azure portal での請求書の支払い方法について説明します。 ポータルで支払いを行うには、課金プロファイルの所有者、共同作成者、または請求書管理者である必要があります。
keywords: 請求, 支払い期限を過ぎている, 残高, 今支払う,
author: banders
ms.reviewer: judupont
tags: billing, past due, pay now, bill, invoice, pay
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 12/17/2020
ms.author: banders
ms.openlocfilehash: 2983f870d6ab20dac4da487ba03ebf84905f8dcc
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680313"
---
# <a name="how-to-pay-your-bill-for-microsoft-azure"></a>Microsoft Azure の請求書の支払い方法

この記事は、Microsoft 顧客契約 (MCA) をご利用のお客様を対象としています。

[Microsoft Customer Agreement にアクセスできるかどうかを確認](#check-access-to-a-microsoft-customer-agreement)してください。

Azure の請求書には 2 とおりの支払い方法があります。 課金プロファイルの既定の支払い方法を使用する方法と一括払い ( **[今支払う]** ) を使用する方法です。

Azure クレジットがある場合は、請求期間ごとに請求書に自動的に適用されます。

## <a name="pay-by-default-payment-method"></a>既定の支払い方法を使用する

課金プロファイルの既定の支払い方法として、クレジット カード、デビット カード、小切手 (または電信送金) のいずれかを指定できます。

### <a name="credit-or-debit-card"></a>クレジット カードまたはデビット カード

課金プロファイルの既定の支払い方法がクレジット カードまたはデビット カードである場合、請求期間ごとに自動的に課金されます。

クレジット カードまたはデビット カードの自動請求がなんらかの理由によって拒否された場合は、Azure portal から **[今支払う]** を使用し、クレジット カードまたはデビット カードで一括払いすることができます。

既定の支払い方法を小切手または電信送金に変更したい場合は、[請求書による支払い方法](../manage/pay-by-invoice.md)に関するページを参照してください。

### <a name="check-or-wire-transfer"></a>小切手または電信送金

課金プロファイルの既定の支払い方法が小切手または電信送金である場合は、請求書の PDF ファイルに記載の支払い手順に従ってください。

請求書の金額がご利用の通貨のしきい値を下回っている場合は、Azure portal から **[今支払う]** を使用し、クレジット カードまたはデビット カードで一括払いすることができます。 請求書の金額がしきい値を超えている場合は、クレジット カードやデビット カードを使用して請求書の支払いを行うことはできません。 該当する通貨のしきい値の金額は、Azure portal で **[今支払う]** を選択すると表示されます。

## <a name="pay-now-in-the-azure-portal"></a>Azure portal で今支払う

Azure portal で請求書の支払いを行うには、適切な [MCA アクセス許可](../manage/understand-mca-roles.md)があるか、課金アカウントの管理者である必要があります。MCA アカウントにサインアップしたユーザー本人が、課金アカウントの管理者となります。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. **[コストの管理と請求]** で検索します。
1. 左側のメニューの **[課金]** から **[請求書]** を選択します。
1. 請求書の支払い期限来ている場合や支払い期限が過ぎた場合、その請求書に青色の **[今支払う]** リンクが表示されます。 **[今支払う]** を選択します。
1. [今支払う] ウィンドウで **[支払い方法の選択]** を選択して、既存のクレジット カードを選択するか、新しいカードを追加します。
1. 支払い方法を選択したら、 **[今支払う]** を選択します。

24 時間以内に、請求書の状態が "*支払い済み*" と表示されます。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 顧客契約にアクセスできるかどうかを確認する
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="next-steps"></a>次の手順

- 小切手または電信送金による支払いの対象となるには、[請求書による支払い方法](../manage/pay-by-invoice.md)に関する記事を参照してください。