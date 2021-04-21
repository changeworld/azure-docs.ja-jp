---
title: サーバー グループ - Hyperscale (Citus) - Azure Database for PostgreSQLをアップグレードする
description: この記事では、Azure Database for PostgreSQL - Hyperscale (Citus) で PostgreSQL と Citus をアップグレードする方法について説明します。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 4/5/2021
ms.openlocfilehash: 3758e2d458e1a6bd052ac746ac361de033d508e9
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023887"
---
# <a name="upgrade-hyperscale-citus-server-group"></a>Hyperscale (Citus) サーバー グループをアップグレードする

以下の手順では、すべてのサーバー グループ ノードで新しいメジャー バージョンの PostgreSQL にアップグレードする方法について説明します。

## <a name="test-the-upgrade-first"></a>まずアップグレードをテストする

PostgreSQL をアップグレードすると、想像するよりも多くの変更が発生します。これは、Hyperscale (Citus) によって、Citus 拡張機能を含む[データベース拡張機能](concepts-hyperscale-extensions.md)もアップグレードされるためです。
運用環境をアップグレードする前に、新しい PostgreSQL と Citus のバージョンでアプリケーションをテストすることを強くお勧めします。

テストする便利な方法は、[ポイントインタイム リストア](concepts-hyperscale-backup.md#point-in-time-restore-pitr)を使用してサーバー グループのコピーを作成することです。 コピーをアップグレードし、それに対してアプリケーションをテストします。 すべてが正常に動作することを確認したら、元のサーバー グループをアップグレードします。

## <a name="upgrade-a-server-group-in-the-azure-portal"></a>Azure portal でサーバー グループをアップグレードする

1. Hyperscale (Citus) サーバー グループの **[概要]** セクションで、 **[アップグレード]** ボタンを選択します。
1. PostgreSQL と Citus の現在のバージョンを示すダイアログが表示されます。
   **[Upgrade to]\(アップグレード後のバージョン\)** の一覧から新しい PostgreSQL のバージョンを選択します
1. **[アップグレード後の Citus バージョン]** の値が想定どおりであることを確認します。
   この値は、選択した PostgreSQL のバージョンによって変わります。
1. **[アップグレード]** ボタンを選択して続行します。

## <a name="next-steps"></a>次のステップ

* [サポートされている+ PostgreSQL のバージョン](concepts-hyperscale-versions.md)について確認します。
* Hyperscale (Citus) サーバー グループの各 PostgreSQL バージョンにパッケージ化されている[拡張機能](concepts-hyperscale-extensions.md)を確認します。
