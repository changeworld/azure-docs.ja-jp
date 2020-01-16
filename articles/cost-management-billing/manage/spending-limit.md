---
title: Azure の使用制限 | Microsoft Docs
description: この記事では、Azure の使用制限のしくみと削除方法を説明します。
author: bandersmsft
manager: amberb
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: banders
ms.openlocfilehash: ddb906aeb8d1c2b0d1f0c97fc12bd389da431646
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "75985027"
---
# <a name="azure-spending-limit"></a>Azure の使用制限

Azure の使用制限により、クレジット額を超過する支出が防止されます。 Azure 無料アカウント、または複数月にわたるクレジットが含まれるサブスクリプション タイプにサインアップする新規のお客様に対してはすべて、既定で使用制限が有効化されます。 使用制限はクレジットの金額と等価であり、変更できません。 たとえば Azure 無料アカウントにサインアップした場合、使用制限は 200 ドルであり、500 ドルに変更することはできません。 ただし、使用制限の削除はできます。 したがって選択肢は、制限なしとするか、またはクレジットと同額の制限を設けるかのどちらかとなります。 ほとんどの種類の支出は、これで防止されます。 使用制限は、コミットメント プランや従量課金制価格を使用したサブスクリプションでは利用できません。 [Azure のサブスクリプションの種類と使用制限の利用の可否を網羅した一覧](https://azure.microsoft.com/support/legal/offer-details/)をご覧ください。

## <a name="reaching-a-spending-limit"></a>使用制限への到達

使用量が、使用制限を使い果たす額に到達した場合、デプロイしたサービスは、請求の残りの期間、無効になります。

たとえば、Azure 無料アカウントに含まれているすべてのクレジットを使用した場合は、デプロイした Azure リソースは運用環境から削除され、Azure 仮想マシンは停止し、割り当てが解除されます。 ストレージ アカウントのデータは、読み取り専用として使用できます。

ご利用の種類のサブスクリプションに複数月のクレジットが含まれる場合、翌請求期間の開始時点でサブスクリプションは自動的に再び有効化されます。 この後、Azure リソースを再デプロイし、ストレージ アカウントおよびデータベースに完全にアクセスできるようになります。

使用制限に達した場合は、Azure により電子メール通知が送信されます。 [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) にサインインして、使用制限に到達したサブスクリプションに関する通知を確認します。

Azure 無料アカウントにサインアップしたユーザーが使用制限に達した場合、[従量課金制](upgrade-azure-subscription.md)価格にアップグレードすることで、使用制限を解除し、サブスクリプションを自動的に再有効化できます。

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-azure-portal"></a>Azure portal での使用制限の解除

Azure サブスクリプションに関連付けられた有効な支払方法がある限り、使用制限はいつでも削除できます。 複数月にわたるクレジットを含むサブスクリプション タイプ (Visual Studio Enterprise、Visual Studio Professional など) の場合、翌請求期間の開始時に使用制限を有効化することもできます。

使用制限を削除するには、次の手順に従ってください。

1. [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) にサインインする
1. サブスクリプションを選択します。 サブスクリプションの使用制限に達した場合は無効になります。
1. ページの上部から **[使用制限の削除]** を選択します。
1. 適切なオプションを選択します。

![使用制限を削除するためのオプションを選択](./media/spending-limit/remove-spending-limit01.png)

| オプション | 結果 |
| --- | --- |
| 使用制限を無期限に削除 | 次の請求期間の開始時に使用制限を自動的に有効にすることなく、使用制限を削除します。 |
| 現在の請求期間の使用制限を削除 | 使用制限を削除し、次の請求期間の開始時に使用制限を自動的に有効に戻します。 |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>使用制限を削除しようと考えるときの理由は何ですか

使用制限により、特定のサードパーティおよび Microsoft サービスのデプロイまたは使用ができなくなる場合があります。 サブスクリプションの使用制限を削除する必要がある状況を次に示します。

-  Oracle のようなファースト パーティ イメージや Azure DevOps Services などのサービスのデプロイを計画しています。 この状況は、ほぼ即座に使用制限に到達し、サブスクリプションが無効化される原因となります。
- 中断したくないサービスがあります。 使用制限に達した場合、デプロイ済みの Azure リソースは運用環境から削除され、Azure 仮想マシンは停止し、割り当てが解除されます。 中断したくないサービスがある場合は、使用制限を解除してください。
- 仮想 IP アドレスなど、中断することのできない設定があるサービスおよびリソースを利用しています。 これらの設定は、使用制限に到達してサービスおよびリソースの割り当てが解除されると、失われます。

## <a name="turn-on-the-spending-limit-after-removing"></a>削除後に使用制限を有効にする

この機能を利用できるのは、複数月にわたるクレジットを含む種類のサブスクリプションで、使用制限が無期限に解除されている場合だけです。 この機能を使用すると、次の請求期間の開始時に使用制限を自動的に有効にできます。

1. [アカウント センター](https://account.windowsazure.com/Subscriptions)にサインインします。
1. 使用制限オプションを変更するには、黄色のバナーをクリックします。
1. **[次の請求期間に使用制限を有効にする \<請求期間の開始日\>]** を選びます。

## <a name="custom-spending-limit"></a>カスタムの使用制限

カスタムの使用制限は使用できません。

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>すべての料金が使用制限で防止されるわけではない

[Azure Marketplace で公開されている一部の外部サービス](../understand/understand-azure-marketplace-charges.md)には、サブスクリプション クレジットを使用できません。また、使用制限が設定されている場合でも、別に料金を請求される場合があります。 Visual Studio のライセンス、Azure Active Directory Premium、サポート プランとほとんどのサード パーティ ブランド サービスなどがその例として挙げられます。 新しい外部サービスをプロビジョニングすると、警告が表示され、サービス料が別に請求されることをお知らせします。

![Marketplace 購入警告](./media/spending-limit/marketplace-warning01.png)

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート要求を作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ
- [従量課金制](upgrade-azure-subscription.md)料金のプランにアップグレードする。
