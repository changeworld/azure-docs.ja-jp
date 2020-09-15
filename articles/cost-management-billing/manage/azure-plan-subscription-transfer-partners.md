---
title: Azure サブスクリプションを 1 つのパートナーから別のパートナーに譲渡する (プレビュー)
description: この記事は、Azure サブスクリプションの課金所有権を譲渡する前と後に知っておくべきことを理解するのに役立ちます。
author: bandersmsft
ms.reviewer: mcville
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: banders
ms.openlocfilehash: 246128ec3d26e4bf3f2a5a8f0660aab7d9daca3d
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/08/2020
ms.locfileid: "89554204"
---
# <a name="transfer-azure-subscriptions-from-one-partner-to-another-preview"></a>Azure サブスクリプションを 1 つのパートナーから別のパートナーに譲渡する (プレビュー)

この記事は、Azure サブスクリプションの課金所有権を譲渡する前と後に知っておくべきことを理解するのに役立ちます。 Azure プランの下にある、1 つの Microsoft パートナーから別の Microsoft パートナーへの Azure サブスクリプションの譲渡を開始するには、パートナーに連絡する必要があります。 パートナーは、開始方法に関する指示を送信します。 譲渡処理が完了すると、サブスクリプションの課金所有権が変更されます。

## <a name="user-access"></a>ユーザー アクセス

移転中、Azure ロールベースのアクセス制御 (RBAC) を使用して割り当てられた既存のユーザー、グループ、またはサービス プリンシパルへのアクセスは影響を受けません。 [Azure RBAC](../../role-based-access-control/overview.md) は、Azure のリソースにアクセスできるユーザー、そのユーザーがそれらのリソースに対して実行できること、そのユーザーがアクセスできる領域を管理するのに役立ちます。 サブスクリプションの譲渡によって、新しいパートナーにはリソースへの RBAC アクセス権は付与されません。 以前のパートナーは RBAC へのアクセスを維持しています。

そのため、以前のパートナーの Azure RBAC アクセスを削除し、新しいパートナーのアクセス権を追加することが重要です。 新しいパートナーにアクセス権を付与する方法の詳細については、「[Azure ロールベースのアクセス制御 (Azure RBAC) とは](../../role-based-access-control/overview.md)」を参照してください。 以前のパートナーの RBAC アクセス権を削除する方法の詳細については、「[ロールの割り当てを削除する](../../role-based-access-control/role-assignments-portal.md#remove-a-role-assignment)」を参照してください。

また、新しいパートナーは、サブスクリプションへの[代理管理者 (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) アクセス権を自動的に取得することはありません。 パートナーがお客様に代わって Azure サブスクリプションを管理するには、AOBO が必要です。 新しいパートナーに AOBO アクセス権を付与する方法の詳細については、「[Azure サブスクリプション アカウントの課金所有権を別のアカウントに譲渡する](billing-subscription-transfer.md)」を参照してください。

## <a name="stop-a-transfer"></a>譲渡を停止する

譲渡要求が送信されたことを示す確認メッセージが表示されたときに、**譲渡が続行されないようにする**場合は、次のいずれかのオプションを使用します。

- 現在のパートナーが要求をまだ受け入れていない場合は、開始した譲渡要求をキャンセルするように新しいパートナーに依頼できます (状態が保留中の場合)。
- 譲渡要求を受信したときにアクションを実行しないように現在のパートナーに依頼します。 現在のパートナーの同意がないと、譲渡を続行できません。 要求の有効期限が切れます。
- 新しいパートナーと_リセラーとの関係を削除_できます。 このアクションにより、サブスクリプションを移行する機能が削除されます。 要求が効果的にキャンセルされます。

また、[Microsoft の法律](https://www.microsoft.com/legal/) Web サイトにある任意のオプションを使用して、ヘルプ、誤報告、疑わしいアクティビティを検索することもできます。 問題を報告するオプションは、コンプライアンス & 倫理に関するセクションの下にあります。

## <a name="next-steps"></a>次のステップ

- 新しいパートナーに RBAC アクセス権を付与するには、「[Azure ロールベースのアクセス制御 (Azure RBAC) とは](../../role-based-access-control/overview.md)」を参照してください。
- 新しいパートナーに AOBO アクセス権を付与するには、「[Azure サブスクリプション アカウントの課金所有権を別のアカウントに譲渡する](billing-subscription-transfer.md)」を参照してください。