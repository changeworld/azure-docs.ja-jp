---
title: Azure Database for PostgreSQL - フレキシブル サーバーのリリース ノート
description: Azure Database for PostgreSQL - フレキシブル サーバーのリリース ノート。
author: sr-msft
ms.author: srranga
ms.custom: references_regions
ms.service: postgresql
ms.topic: overview
ms.date: 06/23/2021
ms.openlocfilehash: 87af6f9764c2ab01b0e0d02d8eb4a6c7342ca31c
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2021
ms.locfileid: "112894628"
---
# <a name="release-notes---azure-database-for-postgresql---flexible-server"></a>リリース ノート - Azure Database for PostgreSQL - フレキシブル サーバー

このページでは、PostgreSQL フレキシブル サーバーに関連する機能の追加、エンジン バージョンのサポート、拡張機能、およびその他の発表に関する最新ニュースと更新情報を提供します。

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーはプレビュー段階です

## <a name="release-june-2021"></a>リリース: 2021 年 6 月

* 新規サーバー作成での[最新の PostgreSQL マイナー バージョン](./concepts-supported-versions.md) 13.3、12.7、11.12 のサポート<sup>$</sup>。
* [新しいリージョン](overview.md#azure-regions)のサポート: オーストラリア南東部、ブラジル南部、韓国中部、ノルウェー東部、南アフリカ北部、スイス北部、アラブ首長国連邦北部、米国西部。
* ゾーン冗長高可用性デプロイのための強制フェールオーバーと計画フェールオーバーを含む、[オンデマンド フェールオーバー](./concepts-high-availability.md#on-demand-failover)機能のサポート。
* 新規サーバー作成でのすべてのメジャー バージョンに対する [SCRAM 認証](how-to-connect-scram.md)のサポート<sup>$</sup>。
* 新規サーバー作成での `shared_preload_libraries` を使用した `pg_prewarm` の事前読み込みのサポート<sup>$</sup>。
* lo 拡張機能のサポート。 各メジャー バージョンでサポートされているバージョンについては、[拡張機能のページ](./concepts-extensions.md)を参照してください<sup>$</sup>。
* 複数のバグ修正、安定性とパフォーマンスの向上<sup>$</sup>。
  
<sup> **$** </sup> 既存のサーバーは、サポートされる最新のマイナー バージョンに自動的にアップグレードされ、サーバーの今後のメンテナンス期間中にも新しい機能が有効になります。

## <a name="release-may-2021"></a>リリース: 2021 年 5 月

* [PostgreSQL メジャー バージョン 13 ](./concepts-supported-versions.md)のサポート。
* 拡張機能 (pg_partman、pg_cron、pgaudit など) のサポート。 各メジャー バージョンでサポートされているバージョンについては、[拡張機能のページ](./concepts-extensions.md)を参照してください。
* 複数のバグ修正、安定性とパフォーマンスの向上。

## <a name="release-april-2021"></a>リリース: 2021 年 4 月

* 新たなサーバー作成での[最新の PostgreSQL マイナー バージョン](./concepts-supported-versions.md) 12.6 および 11.11 のサポート。
* 仮想ネットワーク (VNET) の[プライベート DNS ゾーン](./concepts-networking.md#private-access-vnet-integration)のサポート。
* 特定の時点に復旧操作中に可用性ゾーンを選択できるようになりました。
* オーストラリア東部、カナダ中部、およびフランス中部を含む新しい[リージョン](./overview.md#azure-regions)のサポート。
* [組み込みの PgBouncer](./concepts-pgbouncer.md) 接続プーラーのサポート。 
<!--- * Support for [pglogical](https://github.com/2ndQuadrant/pglogical) extension version 2.3.2. -->
* パブリック プレビューでの[インテリジェント パフォーマンス](concepts-query-store.md)。
* 複数のバグ修正、安定性とパフォーマンスの向上。

## <a name="contacts"></a>連絡先

Azure Database for PostgreSQL フレキシブル サーバーについての質問や提案は、Azure Database for PostgreSQL チームにメール ([@Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)) でお送りください。 このメール アドレスはテクニカル サポートのエイリアスではないのでご注意ください。

さらに、適切な連絡先について次の点を考慮してください。

- Azure サポートに問い合わせる場合は、[Azure portal からチケットを申請します](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- アカウントを使用して問題を修正するには、Azure Portal で[サポート要求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を提出します。
- フィードバックを提供したり、新しい機能を要求したりするには、[UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql) でエントリを作成します。
  

## <a name="next-steps"></a>次のステップ

Azure Database for PostgreSQL フレキシブル サーバー デプロイ モードの概要を確認したので、最初のサーバーを作成する準備ができました。[Azure portal を使用して Azure Database for PostgreSQL - フレキシブル サーバーを作成する](./quickstart-create-server-portal.md)