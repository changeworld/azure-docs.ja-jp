---
title: Azure サブスクリプションの状態
description: この記事では、Azure サブスクリプションのさまざまな状態について説明します。
keywords: Azure サブスクリプションの状態
author: anuragdalmia
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: andalmia
ms.openlocfilehash: 29af3d064b481548cdb9b9518e9735eb34aaf034
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "78270232"
---
# <a name="azure-subscription-states"></a>Azure サブスクリプションの状態

この記事では、Azure サブスクリプションのさまざまな状態について説明します。 これらの状態は、Azure portal のサブスクリプション領域に **[状態]** として表示されます。

| サブスクリプションの状態 | 説明 |
|-------------| ----------------|
| **アクティブ** | Azure サブスクリプションがアクティブです。 このサブスクリプションを使用して、新しいリソースをデプロイしたり既存のリソースを管理したりできます。|
| **削除済み** | ご利用の Azure サブスクリプションは、基になるすべてのリソースおよびデータと共に削除されました。 |
| **Disabled** | ご利用の Azure サブスクリプションは無効化され、Azure リソースの作成や管理に使用することはできなくなりました。 この状態では、仮想マシンの割り当てが解除されて、一時 IP アドレスが解放され、ストレージが読み取り専用になって、他のサービスが無効化されます。 サブスクリプションは、クレジットの有効期限が切れている、使用制限に達している、 請求の支払期限が過ぎている、 クレジット カードの限度額を超えている、 明示的に無効化 (キャンセル) された などの理由で無効にされることがあります。 サブスクリプションは、その種類によって 1 日から 90 日、無効状態で維持されます。 その期間が経過した後、完全に削除されます。 詳細については、「[無効な Azure サブスクリプションを再度有効にする](subscription-disabled.md)」を参照してください。 |
| **Expired** | キャンセルされた Azure サブスクリプションは期限切れとなります。 期限切れのサブスクリプションは再度有効にすることができます。 詳細については、「[無効な Azure サブスクリプションを再度有効にする](subscription-disabled.md)」を参照してください。|
| **期限経過** | Azure サブスクリプションの支払いが未払いのまま保留状態になっています。 サブスクリプションは引き続きアクティブ状態ですが、支払い義務を怠ると、サブスクリプションが無効化されることがあります。 詳細については、「[支払期限を過ぎた Azure サブスクリプション未払い額の支払い](resolve-past-due-balance.md)」を参照してください。 |
