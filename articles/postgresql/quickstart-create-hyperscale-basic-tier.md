---
title: 'クイックスタート: Basic レベルのサーバー グループを作成する - Hyperscale (Citus) - Azure Database for PostgreSQL'
description: Azure Database for PostgreSQL Hyperscale (Citus) Basic レベルを使ってみます。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 04/07/2021
ms.openlocfilehash: 07ace217e5299662a1c3145a225abc25f4f1f337
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023881"
---
# <a name="create-a-hyperscale-citus-basic-tier-server-group-in-the-azure-portal"></a>Azure portal で Hyperscale (Citus) Basic レベルのサーバー グループを作成する

Azure Database for PostgreSQL - Hyperscale (Citus) は、高可用性の PostgreSQL データベースをクラウドで実行、管理、スケーリングできるマネージド サービスです。 [Basic レベル](concepts-hyperscale-tiers.md)は、初期開発やテストに便利なデプロイ オプションです。

このクイックスタートでは、Azure portal を使用して Hyperscale (Citus) Basic レベルのサーバー グループを作成する方法について説明します。 サーバー グループをプロビジョニングした後、そこに接続してクエリを実行できることを検証します。

> [!IMPORTANT]
> Hyperscale (Citus) Basic レベルは現在プレビュー段階です。  このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
>
> その他の新機能については、[Hyperscale (Citus) のプレビュー機能](hyperscale-preview-features.md)に関するページで全一覧をご覧いただけます。

[!INCLUDE [azure-postgresql-hyperscale-create-basic-tier](../../includes/azure-postgresql-hyperscale-create-basic-tier.md)]

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Hyperscale (Citus) サーバー グループのプロビジョニング方法を学習しました。 そのサーバー グループに psql で接続し、スキーマを作成して、データを分散しました。

- チュートリアルに従って[スケーラブルなマルチテナント アプリケーションを構築する](./tutorial-design-database-hyperscale-multi-tenant.md)
- サーバー グループに最適な[初期サイズ](howto-hyperscale-scale-initial.md)を決定する
