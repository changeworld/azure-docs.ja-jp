---
title: インクルード ファイル
description: インクルード ファイル
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 03/28/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 55bc2bd4e065c301f11a5fc4d3b58aa443b83e2d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
---
キャッシュを作成するには、まず [Azure Portal](https://portal.azure.com) にサインインし、**[リソースの作成]** > **[データベース]** > **[Redis Cache]** の順にクリックします。

![新しいキャッシュ](media/redis-cache-create/redis-cache-new-cache-menu.png)

**[新規 Redis Cache]** で、新しいキャッシュの設定を構成します。

| Setting      | 推奨値  | [説明] |
| ------------ |  ------- | -------------------------------------------------- |
| **DNS 名** | グローバルに一意の名前 | キャッシュ名は 1 ～ 63 文字の文字列で、数字、英字、`-` 文字のみを使用する必要があります。 キャッシュ名の先頭と末尾には `-` 文字を使用できません。また、連続する `-` 文字は無効です。  | 
| **サブスクリプション** | 該当するサブスクリプション | この新しい Azure Redis Cache が作成されるサブスクリプション。 | 
| **リソース グループ** |  *TestResourceGroup* | その中にキャッシュを作成する新しいリソース グループの名前。 アプリのすべてのリソースを 1 つのグループ内に配置することで、それらを一緒に管理できます。 たとえば、リソース グループを削除すれば、そのアプリに関連付けられているすべてのリソースが削除されます。 | 
| **場所** | 米国東部 | キャッシュを使用する他のサービスの近くの[リージョン](https://azure.microsoft.com/regions/)を選択します。 |
| **[価格レベル](https://azure.microsoft.com/pricing/details/cache/)** |  Basic C0 (250 MB のキャッシュ) |  価格レベルによって、キャッシュのサイズ、パフォーマンス、および使用可能な機能が決まります。 詳細については、[Azure Redis Cache の概要に関するページ](../articles/redis-cache/cache-overview.md)を参照してください。 |
| **ダッシュボードにピン留めする** |  オン | クリックすると、新しいキャッシュをダッシュ ボードにピン留めし、簡単に見つけられるようになります。 |

![キャッシュの作成](media/redis-cache-create/redis-cache-cache-create.png) 

新しいキャッシュ設定を構成したら、**[作成]** をクリックします。 

キャッシュが作成されるまで数分かかる場合があります。 状態を確認するには、ダッシュボードで進行状況を監視してください。 キャッシュが作成されると、新しいキャッシュの状態が **実行中** になり、使用できるようになります。

![作成されたキャッシュ](media/redis-cache-create/redis-cache-cache-created.png)

