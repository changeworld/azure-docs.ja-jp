---
title: Azure データ エクスプローラーからデータを削除する
description: この記事では、Azure データ エクスプローラーで、消去およびリテンション期間を使用した削除を含む、一括削除のシナリオについて説明します。
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 485ee48bf197ad996bba8a516b80882f44ba623a
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212915"
---
# <a name="delete-data-from-azure-data-explorer"></a>Azure データ エクスプローラーからデータを削除する

Azure データ エクスプローラーでは、この記事で取り上げる一括削除のアプローチをいくつかサポートしています。 これでは高速な読み取りアクセスが最適化されているので、リアル タイムでのレコードごとの削除はサポートされていません。

* 1 つ以上のテーブルが不要になった場合、drop table または drop tables コマンドを使用して削除します。

    ```Kusto
    .drop table <TableName>

    .drop tables (<TableName1>, <TableName2>,...)
    ```

* 不要になった古いデータは、データベースまたはテーブル レベルでリテンション期間を変更して削除します。

    90 日間のリテンション期間が設定されているデータベースまたはテーブルがあるとします。 ビジネスでの変更により、現在データは 60 日間のみ必要になったとします。 この場合、次のいずれかの方法で古いデータを削除します。

    ```Kusto
    .alter-merge database <DatabaseName> policy retention softdelete = 60d

    .alter-merge table <TableName> policy retention softdelete = 60d
    ```

    詳細については、「[Retention policy](https://docs.microsoft.com/azure/kusto/concepts/retentionpolicy)」 (アイテム保持ポリシー) を参照してください。

データの削除の問題でサポートが必要な場合は、[Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) でサポート要求を開いてください。
