---
title: 列の追加:モジュール リファレンス
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service で列の追加モジュールを使用して、2 つのデータセットを連結する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: cbb1249b529fde1368bfaff0c6c251c93fa6c309
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693858"
---
# <a name="add-columns-module"></a>列の追加モジュール

この記事では、Azure Machine Learning service のビジュアル インターフェイス (プレビュー) のモジュールについて説明します。

このモジュールを使用して、2 つのデータセットを連結します。 入力として指定した 2 つのデータセットのすべての列を結合し、単一のデータセットを作成します。 3 つ以上のデータセットを連結する必要がある場合は、**列の追加**の複数のインスタンスを使用します。



## <a name="how-to-configure-add-columns"></a>列の追加の構成方法
1. **Add Columns (列の追加)** モジュールをパイプラインに追加します。

2. 連結する 2 つのデータセットを接続します。 3 つ以上のデータセットを結合したい場合は、**列の追加**の複数の組み合わせを連結できます。

    - 異なる数の行を持つ 2 つの列を結合できます。 出力データセットには、より小さいソース列の各行の欠損値が埋め込まれます。

    - 個々の列を選択して追加することはできません。 **列の追加**を使用すると、各データセットのすべての列が連結されます。 このため、列のサブセットのみを追加する場合は、"データセット内の列の選択" を使用して、必要な列でデータセットを作成します。

3. パイプラインを実行します。

### <a name="results"></a>結果
パイプラインの実行後:

- 新しいデータセットの最初の行を表示するには、**列の追加**の出力を右クリックして、[Visualize]\(視覚化\) を選択します。

新しいデータセットの列数は、両方の入力データセットの列の合計数と等しくなります。

入力データセットに同じ名前の 2 つの列がある場合は、列の名前に数値のサフィックスが追加されます。 たとえば、TargetOutcome という名前の列のインスタンスが 2 つある場合、左側の列の名前が TargetOutcome_1 に変更され、右側の列の名前が TargetOutcome_2 に変更されます。

## <a name="next-steps"></a>次の手順

Azure Machine Learning service で[使用できる一連のモジュール](module-reference.md)を参照してください。 