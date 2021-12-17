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
ms.openlocfilehash: 74b23eb8560a830e7e07a86fe745b94619eeeaf0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440330"
---
1. キャッシュを作成するには、[Azure portal](https://portal.azure.com) にサインインし、 **[リソースの作成]** を選択します。

    :::image type="content" source="media/redis-cache-create/create-resource.png" alt-text="左側のナビゲーション ペインの [リソースの作成] が強調表示されています。":::

1. **[新規]** ページで、 **[データベース]** を選択し、 **[Azure Cache for Redis]** を選択します。

    :::image type="content" source="media/redis-cache-create/select-cache.png" alt-text="[新規] で、 [データベース] が強調表示され、[Azure Cache for Redis] が強調表示されています。":::

1. **[新規 Redis Cache]** ページで、新しいキャッシュの設定を構成します。

   | 設定      | 値を選択する  | 説明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **サブスクリプション** | ドロップダウンで、ご自身のサブスクリプションを選択します。 | この新しい Azure Cache for Redis インスタンスが作成されるサブスクリプション。 |
   | **リソース グループ** | ドロップ ダウンでリソース グループを選択するか、 **[新規作成]** を選択し、新しいリソース グループの名前を入力します。 | その中にキャッシュやその他のリソースを作成するリソース グループの名前。 すべてのアプリ リソースを 1 つのリソース グループに配置することで、それらをまとめて簡単に管理または削除できます。 |
   | **DNS 名** | 一意の名前を入力します。 | キャッシュ名は 1 から 63 文字の文字列で、数字、英字、ハイフンのみを使用する必要があります。 名前の先頭と末尾には数字または文字を使用する必要があり、連続するハイフンを含めることはできません。 キャッシュ インスタンスの "*ホスト名*" は、 *\<DNS name>.redis.cache.windows.net* になります。 |
   | **場所** | ドロップ ダウンで場所を選択します。 | キャッシュを使用する他のサービスの近くの[リージョン](https://azure.microsoft.com/regions/)を選択します。 |
   | **キャッシュの種類** | ドロップ ダウンで[レベル](https://azure.microsoft.com/pricing/details/cache/)を選択します。 |  レベルによって、キャッシュに使用できるのサイズ、パフォーマンス、および機能が決まります。 詳細については、[Azure Cache for Redis の概要](../cache-overview.md)に関するページを参照してください。 |

1. **[ネットワーク]** タブを選択するか、ページの下部にある **[ネットワーク]** ボタンを選択します。

1. **[ネットワーク]** タブで、接続方法を選択します。

1. **[次へ: 詳細]** タブを選択するか、ページの下部にある **[次へ: 詳細]** ボタンを選択します。

1. Basic または Standard のキャッシュ インスタンスの **[詳細]** タブで、非 TLS ポートを有効にする場合は有効トグルをオンにします。 また、使用する Redis のバージョンを 4 または 6 から選択できます。

    :::image type="content" source="media/redis-cache-create/cache-redis-version.png" alt-text="Redis バージョン 4 または 6。":::

1. Premium キャッシュ インスタンスの **[詳細]** タブで、非 TLS ポート、クラスタリング、データ永続化の設定を構成します。 また、使用する Redis のバージョンを 4 または 6 から選択できます。

1. ページの下部にある **[次へ: タグ]** タブを選択するか、ページの下部にある **[次へ: タグ]** ボタンを選択します。

1. 必要に応じて、 **[タグ]** タブで、リソースを分類する場合は名前と値を入力します。

1. **[Review + create]\(レビュー + 作成\)** を選択します。 [確認および作成] タブが表示され、Azure によって構成が検証されます。

1. 緑色の検証に成功のメッセージが表示された後、 **[作成]** を選択します。

キャッシュが作成されるまで、しばらく時間がかかります。 Azure Cache for Redis の **[概要]** ページで進行状況を監視できます。 **[状態]** に "**実行中**" と表示されている場合は、キャッシュを使用する準備ができています。

<!-- Comment to dirty file. -->