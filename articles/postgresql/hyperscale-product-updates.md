---
title: Azure Database for PostgreSQL - Hyperscale (Citus) の製品更新
description: 新機能とプレビュー中の機能
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: overview
ms.date: 10/15/2021
ms.openlocfilehash: aed4439cf608c5080d5c429a4f3af957bd77ccdb
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130070130"
---
# <a name="product-updates-for-postgresql---hyperscale-citus"></a>PostgreSQL - Hyperscale (Citus) の製品更新

## <a name="updates-feed"></a>更新フィード

Microsoft Azure Web サイトには、新しく利用可能になった機能が製品別に一覧表示され、さらに、プレビュー中と開発中の機能が一覧表示されます。 最新の機能については [Hyperscale (Citus) の更新](https://azure.microsoft.com/updates/?category=databases&query=citus)に関するセクションをご覧ください。 RSS フィードはそのページでも利用できます。

## <a name="features-in-preview"></a>プレビュー段階の機能

Azure Database for PostgreSQL - Hyperscale (Citus) では、未リリースの機能のプレビューを提供しています。 プレビュー バージョンは、サービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。  詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

現在プレビュー向けに提供されている機能は次のとおりです。

* **[pgAudit](concepts-hyperscale-audit.md)** 。 PostgreSQL の標準的なログ記録機能を使用して、セッションおよびオブジェクトの詳細な監査ログを提供します。 特定の政府、財務、ISO 認定の監査を通過するうえで必要な監査ログが生成されます。
* **[プライベート アクセス](concepts-hyperscale-private-access.md)** 。
  仮想ネットワーク (VNet) 上のホストが、プライベート エンドポイント経由で Hyperscale (Citus) サーバー グループに安全にアクセスできます。

> [!NOTE]
>
> プライベート アクセスは、[特定のリージョン](concepts-hyperscale-limits.md#regions)でのみプレビューで使用できます。

## <a name="contact-us"></a>お問い合わせ

プレビュー機能を使用した感想をメール ([Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)) でお寄せください。
(このメール アドレスはテクニカル サポートチャネルのエイリアスではありません。 技術的な問題については、[サポート リクエスト](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を開いてください。)
