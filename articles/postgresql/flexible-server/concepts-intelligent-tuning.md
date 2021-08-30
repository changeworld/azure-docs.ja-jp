---
title: インテリジェント チューニング - Azure Database for PostgreSQL - フレキシブル サーバー
description: この記事では、Azure Database for PostgreSQL - フレキシブル サーバーのインテリジェント チューニングの機能について説明します。
author: nathan-wisner-ms
ms.author: nathanwisner
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/04/2021
ms.openlocfilehash: d16a1842c4dfed044a8ac865a3dcdcd6cbe05bf3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780702"
---
# <a name="perform-intelligent-tuning-on-your-postgresql-flex-server"></a>PostgreSQL のフレキシブル サーバーでインテリジェント チューニングを実行する

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーはプレビュー段階です

> [!IMPORTANT]
> インテリジェント チューニングはプレビュー段階です

**適用対象:** Azure Database for PostgreSQL - フレキシブル サーバー バージョン 11 以降

Azure Database for PostgreSQL フレキシブル サーバーのインテリジェント チューニング機能を使用すると、データベースのパフォーマンスを自動的に向上させる方法が提供されます。 Microsoft のサービスでは、インテリジェント チューニングによって、使用パターンと値に基づいて、Checkpoint_completion_target、min_wal_size、および max_wal_size が自動的に調整されます。 このサービスでは、データベースに関する統計を 30 分ごとにクエリし、ユーザーとの対話なしでパフォーマンスを最適化するために継続的な調整が行われます。

## <a name="enabling-intelligent-tuning"></a>インテリジェント チューニングの有効化

インテリジェント チューニングはオプトイン機能なので、既定ではサーバー上でアクティブになりません。 このチューニングは単一のデータベースで使用でき、グローバルではないため、1 つのデータベースで有効にしても、接続されているすべてのデータベースで有効にはなりません。

### <a name="enable-intelligent-tuning-using-the-azure-portal"></a>Azure portal を使用してインテリジェント チューニングを有効にする

1. Azure portal にサインインし、ご利用の Azure Database for PostgreSQL サーバーを選択します。
2. メニューの [設定] セクションで、 [サーバー パラメーター] を選択します。
3. インテリジェント チューニング パラメーターを検索します。
4. 値を True に設定し、保存します。

azure_sys データベースに保持するデータの最初のバッチには、最大で 35 分ほどかかります。

## <a name="information-about-intelligent-tuning"></a>インテリジェント チューニングに関する情報

インテリジェント チューニングは、特定の時間の 3 つの主要機能に基づいて動作します。

* Checkpoint_completion_target
* Min_wal_size
* Max_wal_size

これら 3 つの変数は、主に次の要素に影響します

* チェックポイントの期間
* チェックポイントの頻度
* 同期の期間

インテリジェント チューニングは双方向で動作し、ワークロードが高い間は期間を短縮し、アイドル セグメント中はそれらを増やすことを試みます。 これらのルールは、ユーザーが手動で更新しなくても、困難な期間中にカスタマイズされた結果を得ることができるように、これらの機能を最適化しようとする方法です。

## <a name="limitations-and-known-issues"></a>制限事項と既知の問題

* 最適化は特定の範囲でのみ実行され、変更が行われない可能性がある
* クエリ内の削除されたデータベースによってインテリジェント チューニング機能が遅延することがあり、その結果、改善の実行にわずかな遅延が生じる可能性がある
  
最適化は現時点ではストレージ セクションだけで実行され、他のさまざまなカテゴリへの拡張は未定
