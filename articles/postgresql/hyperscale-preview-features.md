---
title: Azure Database for PostgreSQL - Hyperscale (Citus) のプレビュー機能
description: 現在プレビュー中の最新の機能一覧
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: overview
ms.date: 04/07/2021
ms.openlocfilehash: 5f0552e09464a6b571a665cfe3895f48c3aa8c41
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258454"
---
# <a name="preview-features-for-postgresql---hyperscale-citus"></a>PostgreSQL - Hyperscale (Citus) のプレビュー機能

Azure Database for PostgreSQL - Hyperscale (Citus) では、未リリースの機能のプレビューを提供しています。 プレビュー バージョンは、サービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。  詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="features-currently-in-preview"></a>現在プレビュー段階の機能

現在プレビュー向けに提供されている機能は次のとおりです。

* **[Basic レベル](concepts-hyperscale-tiers.md)** 。 ワーカー ノードは使用せずコーディネーター ノードのみを使用してサーバー グループを実行します。 初期のテストと開発を行ったり、小規模な運用環境のワークロードを扱ったりするための経済的な手段です。
* **[PostgreSQL 12 および 13](concepts-hyperscale-versions.md)** 。
  最新のデータベース バージョンをサーバー グループで使用してください。
* **[Citus 10](concepts-hyperscale-versions.md#citus-and-other-extension-versions)** 。
  PostgreSQL 13 を実行しているサーバー グループに自動的にインストールされます。
* **[カラム型ストレージ](concepts-hyperscale-columnar.md)** 。
  選択したテーブルの (行ではなく) 列をディスク上に連続的に格納します。 ディスクの圧縮がサポートされます。 分析とデータ ウェアハウジングのワークロードに適しています。
* **[読み取りレプリカ](howto-hyperscale-read-replicas-portal.md)** (現在は同一リージョンのみ)。 プライマリ サーバー グループに生じたすべての変更がそのレプリカに反映されます。レプリカに対するクエリによって、元のプライマリ サーバー グループに余分な負荷がかかることはありません。
  レプリカは、読み取り専用ワークロードのパフォーマンスを向上させる効果的な手段です。
* **[マネージド PgBouncer](concepts-hyperscale-limits.md#managed-pgbouncer-preview)** 。
  アクティブな接続の数を制限しながら、一度に多数のクライアントがサーバー グループに接続することを可能にする接続プーラーです。 コーディネーター ノードの円滑な実行を維持しながら接続要求を満たします。
* **[pgAudit](concepts-hyperscale-audit.md)** 。 PostgreSQL の標準的なログ記録機能を使用して、セッションおよびオブジェクトの詳細な監査ログを提供します。 特定の政府、財務、ISO 認定の監査を通過するうえで必要な監査ログが生成されます。

### <a name="available-regions-for-preview-features"></a>プレビュー機能が提供されるリージョン

pgAudit 拡張機能は、[Hyperscale (Citus) がサポートするリージョン](concepts-hyperscale-configuration-options.md#regions)すべてで利用できます。
その他のプレビュー機能は、**米国東部** でのみ提供されます。

## <a name="does-my-server-group-have-access-to-preview-features"></a>私のサーバー グループからプレビュー機能を利用することはできますか?

ご利用の Hyperscale (Citus) サーバー グループでプレビュー機能が有効になっているかどうかを確かめるには、Azure portal でサーバー グループの **[概要]** ページに移動します。
**[Tier: Basic (preview)]\(レベル: Basic (プレビュー)\)** または **[Tier: Standard (preview)]\(レベル: Standard (プレビュー)\)** のどちらかのプロパティが表示される場合は、お使いのサーバー グループからプレビュー機能をご利用いただけます。

### <a name="how-to-get-access"></a>利用方法

新しい Hyperscale (Citus) サーバー グループを作成する際に、 **[プレビュー機能を有効にする]** チェック ボックスをオンにします。

## <a name="contact-us"></a>お問い合わせ

プレビュー機能を使用した感想をメール ([Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)) でお寄せください。
(このメール アドレスはテクニカル サポートチャネルのエイリアスではありません。 技術的な問題については、[サポート リクエスト](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を開いてください。)
