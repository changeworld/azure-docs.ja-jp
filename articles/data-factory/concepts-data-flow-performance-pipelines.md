---
title: マッピング データ フローでのパイプラインのパフォーマンスの最適化
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory と Azure Synapse Analytics のパイプラインでのデータ フローの実行を最適化する方法を説明します。
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.date: 09/29/2021
ms.openlocfilehash: 3545d7e8bbf8131c9fe454b39ea57a23cd233264
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129293509"
---
# <a name="using-data-flows-in-pipelines"></a>パイプラインでのデータ フローの使用 

複数のデータ フローを持つ複雑なパイプラインを構築する場合、論理フローがタイミングとコストに大きな影響を与える可能性があります。 このセクションでは、さまざまなアーキテクチャ戦略の影響について説明します。

## <a name="executing-data-flows-in-parallel"></a>データ フローの並列実行

複数のデータ フローを並列に実行すると、アクティビティごとに個別の Spark クラスターがサービスによってスピンアップされます。 これにより、各ジョブを分離して並列に実行できますが、複数のクラスターが同時に実行されることになります。

データ フローを並列実行する場合、使用されないウォーム プールが複数発生してしまうため、Azure IR の time to live プロパティを有効にしないことをお勧めします。

> [!TIP]
> 各アクティビティで同じデータ フローを複数回実行するのではなく、データ レイクにデータをステージして、ワイルドカード パスを使用してデータを 1 つのデータ フローで処理します。

## <a name="execute-data-flows-sequentially"></a>データ フローの順次実行

データ フロー アクティビティを順番に実行する場合は、Azure IR 構成で TTL を設定することをお勧めします。 サービスによってコンピューティング リソースが再利用され、クラスターの起動時間が短縮されます。 各アクティビティは引き続き分離され、実行のたびに新しい Spark コンテキストを受け取ります。 順次のアクティビティ間の時間をさらに短縮するには、Azure IR の **[クイック再利用]** チェックボックスをオンにして、サービスに既存のクラスターを再利用するように指示します。

## <a name="overloading-a-single-data-flow"></a>単一データ フローのオーバーロード

すべてのロジックを単一データ フロー内に配置すると、サービスによって単一の Spark インスタンスでジョブ全体が実行されます。 これはコストを削減する方法のように思えるかもしれませんが、さまざまな論理フローを組み合わせるため、監視やデバッグが困難になる可能性があります。 1 つのコンポーネントが失敗すると、ジョブの他のすべての部分も失敗します。 ビジネス ロジックの独立したフロー別にデータ フローを整理することをお勧めします。 データ フローが大きくなりすぎた場合、コンポーネントを分割すると、監視とデバッグが容易になります。 データ フロー内の変換の数にはハード制限はありませんが、多すぎるとジョブが複雑になります。

## <a name="execute-sinks-in-parallel"></a>シンクを並列実行する

データ フロー シンクの既定の動作では、各シンクが順番に実行され、シンクでエラーが発生した場合はデータ フローが失敗します。 さらに、データ フロー プロパティでシンクに異なる優先順位を設定しない限り、すべてのシンクは既定で同じグループに設定されます。

データ フローでは、UI デザイナーのデータ フロー プロパティのタブで、シンクをグループにまとめることができます。 シンクの実行順序を設定できるほか、同じグループ番号を使用してシンクをグループ化できます。 グループの管理に役立つように、シンクを同じグループで並列実行するようにサービスに要求できます。

シンクのプロパティ セクションにある、パイプラインのデータ フロー実行アクティビティでも、シンクの並列読み込みを有効にできます。 並列実行を有効にすると、データ フローは、接続されているシンクに (順次ではなく) 同時に書き込まれるように指示されます。 並列実行のオプションを利用するには、シンクがグループ化され、新しい分岐または条件分割を使用して同じストリームに接続されている必要があります。

## <a name="next-steps"></a>次のステップ

- [データ フローのパフォーマンスの概要](concepts-data-flow-performance.md)
- [ソースの最適化](concepts-data-flow-performance-sources.md)
- [シンクの最適化](concepts-data-flow-performance-sinks.md)
- [変換の最適化](concepts-data-flow-performance-transformations.md)

パフォーマンスに関する Data Flow のその他の記事を参照してください。

- [Data Flow のアクティビティ](control-flow-execute-data-flow-activity.md)
- [データ フローのパフォーマンスの監視](concepts-data-flow-monitoring.md)
- [Integration Runtime のパフォーマンス](concepts-integration-runtime-performance.md)
