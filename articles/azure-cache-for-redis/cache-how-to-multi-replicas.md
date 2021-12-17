---
title: Azure Cache for Redis にレプリカを追加する
description: Premium レベルの Azure Cache for Redis インスタンスに、より多くのレプリカを追加する方法について説明します
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: ca535f7f30db66bf527c00b737dc43ee1a7f1603
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129538246"
---
# <a name="add-replicas-to-azure-cache-for-redis"></a>Azure Cache for Redis にレプリカを追加する

この記事では、Azure portal を使用して、追加のレプリカを持つ Azure Cache for Redis インスタンスを設定する方法について説明します。

Azure Cache for Redis の Standard レベルと Premium レベルでは、2 つの専用仮想マシン (VM) で各キャッシュをホストすることで冗長性が提供されます。 これらの VM は、プライマリとレプリカとして構成されます。 プライマリ VM が使用できなくなると、レプリカでは、それが検出されて、新しいプライマリとして自動的に引き継ぎが行われます。 Premium キャッシュ内のレプリカの数は、3 つまで増やせるようになったため、キャッシュをバックアップする VM は合計 4 つになります。 複数のレプリカを使用すると、単一のレプリカで提供できるレベルよりも高い回復力が得られます。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)

## <a name="create-a-cache"></a>キャッシュの作成

キャッシュを作成するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) にサインインし、**[リソースの作成]** を選択します。
  
1. **[新規]** ページで、 **[データベース]** を選択し、 **[Azure Cache for Redis]** を選択します。

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="[Azure Cache for Redis] を選択します。":::

1. **[基本]** ページで、新しいキャッシュの設定を構成します。

    | 設定      | 推奨値  | 説明 |
    | ------------ |  ------- | -------------------------------------------------- |
    | **サブスクリプション** | サブスクリプションを選択します。 | この新しい Azure Cache for Redis インスタンスが作成されるサブスクリプション。 |
    | **リソース グループ** | リソース グループを選択するか、 **[新規作成]** を選択し、新しいリソース グループの名前を入力します。 | その中にキャッシュやその他のリソースを作成するリソース グループの名前。 すべてのアプリ リソースを 1 つのリソース グループに配置することで、それらをまとめて簡単に管理または削除できます。 |
    | **DNS 名** | グローバルに一意の名前を入力します。 | キャッシュ名は 1 から 63 文字の文字列で、数字、英字、ハイフンのみを使用する必要があります。 名前の先頭と末尾には数字または文字を使用する必要があり、連続するハイフンを含めることはできません。 キャッシュ インスタンスの "*ホスト名*" は、 *\<DNS name>.redis.cache.windows.net* になります。 |
    | **場所** | 場所を選択します。 | キャッシュを使用する他のサービスの近くの[リージョン](https://azure.microsoft.com/regions/)を選択します。 |
    | **キャッシュの種類** | [Premium レベル](https://azure.microsoft.com/pricing/details/cache/) キャッシュを選択します。 |  価格レベルによって、キャッシュに使用できるのサイズ、パフォーマンス、および機能が決まります。 詳細については、[Azure Cache for Redis の概要](cache-overview.md)に関するページを参照してください。 |

1. **[詳細設定]** ページで、 **[レプリカ数]** を選択します。

    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="[レプリカ数]。":::

    > [!NOTE]
    > 現在のところ、Append-only File (AOF) の永続化と geo レプリケーションは複数のレプリカ (2 個以上のレプリカ) では利用できません。
    >

1. 他のオプションは既定の設定のままにします。

1. **［作成］** を選択します

    キャッシュが作成されるまで、しばらく時間がかかります。 Azure Cache for Redis の **[概要]** ページで進行状況を監視できます。 **[状態]** に "**実行中**" と表示されている場合は、キャッシュを使用する準備ができています。

    > [!NOTE]
    > キャッシュ作成後にその中のレプリカの数を変更することはできません。
    >

## <a name="next-steps"></a>次の手順

Azure Cache for Redis の機能について

> [!div class="nextstepaction"]
> [Azure Cache for Redis Premium サービス レベル](cache-overview.md#service-tiers)
