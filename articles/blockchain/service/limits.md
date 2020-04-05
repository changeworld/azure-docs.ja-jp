---
title: Azure Blockchain Service の制限事項
description: Azure Blockchain Service でのサービスと機能の制限に関する概要です
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: f4001ee520f3f3136d1bac5ca047c80526fc92e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455648"
---
# <a name="limits-in-azure-blockchain-service"></a>Azure Blockchain Service での制限事項

Azure Blockchain Service のサービスと機能には、メンバーが持つことのできるノードの数、コンソーシアムの制限、ストレージの量などの制限事項があります。

## <a name="pricing-tier"></a>Pricing tier

トランザクションと検証ノードの数の上限は、Azure Blockchain Service を Basic または Standard のどちらの価格レベルでプロビジョニングするかによって異なります。

| Pricing tier | トランザクション ノードの最大数 | 検証ノードの最大数 |
|:---|:---:|:---:|
| Basic | 10 | 1 |
| Standard | 10 | 2 |

メンバーの作成後に価格レベルを Basic と Standard の間で変更することはできません。

## <a name="storage-capacity"></a>ストレージの容量

台帳データおよびログ用にノードごとに使用できるストレージの最大量は、1.8 テラバイトです。

台帳およびログのストレージ サイズを減らすことはできません。

## <a name="consortium-limits"></a>コンソーシアムの制限

* Azure Blockchain Service では**コンソーシアム名とメンバー名は一意でなければなりません**。

* **メンバー名とコンソーシアム名は変更できません**

* **1 つのコンソーシアム内のすべてのメンバーは、同じ価格レベルである必要があります**

* **1 つのコンソーシアムに参加するすべてのメンバーは、同じリージョン内に存在する必要があります**

    コンソーシアムで最初のメンバーを作成するときに、リージョンを指定します。 コンソーシアムに招待されるメンバーは、最初のメンバーと同じリージョン内に存在する必要があります。 すべてのメンバーを同じリージョンに制限することにより、ネットワーク コンセンサスへの悪影響がありません。

* **コンソーシアムには少なくとも 1 人の管理者が必要です**

    コンソーシアムの管理者が 1 人だけの場合、別の管理者が追加されるか、コンソーシアムでレベル上げされるまで、管理者は自分自身やメンバーをコンソーシアムから削除することはできません。

* **コンソーシアムから削除されたメンバーを再び追加することはできません**

    代わりに、コンソーシアムへの参加を再度招待し、新しいメンバーを作成する必要があります。 トランザクションの履歴を保持するため、既存メンバーのリソースは削除されません。

* **1 つのコンソーシアム内のすべてのメンバーは、同じ台帳バージョンを使用する必要があります**

    Azure Blockchain Service で使用できる修正プログラムの適用、更新、および台帳バージョンについて詳しくは、「[Patching, updates, and versions (修正プログラムの適用、更新、およびバージョン)](ledger-versions.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

システムのパッチ適用とアップグレードに関するポリシーについて学習する - [修正プログラムの適用、更新、およびバージョン](ledger-versions.md)。
