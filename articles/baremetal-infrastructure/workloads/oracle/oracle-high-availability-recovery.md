---
title: Azure BareMetal インフラストラクチャで Oracle データベースを復旧する
description: Azure BareMetal インフラストラクチャで Oracle データベースを復旧する方法について説明します。
ms.topic: reference
ms.subservice: baremetal-oracle
ms.date: 04/15/2021
ms.openlocfilehash: c93159476403ac01ea9622b438fb36e5c423ecb4
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2021
ms.locfileid: "110578484"
---
# <a name="recover-your-oracle-database-on-azure-baremetal-infrastructure"></a>Azure BareMetal インフラストラクチャで Oracle データベースを復旧する

1 つのテクノロジですべての障害シナリオから保護することはできませんが、機能を組み合わせることによって、データベース管理者はほぼすべての状況でデータベースを復旧することができます。

## <a name="causes-of-database-failure"></a>データベース障害の原因

データベースの障害はさまざまな理由で発生する可能性がありますが、通常はいくつかのカテゴリに分類されます。

- データ操作のエラー。
- オンライン再実行ログの消失。
- データベース コントロール ファイルの消失。
- データベース データファイルの消失。
- 物理的なデータの破損。

## <a name="choose-your-method-of-recovery"></a>復旧方法を選択する

復旧の種類は、障害の種類によって異なります。 たとえば、オブジェクトが削除された、またはデータが誤って変更されたとします。 その場合、最も迅速な解決策は、通常、フラッシュバック データベース操作を実行することです。 場合によっては、Azure NetApp Files スナップショットを使用して復旧することで、必要な回復を実現できることがあります。 次の図のデシジョン ツリーは、上記で説明したすべてのデータ保護オプションが実装されている場合の一般的な障害と復旧のシナリオを示しています。

:::image type="content" source="media/oracle-high-availability/db-recovery-decision-tree.png" alt-text="データベース復旧デシジョン ツリーの図。" lightbox="media/oracle-high-availability/db-recovery-decision-tree.png":::

このデシジョン ツリーの例は、データベース管理者の視点のみを示していることに注意してください。 各デプロイには、選択順序の変更につながる異なる要件がある場合があります。 たとえば、Data Guard を使用して別のリージョンへのデータベース ロールの切り替えを実行すると、アプリケーションのパフォーマンスに悪影響を及ぼす可能性があります。 これにより、スナップショットによる復旧方法の RTO が短縮できる可能性もあります。 RTO と RPO の要件が確実に満たされるようにするために、これらの操作を実行し、必要なときにこれらを実行すための手順を文書化しておくことをお勧めします

## <a name="next-steps"></a>次のステップ

BareMetal インフラストラクチャの詳細を確認してください。

- [Azure の BareMetal インフラストラクチャとは](../../concepts-baremetal-infrastructure-overview.md)
- [Azure で BareMetal インフラストラクチャ インスタンスを接続する](../../connect-baremetal-infrastructure.md)
