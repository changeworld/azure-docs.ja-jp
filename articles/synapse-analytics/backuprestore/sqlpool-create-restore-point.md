---
title: 専用 SQL プール用にユーザー定義の復元ポイントを作成する
description: 専用 SQL プールの復元ポイントを作成する方法。
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c0835fb48d00fe5277732f34fd6b0de1448f6a78
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2020
ms.locfileid: "94332009"
---
# <a name="user-defined-restore-points"></a>ユーザー定義の復元ポイント

この記事では、Azure portal を使用して、Azure Synapse Analytics で専用 SQL プール用に新しいユーザー定義の復元ポイントを作成する方法について説明します。

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Azure portal を使用してユーザー定義の復元ポイントを作成する

ユーザー定義の復元ポイントは、Azure portal を使用して作成することもできます。

1. [Azure portal](https://portal.azure.com/) アカウントにサインインします。

2. 復元ポイントを作成する専用 SQL プールに移動します。

3. 左側のウィンドウで **[概要]** を選択し、 **[+ 新しい復元ポイント]** を選択します。 [新しい復元ポイント] ボタンが有効でない場合は、専用 SQL プールが一時停止されていないことを確認します。

    ![新しい復元ポイント](../media/sql-pools/create-sqlpool-restore-point-01.png)

4. ユーザー定義の復元ポイントの名前を指定して **[適用]** をクリックします。 ユーザー定義の復元ポイントには、7 日間の既定の保有期間があります。

    ![復元ポイントの名前](../media/sql-pools/create-sqlpool-restore-point-02.png)

## <a name="next-steps"></a>次のステップ

- [既存の専用 SQL プールを復元する](restore-sql-pool.md)

