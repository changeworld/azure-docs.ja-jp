---
title: セキュリティ概要 - Hyperscale (Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL - Hyperscale (Citus) の情報保護とネットワーク セキュリティ。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: dd3d359143185eb4d33ddce6396b7c69288fe841
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084333"
---
# <a name="security-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL - Hyperscale (Citus) のセキュリティ

このページでは、Hyperscale (Citus) サーバー グループのデータ保護に利用できる複数のセキュリティ層について説明します。 

## <a name="information-protection-and-encryption"></a>情報の保護と暗号化

### <a name="in-transit"></a>転送中のデータ

データがノードに取り込まれるときは常に、Hyperscale (Citus) では、トランスポート層セキュリティ 1.2 で転送中のデータを暗号化することでデータが保護されます。 暗号化 (SSL/TLS) は常に適用され、無効にできません。

### <a name="at-rest"></a>保存中

Hyperscale (Citus) サービスでは、保存データのストレージ暗号化に FIPS 140-2 認証済みの暗号モジュールが使用されます。 データ (バックアップを含む) は、クエリの実行中に作成される一時ファイルも含めて、ディスク上で暗号化されます。
このサービスでは、Azure ストレージ暗号化に含まれる AES 256 ビット暗号が使用され、キーはシステムによって管理されます。 ストレージの暗号化は常にオンになっており、無効にできません。

## <a name="network-security"></a>ネットワークのセキュリティ

[!INCLUDE [azure-postgresql-hyperscale-access](../../includes/azure-postgresql-hyperscale-access.md)]

## <a name="limits-and-limitations"></a>制限と制限事項

詳細については、Hyperscale (Citus) の[制限と制限事項](concepts-hyperscale-limits.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* [プライベート アクセスを有効にし、管理する](howto-hyperscale-private-access.md)方法を学習する (プレビュー)
* [プライベート エンドポイント](../private-link/private-endpoint-overview.md)について学習する
* [仮想ネットワーク](../virtual-network/concepts-and-best-practices.md)について学習する
* [プライベート DNS ゾーン](../dns/private-dns-overview.md)について学習する