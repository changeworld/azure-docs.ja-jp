---
title: クレジット制限の削除と Azure Dev/Test オファーの変更
description: クレジット制限を削除し、Azure Dev/Test オファーを変更する方法。 従量課金制から別のオファーに切り替えます。
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/04/2021
ms.custom: devtestoffer
ms.openlocfilehash: ddeff5d88aa69af7d4958bddb90ad4add61170b4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092214"
---
# <a name="changing-azure-devtest-offers--remove-limits"></a>Azure DevTest オファーを切り替えて制限を削除する

現在、お持ち込み料金のサブスクリプションをお持ちのお客様は、Azure サブスクリプションを別のプランに切り替えられます。 オファーを変更する理由は、組織の構造に固有の場合があります。 ただし、サブスクリプション オファーを変更したり、クレジット制限を削除したりする一般的な理由を次に示します。  

- クレジット制限を使用すると、クレジット カードに課金することなく、実験やテストを実行できます。 お金はサービスを実行するとすぐに使い尽くしてしまうことがあります。 クレジット制限により、サブスクリプションの周囲にガード レールを設けられます。 必要なアーキテクチャを作成し、必要な限り実行しつつ、使いすぎないようにできます。  
- 他の組織や組織内の変更を操作するには、オファーの種類を変更する必要がある場合があります。  
- 現在のオファーの種類を引き続き変更する必要がある Azure サブスクリプションのニーズが変わる場合があります。 プロジェクトの作業方法を変更する必要がある場合があります。 この場合は、必要なサービスを実行するために、クレジット制限構造から従量課金制構造に切り替える必要がある場合があります。  
- 実行する重要なプロジェクトが作成され、より多くの時間が必要になります。 $150 または $50 の制限を少し超える必要がある場合は、制限を削除できます。  
- 現在のオファーの有効期限が切れています。  

サブスクリプション オファーの変更は、特定のオファーの種類に対して簡単に行います。 [Azure オファーの切り替え](../../cost-management-billing/manage/switch-azure-offer.md)に関する記事に従うか、サポートにお問い合わせください。  

## <a name="understand-spending-limits-and-cost-management"></a>使用制限とコスト管理について

Azure の月間クレジット サブスクリプションの[コスト管理](../../cost-management-billing/costs/cost-mgt-best-practices.md)には、クラウドへの投資を最大化するために支出を計画、分析、削減するツールが用意されています。 これらのツールを使用すると、使用制限を削除して追加することができます。  

Azure の使用制限により、過剰な支出が防止されます。 制限は変更できませんが、使用制限は削除できます。 クレジットの金額に等しい制限または制限はありません。  

Azure サブスクリプションに関連付けられた有効な支払方法がある限り、[使用制限](../../cost-management-billing/manage/spending-limit.md)はいつでも削除できます。  

その他の Cost Management ドキュメント:  

* [Cost Management のデータを理解する](../../cost-management-billing/costs/understand-cost-mgt-data.md)  
* [Azure Cost Management でクラウド投資を最適化する](../../cost-management-billing/costs/cost-mgt-best-practices.md)  

## <a name="payment-methods-and-overages"></a>支払い方法と超過分

制限を削除したり、オファーを変更したりするときに、[新しいクレジットを更新または追加](../../cost-management-billing/manage/change-credit-card.md)する必要がある場合があります。 クレジットが使い尽くされるまで、クレジット カードを超過して使用することはできません。  

Azure portal では、既定の支払い方法から新しいクレジット カードへの変更と、クレジット カードの詳細の更新を行うことができます。 これらの変更を行うには、[アカウント管理者](../../cost-management-billing/understand/subscription-transfer.md#whoisaa)であるか、適切な [MCA アクセス許可](../../cost-management-billing/manage/understand-mca-roles.md)を持っている必要があります。  

クレジット カードを削除する場合は、「[Azure の課金の支払い方法を削除する](../../cost-management-billing/manage/delete-azure-payment-method.md)」を参照してください  

支払い方法を更新して、クレジット カードではなく、小切手や電信送金にすることもできます。 最初に、請求書で支払う承認が必要です。 承認を得るには、「[Azure サブスクリプションの請求書による支払い](../../cost-management-billing/manage/pay-by-invoice.md)」をご覧ください。  
