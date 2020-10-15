---
title: インクルード ファイル
description: インクルード ファイル
services: redis-cache
author: curib
ms.service: cache
ms.topic: include
ms.date: 10/06/2020
ms.author: cauribeg
ms.custom: include file
ms.openlocfilehash: ae9526a47a77118503274fc824538980ebcd50de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91829147"
---
1. キャッシュを作成するには、[Azure portal](https://portal.azure.com) にサインインし、 **[リソースの作成]** を選択します。

    :::image type="content" source="media/redis-cache-create/create-resource.png" alt-text="[Azure Cache for Redis] を選択します。":::

   
1. **[新規]** ページで、 **[データベース]** を選択し、 **[Azure Cache for Redis]** を選択します。

    :::image type="content" source="media/redis-cache-create/select-cache.png" alt-text="[Azure Cache for Redis] を選択します。" は、 *\<DNS name>.redis.cache.windows.net* になります。 | 
   | **サブスクリプション** | ドロップダウンで、ご自身のサブスクリプションを選択します。 | この新しい Azure Cache for Redis インスタンスが作成されるサブスクリプション。 | 
   | **リソース グループ** | ドロップ ダウンでリソース グループを選択するか、 **[新規作成]** を選択し、新しいリソース グループの名前を入力します。 | その中にキャッシュやその他のリソースを作成するリソース グループの名前。 すべてのアプリ リソースを 1 つのリソース グループに配置することで、それらをまとめて簡単に管理または削除できます。 | 
   | **場所** | ドロップ ダウンで場所を選択します。 | キャッシュを使用する他のサービスの近くの[リージョン](https://azure.microsoft.com/regions/)を選択します。 |
   | **価格レベル** | ドロップ ダウンで[価格レベル](https://azure.microsoft.com/pricing/details/cache/)を選択します。 |  価格レベルによって、キャッシュに使用できるのサイズ、パフォーマンス、および機能が決まります。 詳細については、[Azure Cache for Redis の概要](../articles/azure-cache-for-redis/cache-overview.md)に関するページを参照してください。 |

1. **[ネットワーク]** タブを選択するか、ページの下部にある **[ネットワーク]** ボタンをクリックします。

1. **[ネットワーク]** タブで、接続方法を選択します。

1. **[次へ: 詳細]** タブを選択するか、ページの下部にある **[次へ: 詳細]** ボタンをクリックします。

1. Basic または Standard のキャッシュ インスタンスの **[詳細]** タブで、非 TLS ポートを有効にする場合は有効トグルをオンにします。

1. Premium キャッシュ インスタンスの **[詳細]** タブで、非 TLS ポート、クラスタリング、データ永続化の設定を構成します。

1. **[次へ: タグ]** タブを選択するか、ページの下部にある **[次へ: タグ]** ボタンをクリックします。

1. 必要に応じて、 **[タグ]** タブで、リソースを分類する場合は名前と値を入力します。 

1.  **[確認および作成]** を選択します。 [確認および作成] タブが表示され、Azure によって構成が検証されます。

1. 緑色の検証に成功のメッセージが表示された後、 **[作成]** を選択します。

キャッシュが作成されるまで、しばらく時間がかかります。 Azure Cache for Redis の **[概要]**  ページで進行状況を監視できます。  **[状態]**  に **[実行中]** と表示されている場合は、キャッシュを使用する準備ができています。 