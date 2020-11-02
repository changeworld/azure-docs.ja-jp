---
title: Azure で CSP パートナー情報を使用して Cloudyn に登録する
description: パートナーがその顧客を Cloudyn ポータルにオンボードする際の登録プロセスについて詳しく説明します。
author: bandersmsft
ms.author: banders
ms.date: 10/23/2020
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ROBOTS: NOINDEX
ms.openlocfilehash: 95d8a425314ad1c968915085b35ca89aa3f62711
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/26/2020
ms.locfileid: "92543323"
---
# <a name="register-with-the-csp-partner-program-and-view-cost-data"></a>CSP パートナー プログラムに登録してコスト データを確認する

CSP パートナーおよび登録済みの Cloudyn ユーザーは、Cloudyn でクラウド支出を表示して分析することができます。 Azure Cost Management は、お客様を Microsoft 顧客契約にオンボードし、Azure プランを購入している[直接パートナーがネイティブに使用](../costs/get-started-partners.md)できます。

この登録によって、Cloudyn ポータルにアクセスできるようになります。 このクイック スタートでは、Cloudyn 試用版サブスクリプションの作成に必要な登録プロセスと、Cloudyn ポータルへのサインインについて詳しく説明します。

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="configure-indirect-csp-access-in-cloudyn"></a>Cloudyn で間接 CSP アクセスを構成する

既定で Partner Center API は直接 CSP にのみアクセスできます。 ただし、直接 CSP プロバイダーは、Cloudyn のエンティティ グループを使用して、間接 CSP 顧客またはパートナーのアクセスを構成できます。

間接 CSP 顧客またはパートナーのアクセスを有効にするには、以下の手順を完了して、Cloudyn エンティティ グループを使用して間接 CSP データをセグメント化します。 次に、エンティティ グループに適切なユーザーのアクセス許可を割り当てます。

1. [エンティティの作成](tutorial-user-access.md#create-and-manage-entities)に関する情報を参照して、エンティティ グループを作成します。
2. 「[Assigning subscriptions to Cost Entities](https://www.youtube.com/watch?v=d9uTWSdoQYo)」(サブスクリプションをコスト エンティティに割り当てる) の手順に従って実行します。 間接 CSP 顧客のアカウントとその Azure サブスクリプションを、以前に作成したエンティティに関連付けます。
3. 「[Create a user with admin access](tutorial-user-access.md#create-a-user-with-admin-access)」(管理者アクセス権をユーザーを作成する) の手順に従って、管理者アクセス権を使用してユーザー アカウントを作成します。 次に、間接アカウント用に以前に作成した特定のエンティティに対して、ユーザー アカウントが管理者アクセス権を持っていることを確認します。

間接 CSP パートナーは、パートナー用に作成されたアカウントを使用して Cloudyn ポータルにサインインします。


[!INCLUDE [cost-management-create-account-view-data](../../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、CSP 情報を使用して、Cloudyn に登録しました。 また、Cloudyn ポータルにサインインし、コスト データの確認を開始しました。 Cloudyn に関する詳細は、Cloudyn のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [使用状況とコストを確認する](tutorial-review-usage.md)