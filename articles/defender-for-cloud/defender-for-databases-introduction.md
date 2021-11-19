---
title: Microsoft Defender for open-source relational databases - 利点と機能
description: PostgreSQL、MySQL、MariaDB などのオープンソース リレーショナル データベース用の Microsoft Defender の利点と機能について説明します
author: memildin
ms.author: memildin
ms.date: 11/09/2021
ms.topic: overview
ms.service: defender-for-cloud
manager: rkarlin
ms.openlocfilehash: 200aa7e3cbc4b65b4671c3ce41a8bbbc8aa31394
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132525579"
---
# <a name="introduction-to-microsoft-defender-for-open-source-relational-databases"></a>Microsoft Defender for open-source relational databases の概要

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

このプランを使用すると、次のオープンソース リレーショナル データベースに脅威の防止が提供されます。

- [Azure Database for PostgreSQL](../postgresql/index.yml)
- [Azure Database for MySQL](../mysql/index.yml)
- [Azure Database for MariaDB](../mariadb/index.yml)

Defender for Cloud によって、通常とは異なる、害を及ぼす可能性のあるデータベースへのアクセスやその悪用の試みを示す不自然なアクティビティが検出されます。 このプランを使用すると、データベースに対する潜在的な脅威に簡単に対処でき、セキュリティの専門家である必要や、高度なセキュリティ監視システムを管理する必要はありません。

## <a name="availability"></a>可用性

| 側面                             | 詳細                                                                                                                                    |
|------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------|
| リリース状態:                     | 一般公開 (GA)                                                     |
| 価格:                           | **Microsoft Defender for open-source relational databases** の課金については、[課金に関するページ](https://azure.microsoft.com/pricing/details/security-center/)をご覧ください。   |
| PostgreSQL の保護されたバージョン:  | 単一サーバー - 汎用とメモリ最適化。 詳細については、[PostgreSQL の価格レベル](../postgresql/concepts-pricing-tiers.md)に関する記事を参照してください。   |
| MySQL の保護されたバージョン:       | 単一サーバー - 汎用とメモリ最適化。 詳細については、[MySQL の価格レベル](../mysql/concepts-pricing-tiers.md)に関する記事を参照してください。                        |
| MariaDB の保護されたバージョン:     | 汎用とメモリ最適化。 詳細については、[MariaDB の価格レベル](../mariadb/concepts-pricing-tiers.md)に関する記事を参照してください。                      |
| クラウド:                            | :::image type="icon" source="./media/icons/yes-icon.png"::: 商用クラウド<br>:::image type="icon" source="./media/icons/no-icon.png"::: 各国 (Azure Government、Azure China 21Vianet) |
|                                    |                                                                                                                                            |

## <a name="what-are-the-benefits-of-microsoft-defender-for-open-source-relational-databases"></a>Microsoft Defender for open-source relational databases の利点

Defender for Cloud では、異常なアクティビティに関するセキュリティ アラートが提供されているため、潜在的な脅威を検出し、発生した脅威に対応できます。

このプランを有効にすると、Defender for Cloud により、異常なデータベース アクセスとクエリ パターン、および不審なデータベース アクティビティが検出されたときにアラートが提供されます。

これらのアラートは、Defender for Cloud のセキュリティ アラート ページに表示され、次のものが含まれます。

- それらをトリガーした疑わしいアクティビティの詳細
- 関連付けられている MITRE ATT&CK 戦術
- 脅威を調査して軽減する方法に関して推奨されるアクション
- Microsoft Sentinel で調査を続けるためのオプション

:::image type="content" source="media/defender-for-databases-introduction/defender-alerts.png" alt-text="Microsoft Defender for open-source relational database sによって保護されているデータベースで表示される可能性があるいくつかのセキュリティ アラート。" lightbox="./media/defender-for-databases-introduction/defender-alerts.png":::

## <a name="what-kind-of-alerts-does-microsoft-defender-for-open-source-relational-databases-provide"></a>Microsoft Defender for open-source relational databases によって提供されるアラートの種類

脅威インテリジェンスによってエンリッチされたセキュリティ アラートは、次のような場合にトリガーされます。

- **異常なデータベース アクセスとクエリ パターン** - さまざまな資格情報でのサインイン試行の失敗が異常に多い場合 (ブルートフォース試行) など
- **不審なデータベース アクティビティ** - 正当なユーザーが、クリプトマイニング C&C サーバーとの通信を行う侵害されたコンピューターから SQL Server にアクセスするなど
- **ブルート フォース攻撃** – 単純なブルート フォースと、有効なユーザーに対するブルート フォースまたは成功したブルート フォースを分離する機能

> [!TIP]
> データベース サーバーのセキュリティ アラートの完全な一覧については、[アラートのリファレンス ページ](alerts-reference.md#alerts-osrdb)を参照してください。



## <a name="next-steps"></a>次のステップ

この記事では、Microsoft Defender for open-source relational databases について説明しました。

> [!div class="nextstepaction"]
> [強化された保護を有効にする](enable-enhanced-security.md)
