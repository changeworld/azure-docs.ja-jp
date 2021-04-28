---
title: Azure Database for PostgreSQL - フレキシブル サーバーでのクエリ ストアのベスト プラクティス
description: この記事では、Azure Database for PostgreSQL - フレキシブル サーバーでのクエリ ストア関連のベスト プラクティスについて説明します。
author: ssen-msft
ms.author: ssen
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 9ee2dc4b3e8ea6a9f892adbc378e8e13b8bd8160
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2021
ms.locfileid: "107558692"
---
# <a name="best-practices-for-query-store---flexible-server"></a>クエリ ストア - フレキシブル サーバーのベスト プラクティス

**適用対象:** Azure Database for PostgreSQL - フレキシブル サーバー バージョン 11、12

この記事では、Azure Database for PostgreSQL でクエリ ストアを使用するさいのベスト プラクティスについて説明します。

## <a name="set-the-optimal-query-capture-mode"></a>最適なクエリ キャプチャ モードを設定します。
重要なデータをクエリ ストアをキャプチャさせます。 

|**pg_qs.query_capture_mode** | **シナリオ**|
|---|---|
|_すべて_  |すべてのクエリとその実行頻度や他の統計情報の観点から徹底的にワークロードを分析します。 ワークロード中の新しいクエリを特定します｡ アドホック クエリを使用してユーザーまたは自動化によるパラメーター化が特定されているかどうかを検出します。 _すべて_ はリソース消費の増加というコストを伴います｡ |
|_上位_  |上位のクエリ (クライアントによって発行されたクエリ) に注目します｡
|_なし_ |[なし] に設定すると、クエリ ストアで新しいクエリがキャプチャされなくなります。 調査したいクエリ セットと時間枠をすでにキャプチャしているため､他のクエリによって生まれる邪魔なものを排除したい考えているとします｡ テストおよびベンチマーク環境では､_なし_ が適切です｡ 重要な新しいクエリを追跡､最適化する機会を逃す可能性があるため､_なし_ を使用するときは注意してください｡ |


> [!NOTE] 
> **pg_qs.query_capture_mode** は **pgms_wait_sampling.query_capture_mode** に優先します。 Pg_qs.query_capture_mode が _なし_ の場合、pgms_wait_sampling.query_capture_mode の設定は何の働きもしません｡ 


## <a name="keep-the-data-you-need"></a>必要なデータを保持します。
**Pg_qs.retention_period_in_days** パラメーターには､クエリ ストアのデータ保持期間を日数で指定します。 この期間より古いクエリと統計データは削除されます。 既定では、クエリ ストアは 7 日の間データを保持するように構成されます｡ 使用する予定がない履歴データを残さないでください。 もっと長くデータを保持する必要がある場合は、値を大きくします｡


## <a name="next-steps"></a>次のステップ
- [Azure portal](howto-configure-server-parameters-using-portal.md) または [Azure CLI](howto-configure-server-parameters-using-cli.md) を使用してパラメーターを取得または設定する方法を学びます｡
