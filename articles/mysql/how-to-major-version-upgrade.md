---
title: メジャー バージョンのアップグレード - Azure portal - Azure Database for MySQL - 単一サーバー
description: この記事では、Azure portal を使用して Azure Database for MySQL - 単一サーバーのメジャー バージョンをアップグレードする方法について説明します
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 78c35e42cefa8897d9f93c3a941b4c0e8b81e5f9
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686490"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server-using-the-azure-portal"></a>Azure portal を使用した Azure Database for MySQL 単一サーバーのメジャー バージョンのアップグレード

> [!IMPORTANT]
> Azure database for MySQL 単一サーバーのメジャー バージョンのアップグレードは、パブリック プレビュー段階にあります。

この記事では、Azure Database for MySQL 単一サーバーで MySQL のメジャー バージョンをインプレース アップグレードする方法について説明します。

この機能を使用すると、データを移動したり、アプリケーションの接続文字列を変更したりしなくても、ボタンをクリックするだけで MySQL 5.6 サーバーを MySQL 5.7 にインプレース アップグレードできます。

> [!Note]
> * メジャー バージョンのアップグレードは、MySQL 5.6 から MySQL 5.7 へのメジャー バージョンのアップグレードにのみ使用できます。<br>
> * メジャー バージョンのアップグレードは、レプリカ サーバーではまだサポートされていません。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドを完了するには、次が必要です。
- [Azure Database for MySQL 単一サーバー](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57"></a>MySQL 5.6 から MySQL 5.7 へのメジャー バージョンのアップグレードを実行する

Azure Database of MySQL 5.6 サーバーのメジャー バージョンのアップグレードを実行するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) で、既存の Azure Database for MySQL 5.6 サーバーを選択します。

2. **[概要]** ページのツール バーで **[アップグレード]** ボタンをクリックします。

3. **[アップグレード]** セクションで、 **[OK]** を選択して、Azure Database for MySQL 5.6 サーバーを 5.7 サーバーにアップグレードします。

    :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL - 概要 - アップグレード":::

4. アップグレードが成功したことを確認する通知が表示されます。

## <a name="next-steps"></a>次のステップ

[Azure Database for MySQL バージョン管理ポリシー](concepts-version-policy.md)について説明します。