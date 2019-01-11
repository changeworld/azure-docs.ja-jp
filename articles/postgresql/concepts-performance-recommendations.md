---
title: Azure Database for PostgreSQL のパフォーマンスに関する推奨事項
description: この記事では、Azure database for PostgreSQL で実現できるパフォーマンスに関する推奨事項について説明します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: 1dedc08f27d1a483290dc61cce879290ca66592d
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548104"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL のパフォーマンスに関する推奨事項

**適用対象:** Azure Database for PostgreSQL 9.6 および 10

> [!IMPORTANT]
> パフォーマンスに関する推奨事項はパブリック プレビュー段階です。

パフォーマンスに関する推奨事項機能は、パフォーマンスを向上させるために、Azure Database for PostgreSQL サーバーで作成できる最上位のインデックスを識別します。 推奨インデックスを作成するために、この機能は、クエリ ストアから報告されたスキーマ、ワークロードなどのさまざまなデータベースの特性を考慮します。 顧客は、パフォーマンスに関する推奨事項を実装した後、パフォーマンスをテストし、変更の影響を評価する必要があります。 

## <a name="permissions"></a>アクセス許可
パフォーマンスに関する推奨事項機能を使用して分析を実行するには、**所有者**または**共同作成者**の許可が必要です。

## <a name="performance-recommendations"></a>パフォーマンスに関する推奨事項
[パフォーマンスに関する推奨事項](concepts-performance-recommendations.md)機能は、パフォーマンスを向上させる可能性のあるインデックスを識別するために、サーバーの間でワークロードを分析します。

PostgreSQL サーバーの Azure Portal ページで、メニュー バーの**サポート + トラブルシューティング**セクションから、**パフォーマンスに関する推奨事項**を開きます。

![パフォーマンスに関する推奨事項ランディング ページ](./media/concepts-performance-recommendations/performance-recommendations-landing-page.png)

**分析**を選択し、データベースを選択します。 これにより、分析が開始されます。 ワークロードによって完了するまで数分かかる場合があります。 分析が完了すると、ポータルで通知があります。

**パフォーマンスに関する推奨事項**ウィンドウには、何かがわかった場合に、推奨事項の一覧に表示されます。 推奨事項は、該当する**データベース**、**テーブル**、**列**、および**インデックス サイズ**に関する情報を表示します。

![パフォーマンスに関する推奨事項の新しいページ](./media/concepts-performance-recommendations/performance-recommendations-result.png)

推奨事項を実装するには、クエリ テキストをコピーし、任意のクライアントから実行します。

## <a name="next-steps"></a>次の手順
- Azure Database for PostgreSQL での[監視およびチューニング](concepts-monitoring.md)の詳細を確認する。

