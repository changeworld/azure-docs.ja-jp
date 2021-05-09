---
title: サポートされるバージョン - Azure Database for PostgreSQL - フレキシブル サーバー
description: Azure Database for PostgreSQL - フレキシブル サーバーでサポートされる、PostgreSQL のメジャーおよびマイナー バージョンについて説明します。
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/22/2021
ms.openlocfilehash: cd78d6802f6b7f71794785d9149b5cda854bf21e
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108166417"
---
# <a name="supported-postgresql-major-versions-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - フレキシブル サーバーでサポートされる Postgres のメジャー バージョン

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーはプレビュー段階です

Azure Database for PostgreSQL - フレキシブル サーバーでは、現在、以下のメジャー バージョンがサポートされています。

## <a name="postgresql-version-12"></a>PostgreSQL バージョン 12

現在のマイナー リリースは **12.6** です。 このマイナー リリースの機能強化と修正の詳細については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/12/static/release-12-6.html)を参照してください。 新しいサーバーは、このマイナー バージョンで作成されます。 既存のサーバーは、今後予定されているメンテナンス期間中に、サポートされている最新のマイナー バージョンに自動的にアップグレードされます。

## <a name="postgresql-version-11"></a>PostgreSQL バージョン 11

現在のマイナー リリースは **11.11** です。 このマイナー リリースの機能強化と修正の詳細については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/11/static/release-11-11.html)を参照してください。 新しいサーバーは、このマイナー バージョンで作成されます。 既存のサーバーは、今後予定されているメンテナンス期間中に、サポートされている最新のマイナー バージョンに自動的にアップグレードされます。

## <a name="postgresql-version-10-and-older"></a>PostgreSQL バージョン 10 以前

Azure Database for PostgreSQL - フレキシブル サーバーでは、PostgreSQL バージョン 10 以前はサポートされていません。 古いバージョンが必要な場合は、[シングル サーバー](../concepts-supported-versions.md)のデプロイ オプションを使用してください。

## <a name="managing-upgrades"></a>アップグレードの管理

PostgreSQL プロジェクトでは、報告されたバグを修正するためにマイナー リリースを定期的に発行しています。 Azure Database for PostgreSQL は、サービスの月次デプロイ中に、マイナー リリースのサーバーに自動的に修正を適用します。

メジャー バージョンのアップグレードの自動化はまだサポートされていません。 たとえば、PostgreSQL 11 から PostgreSQL 12 への自動アップグレードは、現在は存在しません。<!-- To upgrade to the next major version, create a [database dump and restore](howto-migrate-using-dump-and-restore.md) to a server that was created with the new engine version.-->

<!--
## Next steps

For information on supported PostgreSQL extensions, see [the extensions document](concepts-extensions.md).
-->