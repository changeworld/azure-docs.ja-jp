---
title: Enterprise Azure Cache for Redis インスタンスのアクティブ geo レプリケーションを構成する
description: Azure リージョンをまたいで Azure Cache for Redis Enterprise インスタンスをレプリケートする方法について説明します。
author: curib
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: cauribeg
ms.openlocfilehash: 99169aaacf0ddd39fb4213ee6c1d83c05a71d532
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129538490"
---
# <a name="configure-active-geo-replication-for-enterprise-azure-cache-for-redis-instances-preview"></a>Enterprise Azure Cache for Redis インスタンスのアクティブ geo レプリケーションを構成する (プレビュー)

この記事では、Azure portal を使用し、アクティブ geo レプリケートされた Azure Cache を構成する方法について説明します。

アクティブ geo レプリケーションは、最大 5 つの Enterprise Azure Cache for Redis をグループ化して、複数の Azure リージョンにまたがる 1 つのキャッシュにします。 インスタンスはすべて、ローカルのプライマリとして機能します。 アプリケーションでは、読み取りおよび書き込み要求に使用するインスタンスを決定します。

> [!NOTE]
> Azure リージョン間のデータ転送は、標準の[帯域幅レート](https://azure.microsoft.com/pricing/details/bandwidth/)で課金されます。

## <a name="create-or-join-an-active-geo-replication-group"></a>アクティブ geo レプリケーション グループを作成または参加する

> [!IMPORTANT]
> アクティブ geo レプリケーションは、Azure Cache for Redis が作成された時点で有効になる必要があります。
>
>

1. **[新規 Redis Cache]** 作成 UI の **[詳細設定]** タブで、 **[Clustering policy]\(クラスタリング ポリシー\)** に対して **[Enterprise]** を選択します。

    ![アクティブ geo レプリケーションの構成](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-not-configured.png)

1. **[構成]** を選択して **[アクティブ geo レプリケーション]** を設定します。

1. 最初のキャッシュ インスタンス用に新しいレプリケーション グループを作成するか、一覧から既存のものを選択します。

    ![キャッシュのリンク](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-new-group.png)

1. **[構成]** を選択して完了します。

    ![構成されたアクティブ geo レプリケーション](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-configured.png)

1. 最初のキャッシュが正常に作成されるまで待ちます。 Geo レプリケーション グループ内の追加のキャッシュ インスタンスごとに、上記の手順を繰り返します。

## <a name="remove-from-an-active-geo-replication-group"></a>アクティブ geo レプリケーション グループから削除する

アクティブ geo レプリケーション グループからキャッシュ インスタンスを削除するには、そのインスタンスを削除します。 残りのインスタンスは自動的に再構成されます。

## <a name="next-steps"></a>次のステップ

Azure Cache for Redis の機能について

* [Azure Cache for Redis サービス レベル](cache-overview.md#service-tiers)
* [Azure Cache for Redis の高可用性](cache-high-availability.md)
