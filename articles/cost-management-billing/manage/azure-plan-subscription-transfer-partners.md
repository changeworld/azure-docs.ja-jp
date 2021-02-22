---
title: Azure プランの下で、あるパートナーから別のパートナーにサブスクリプションを譲渡する (プレビュー)
description: この記事は、Azure サブスクリプションの課金所有権を譲渡する前と後に知っておくべきことを理解するのに役立ちます。
author: bandersmsft
ms.reviewer: mcville
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.openlocfilehash: d234227de98beeed6898697fb6ed8e0865546e16
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100555852"
---
# <a name="transfer-subscriptions-under-an-azure-plan-from-one-partner-to-another-preview"></a>Azure プランの下で、あるパートナーから別のパートナーにサブスクリプションを譲渡する (プレビュー)

この記事は、Azure サブスクリプションの課金所有権を譲渡する前と後に知っておくべきことを理解するのに役立ちます。 Azure プランの下にある、1 つの Microsoft パートナーから別の Microsoft パートナーへの Azure サブスクリプションの譲渡を開始するには、パートナーに連絡する必要があります。 パートナーは、開始方法に関する指示を送信します。 譲渡処理が完了すると、サブスクリプションの課金所有権が変更されます。

## <a name="user-access"></a>ユーザー アクセス

移転中、Azure ロールベースのアクセス制御 (Azure RBAC) を使用して割り当てられた既存のユーザー、グループ、またはサービス プリンシパルへのアクセスは影響を受けません。 [Azure RBAC](../../role-based-access-control/overview.md) は、Azure のリソースにアクセスできるユーザー、そのユーザーがそれらのリソースに対して実行できること、そのユーザーがアクセスできる領域を管理するのに役立ちます。 サブスクリプションの譲渡によって、新しいパートナーにはリソースへの Azure RBAC アクセス権は付与されません。 以前のパートナーは Azure RBAC へのアクセスを維持しています。

そのため、以前のパートナーの Azure RBAC アクセスを削除し、新しいパートナーのアクセス権を追加することが重要です。 新しいパートナーにアクセス権を付与する方法の詳細については、「[Azure ロールベースのアクセス制御 (Azure RBAC) とは](../../role-based-access-control/overview.md)」を参照してください。 以前のパートナーの Azure RBAC アクセス権を削除する方法の詳細については、「[Azure ロールの割り当てを削除する](../../role-based-access-control/role-assignments-remove.md)」を参照してください。

また、新しいパートナーは、サブスクリプションへの[代理管理者 (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) アクセス権を自動的に取得することはありません。 パートナーがお客様に代わって Azure サブスクリプションを管理するには、AOBO が必要です。 Azure の特権の詳細については、「[顧客のサービスまたはサブスクリプションを管理するためのアクセス許可を取得する](/partner-center/customers-revoke-admin-privileges)」を参照してください。

## <a name="stop-a-transfer"></a>譲渡を停止する

譲渡要求が送信されたことを示す確認メッセージが表示されたときに、**譲渡が続行されないようにする** 場合は、次のいずれかのオプションを使用します。

- 現在のパートナーが要求をまだ受け入れていない場合は、開始した譲渡要求をキャンセルするように新しいパートナーに依頼できます (状態が保留中の場合)。
- 譲渡要求を受信したときにアクションを実行しないように現在のパートナーに依頼します。 現在のパートナーの同意がないと、譲渡を続行できません。 要求の有効期限が切れます。
- 新しいパートナーと _リセラーとの関係を削除_ できます。 このアクションにより、サブスクリプションを移行する機能が削除されます。 要求が効果的にキャンセルされます。

また、[Microsoft の法律](https://www.microsoft.com/legal/) Web サイトにある任意のオプションを使用して、ヘルプ、誤報告、疑わしいアクティビティを検索することもできます。 問題を報告するオプションは、コンプライアンス & 倫理に関するセクションの下にあります。

## <a name="next-steps"></a>次のステップ

- 新しいパートナーに Azure RBAC アクセス権を付与するには、「[Azure ロールベースのアクセス制御 (Azure RBAC) とは](../../role-based-access-control/overview.md)」を参照してください。
- [顧客のサービスまたはサブスクリプションを管理するためのアクセス許可を取得する](/partner-center/customers-revoke-admin-privileges)