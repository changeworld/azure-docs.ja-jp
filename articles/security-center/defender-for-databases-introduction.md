---
title: オープンソース リレーショナル データベース用 Azure Defender - 利点と機能
description: PostgreSQL、MySQL、MariaDB などのオープンソース リレーショナル データベース用の Azure Defender の利点と機能について説明します
author: memildin
ms.author: memildin
ms.date: 05/25/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: eddab19cb46d27d0d48d1f2376cd9507b28cefc8
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2021
ms.locfileid: "113003622"
---
# <a name="introduction-to-azure-defender-for-open-source-relational-databases"></a>オープンソース リレーショナル データベース用 Azure Defender の概要

この Azure Defender プランを使用すると、次のオープンソース リレーショナル データベースに脅威の保護が提供されます。

- [Azure Database for PostgreSQL](../postgresql/index.yml)
- [Azure Database for MySQL](../mysql/index.yml)
- [Azure Database for MariaDB](../mariadb/index.yml)

Azure Defender により、データベースへのアクセスやその悪用を試みる、異常で有害と考えられる不自然な動作が検出されます。 このプランを使用すると、データベースに対する潜在的な脅威に簡単に対処でき、セキュリティの専門家である必要や、高度なセキュリティ監視システムを管理する必要はありません。

## <a name="availability"></a>可用性

| 側面                             | 詳細                                                                                                                                    |
|------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------|
| リリース状態:                     | 一般提供 (GA)                                                     |
| 価格:                           | **オープンソース リレーショナル データベース用 Azure Defender** は、[Security Center の価格](https://azure.microsoft.com/pricing/details/security-center/)で示されているように課金されます   |
| PostgreSQL の保護されたバージョン:  | 単一サーバー - 汎用とメモリ最適化。 詳細については、[PostgreSQL の価格レベル](../postgresql/concepts-pricing-tiers.md)に関する記事を参照してください。   |
| MySQL の保護されたバージョン:       | 単一サーバー - 汎用とメモリ最適化。 詳細については、[MySQL の価格レベル](../mysql/concepts-pricing-tiers.md)に関する記事を参照してください。                        |
| MariaDB の保護されたバージョン:     | 汎用とメモリ最適化。 詳細については、[MariaDB の価格レベル](../mariadb/concepts-pricing-tiers.md)に関する記事を参照してください。                      |
| クラウド:                            | ![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![No](./media/icons/no-icon.png) National/Sovereign (US Gov、Azure China) |
|                                    |                                                                                                                                            |

## <a name="what-are-the-benefits-of-azure-defender-for-open-source-relational-databases"></a>オープンソース リレーショナル データベース用 Azure Defender の利点

Azure Defender を使用すると、異常なアクティビティに関するセキュリティ アラートが提供されるので、潜在的な脅威を検出し、発生した脅威に対応できます。

このプランを有効にすると、Azure Defender により、異常なデータベース アクセスとクエリ パターン、および疑わしいデータベース アクティビティが検出されると、アラートが提供されます。

これらのアラートは、Azure Defender のセキュリティ アラート ページに表示され、次のものが含まれます。

- それらをトリガーした疑わしいアクティビティの詳細
- 関連付けられている MITRE ATT&CK 戦術
- 脅威を調査して軽減する方法に関して推奨されるアクション
- Azure Sentinel で調査を続けるためのオプション

:::image type="content" source="media/defender-for-databases-introduction/defender-alerts.png" alt-text="オープンソース リレーショナル データベース用 Azure Defender によって保護されているデータベースで表示される可能性があるいくつかのセキュリティ アラート。" lightbox="./media/defender-for-databases-introduction/defender-alerts.png":::

## <a name="what-kind-of-alerts-does-azure-defender-for-open-source-relational-databases-provide"></a>オープンソース リレーショナル データベース用 Azure Defender によって提供されるアラートの種類

脅威インテリジェンスによってエンリッチされたセキュリティ アラートは、次のような場合にトリガーされます。

- **異常なデータベース アクセスとクエリ パターン** - さまざまな資格情報でのサインイン試行の失敗が異常に多い場合 (ブルートフォース試行) など
- **不審なデータベース アクティビティ** - 正当なユーザーが、クリプトマイニング C&C サーバーとの通信を行う侵害されたコンピューターから SQL Server にアクセスするなど
- **ブルート フォース攻撃** – 単純なブルート フォースと、有効なユーザーに対するブルート フォースまたは成功したブルート フォースを分離する機能

> [!TIP]
> データベース サーバーのセキュリティ アラートの完全な一覧については、[アラートのリファレンス ページ](alerts-reference.md#alerts-osrdb)を参照してください。



## <a name="next-steps"></a>次のステップ

この記事では、オープンソース リレーショナル データベース用 Azure Defender について説明しました。

> [!div class="nextstepaction"]
> [Azure Defender を有効にする](enable-azure-defender.md)
