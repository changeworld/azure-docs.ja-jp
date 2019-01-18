---
title: Azure Cosmos DB での競合解決の種類と解決ポリシー
description: この記事では、Azure Cosmos DB の競合カテゴリと競合解決ポリシーについて説明します。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 4af3f4c60f186c34d0f5522461ba70c68b59d1ab
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54033895"
---
# <a name="conflict-types-and-resolution-policies"></a>競合のタイプと解決ポリシー

競合と競合解決ポリシーは、複数の書き込みリージョンを使用して Azure Cosmos DB アカウントが構成されている場合に該当します。

複数の書き込みリージョンで構成されている Azure Cosmos DB アカウントの場合、ライターが複数のリージョンで同じ項目を同時に更新するときに更新の競合が発生します。 更新の競合は次の 3 種類に分類されます。

* **挿入の競合**:これらの競合は、アプリケーションが同じ一意のインデックスを持つ複数の項目を複数のリージョンから同時に挿入した場合に発生することがあります。 たとえば、この競合は ID プロパティで発生する場合があります。 最初は、それぞれのローカル リージョンですべての書き込みが成功する可能性があります。 しかし、最後には、選択されている競合解決ポリシーに基づいて、元の ID を持つ 1 つの項目のみがコミットされます。

* **置換の競合:**:アプリケーションが複数のリージョンから 1 つの項目を同時に更新するときに発生することがあります。

* **削除の競合:**:アプリケーションが 1 つのリージョンから項目を削除し、同時に別のリージョンから項目を更新するときに発生することがあります。

## <a name="conflict-resolution-policies"></a>競合解決ポリシー

Azure Cosmos DB では、更新の競合を解決するための柔軟なポリシー主導のメカニズムが提供されています。 Azure Cosmos DB コンテナーでは、2 つの競合解決ポリシーから選択できます。

- **最後の書き込みが有効 (LWW)**: この解決ポリシーでは、システム定義のタイムスタンプ プロパティが既定で使用されます。 時刻同期クロック プロパティに基づきます。 Azure Cosmos DB SQL API を使用している場合、競合解決に使用する他のカスタムの数値型プロパティを指定できます。 カスタム数値型プロパティは、競合解決パスとも呼ばれます。 

  2 つ以上の項目が挿入または置換操作で競合する場合、競合解決パスで最も高い値を持つ項目が勝者になります。 複数の項目の競合解決パスの数値が同じ場合、勝者はシステムによって決定されます。 すべてのリージョンは単一の勝者に収束し、結果として同じバージョンのコミット済み項目になることが保証されます。 削除の競合がある場合、削除されるバージョンは挿入または置換の競合より常に優先されます。 この結果は、競合解決パスの値に関係なく発生します。

  > [!NOTE]
  > "最後の書き込みが有効" が、既定の競合解決ポリシーです。 SQL、Azure Cosmos DB Table、MongoDB、Cassandra、Gremlin API の各アカウントで使用できます。

  詳しくは、[LWW 競合解決ポリシーを使用する例](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)に関する記事をご覧ください。

- **カスタム**:この解決ポリシーは、競合の調整のためのアプリケーション定義のセマンティクス用に設計されています。 Azure Cosmos DB コンテナーにこのポリシーを設定するときは、マージ ストアド プロシージャも登録する必要があります。 このプロシージャは、サーバーのデータベース トランザクションで競合が検出されると自動的に呼び出されます。 システムにより、コミットメント プロトコルの一部としてマージ プロシージャの実行が 1 回だけとなることが保証されます。  

  カスタム解決オプションを使用してコンテナーを構成する場合に憶えておくべき点が 2 つがあります。 コンテナーでのマージ プロシージャの登録が失敗する場合、またはマージ プロシージャが実行時に例外をスローする場合は、競合は競合フィードに書き込まれます。 このときは、アプリケーションにおいて競合フィード内の競合を手動で解決する必要があります。 詳しくは、[カスタム解決ポリシーの使用と競合フィードを使用する方法の例](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)に関する記事をご覧ください。

  > [!NOTE]
  > カスタム競合解決ポリシーは、SQL API アカウントでのみ使用できます。

## <a name="next-steps"></a>次の手順

競合解決ポリシーを構成する方法について学習します。 次の記事を参照してください。

* [LWW 競合解決ポリシーを使用する](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [カスタム競合解決ポリシーを使用する](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [競合フィードを使用する](how-to-manage-conflicts.md#read-from-conflict-feed)
