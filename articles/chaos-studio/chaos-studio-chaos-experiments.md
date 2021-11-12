---
title: Azure Chaos Studio におけるカオス実験
description: Azure Chaos Studio におけるカオス実験の概念を理解します。 カオス実験を構成する要素は何でしょうか。 カオス実験を作成するにはどうすればよいでしょうか。
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: conceptual
ms.date: 11/01/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: ef71d6d09d1965eefd7008dd8c3d020c0a8ae1ae
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371545"
---
# <a name="chaos-experiments"></a>カオス実験

Chaos Studio で、カオス実験を作成して実行します。 カオス実験とは、実行する必要がある障害と、それらの障害を実行する必要がある対象のリソースを記述する Azure リソースです。 実験は、次の 2 つのセクションに分かれます。
- **セレクター**: セレクターとは、障害またはその他のアクションを実行するターゲット リソースのグループです。 セレクターを使用すると、リソースを論理的にグループ化して、複数のアクション全体で再利用できます。 たとえば、"AllNonProdEastUSVMs" というセレクターを設定して、そこに、米国東部のすべての非運用仮想マシンを追加する場合があります。 このセレクターを参照すれば、CPU 負荷を適用した後に、仮想メモリ負荷をこれらの仮想マシンに適用することができます。
- **ロジック**: 実験の残りの部分には、障害を実行する方法とタイミングについて記述します。 実験は、1 つずつ実行する **ステップ** に分けます。 各ステップには、同時に実行する **分岐** を 1 つ以上含めます。 ステップと分岐を使用することで、環境内のリソース全体で複数の障害を並行して挿入することができます。 各分岐には、実行したい障害または時間遅延のいずれかの **アクション** を 1 つ以上含めます。 **障害** とは、何らかの中断を引き起こすアクションです。 ほとんどの障害は、障害の実行期間や適用するストレスの量など、1 つ以上の **パラメーター** を取ります。

![カオス実験のレイアウトを示す図。](images/chaos-experiment.png)

## <a name="cross-subscription-and-cross-tenant-experiments"></a>複数のサブスクリプションにまたがり、複数のテナントにまたがる実験

カオス実験とは、サブスクリプション、リソース グループ、リージョンに配置される Azure リソースです。 Azure portal または Chaos Studio REST API を使用して、実験の作成、更新、開始、キャンセル、および状態表示を行うことができます。

カオス実験では、サブスクリプションが同じ Azure テナント内にある限り、その実験とは異なるサブスクリプションのリソースをターゲットにすることができます。 カオス実験では、リージョンが Chaos Studio でサポートされているリージョンである限り、その実験とは異なるリージョンのリソースをターゲットにすることができます。

## <a name="next-steps"></a>次のステップ
カオス実験について理解したので、次の準備が整いました。
- [障害とアクションについて](chaos-studio-faults-actions.md)
- [最初の実験を作成して実行する](chaos-studio-tutorial-service-direct-portal.md)
