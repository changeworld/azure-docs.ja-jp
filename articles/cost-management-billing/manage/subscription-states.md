---
title: Azure サブスクリプションの状態
description: Azure サブスクリプションのさまざまな状態について説明します。
keywords: Azure サブスクリプションの状態
author: anuragdalmia
ms.reviewer: andalmia
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: andalmia
ms.openlocfilehash: df8a60c0249eb51168e1a67cdd67116813312626
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200539"
---
# <a name="azure-subscription-states"></a>Azure サブスクリプションの状態
この記事では、Azure サブスクリプションのさまざまな状態について説明します。 これらの状態は、サブスクリプションのブレードに [状態] として表示されます。

| サブスクリプションの状態 | 説明 |
|-------------| ----------------|
| **アクティブ** | Azure サブスクリプションがアクティブです。 このサブスクリプションを使用して、新しいリソースをデプロイしたり既存のリソースを管理したりできます。|
| **期限経過** | Azure サブスクリプションの支払いが未払いのまま保留状態になっています。 サブスクリプションは引き続きアクティブ状態ですが、支払い義務を怠ると、サブスクリプションが無効化されることがあります。 「[支払期限を過ぎた Azure サブスクリプション未払い額の支払い](https://docs.microsoft.com/azure/billing/billing-azure-subscription-past-due-balance)」を参照してください。 |
| **Disabled** | ご利用の Azure サブスクリプションは無効化され、Azure リソースの作成や管理に使用することはできなくなりました。 この状態では、仮想マシンの割り当てが解除されて、一時 IP アドレスが解放され、ストレージが読み取り専用になって、他のサービスが無効化されます。 サブスクリプションは、クレジットの有効期限が切れている、使用制限に達している、請求の支払期限が過ぎている、クレジット カードの限度額を超えている、明示的に無効化 (キャンセル) されたなどの理由で無効にされることがあります。 サブスクリプションの種類と無効の理由によっては、無効状態で 1 日から 90 日が経過した後、サブスクリプションが完全に削除されることがあります。 「[無効な Azure サブスクリプションを再度有効にする](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)」を参照してください。|
| **削除済み** | ご利用の Azure サブスクリプションは、基になるすべてのリソースおよびデータと共に削除されました。 |
