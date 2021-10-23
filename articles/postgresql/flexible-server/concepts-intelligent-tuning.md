---
title: インテリジェント チューニング - Azure Database for PostgreSQL - フレキシブル サーバー
description: この記事では、Azure Database for PostgreSQL - フレキシブル サーバーのインテリジェント チューニングの機能について説明します。
author: nathan-wisner-ms
ms.author: nathanwisner
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/04/2021
ms.openlocfilehash: b354394a393907f47b5c29adf668c8d42e059122
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130044897"
---
# <a name="perform-intelligent-tuning-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - フレキシブル サーバーでのインテリジェント チューニングの実行

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーおよびインテリジェント チューニングはプレビュー段階です。

**適用対象:** Azure Database for PostgreSQL - フレキシブル サーバー バージョン 11 以降。

Azure Database for PostgreSQL - フレキシブル サーバーのインテリジェント チューニング機能を使用すると、データベースのパフォーマンスを自動的に向上させる方法が提供されます。 インテリジェント チューニングによって、使用パターンと値に基づいて、`checkpoint_completion_target`、`min_wal_size`、`bgwriter_delay` パラメーターが自動的に調整されます。 データベースの統計を 30 分ごとに照会し、操作なしでパフォーマンスを最適化するために持続的に調整を行います。

インテリジェント チューニングはオプトイン機能なので、既定ではサーバー上でアクティブになりません。 これは単数のデータベースで使用できます。グローバルではありません。 1 つのデータベースで有効にしても、接続されているすべてのデータベースで有効になりません。

## <a name="enable-intelligent-tuning-by-using-the-azure-portal"></a>Azure portal を使用してインテリジェント チューニングを有効にする

1. Azure portal にサインインし、ご利用の Azure Database for PostgreSQL サーバーを選択します。
2. メニューの **[設定]** セクションで、 **[サーバー パラメーター]** を選択します。
3. インテリジェント チューニング パラメーターを検索します。
4. 値を **True** に設定し、 **[保存]** を選択します。

*azure_sys* データベースに保持するデータの最初のバッチには、最大で 35 分ほどかかります。

## <a name="information-about-intelligent-tuning"></a>インテリジェント チューニングに関する情報

インテリジェント チューニングは、特定の時間の 3 つの主要パラメーター `checkpoint_completion_target`、`min_wal_size`、`bgwriter_delay` を中心に動作します。

これらの 3 つのパラメーターは、主に次の点に影響します。 

* チェックポイントの期間。
* チェックポイントの頻度。
* 同期の期間。

インテリジェント チューニングは両方向に動作します。 高いワークロードの間に期間を短くし、アイドル セグメントの間に期間を長くしようとします。 これにより、手動で更新することなく、困難な期間中にパーソナライズされた結果を取得できます。

## <a name="limitations-and-known-issues"></a>制限事項と既知の問題

* インテリジェント チューニングでは、特定の範囲でのみ最適化が行われます。 機能により変更が加えられない可能性があります。
* クエリ内の削除されたデータベースによって、機能の改善の実行にわずかな遅延が生じる可能性があります。
* 現時点では、この機能はストレージ セクションでのみ最適化を行います。
