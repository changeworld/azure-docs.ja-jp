---
title: Azure Database for PostgreSQL - フレキシブル サーバーのリリース ノート
description: Azure Database for PostgreSQL - フレキシブル サーバーのリリース ノート。
author: sr-msft
ms.author: srranga
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 04/26/2021
ms.openlocfilehash: ad417bc44de7d13594f003f7aa1e39b2308204ed
ms.sourcegitcommit: 5f785599310d77a4edcf653d7d3d22466f7e05e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/27/2021
ms.locfileid: "108064865"
---
# <a name="release-notes---azure-database-for-postgresql---flexible-server"></a>リリース ノート - Azure Database for PostgreSQL - フレキシブル サーバー

このページでは、PostgreSQL フレキシブル サーバーに関連する機能の追加、エンジン バージョンのサポート、拡張機能、およびその他の発表に関する最新ニュースと更新情報を提供します。

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーはプレビュー段階です

## <a name="release-april-26-2021"></a>リリース: 2021 年 4 月 26 日

* 新たなサーバー作成での[最新の PostgreSQL マイナー バージョン](./concepts-supported-versions.md) 12.6 および 11.11 のサポート。
* 仮想ネットワーク (VNET) の[プライベート DNS ゾーン](./concepts-networking.md#private-access-vnet-integration)のサポート。
* 特定の時点に復旧操作中に可用性ゾーンを選択できるようになりました。
* オーストラリア東部、カナダ中部、およびフランス中部を含む新しい[リージョン](./overview.md#azure-regions)のサポート。
* [組み込みの PgBouncer](./concepts-pgbouncer.md) 接続プーラーのサポート。 
* [pglogical](https://github.com/2ndQuadrant/pglogical) 拡張機能バージョン 2.3.2 のサポート。
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