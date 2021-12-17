---
title: マッピング データ フローの複数の分岐
titleSuffix: Azure Data Factory & Azure Synapse
description: 複数の分岐を持つマッピング データ フローでのデータ ストリームのレプリケート
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 05aa62c32054f7a5e9ab75367f3d07c6f9223506
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060026"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>マッピング データ フローでの新しい分岐の作成

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

新しい分岐を追加して、同じデータ ストリームに対して複数の操作と変換を行います。 新しい分岐を追加すると、複数のシンクや自己結合データに対して同じソースをまとめて使用する場合に便利です。

他の変換と同様に、変換リストから新しい分岐を追加できます。 **[New Branch]** (新しい分岐) は、分岐しようとしている変換の後に既存の変換が行われる場合にのみ、アクションとして利用可能になります。

:::image type="content" source="media/data-flow/new-branch2.png" alt-text="[Multiple inputs/outputs]\(複数の入出力\) メニューの [New branch]\(新しい分岐\) オプションを示しているスクリーンショット。":::

次の例では、データ フローがタクシー乗車データを読み取っています。 曜日とベンダーの両方で集計された出力が必要です。 同じソースから読み取る 2 つの異なるデータ フローを作成するのではなく、新しい分岐を追加できます。 これにより、両方の集計を同じデータ フローの一部として実行できます。 

:::image type="content" source="media/data-flow/new-branch.png" alt-text="ソースからの 2 つの分岐があるデータ フローを示しているスクリーンショット。":::

> [!NOTE]
> 正符号 (+) をクリックして変換をグラフに追加した場合、後続の変換ブロックがある場合にのみ [New Branch]\(新しい分岐\) オプションが表示されます。 これは、[New Branch]\(新しい分岐\) によって既存のストリームへの参照が作成され、さらにアップストリーム処理を実行する必要があるためです。 [New Branch]\(新しい分岐\) オプションが表示されない場合、最初に派生列または他の変換を追加し、その後、前のブロックに戻ります。これにより、オプションとして [New Branch]\(新しい分岐\) が表示されます。

## <a name="next-steps"></a>次のステップ

分岐後、[データ フロー変換](data-flow-transformation-overview.md)の使用が必要になる場合があります。
