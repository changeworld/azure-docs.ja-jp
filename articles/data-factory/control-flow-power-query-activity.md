---
title: Azure Data Factory での Power Query アクティビティ
description: Data Factory パイプラインでデータ ラングリング機能のために Power Query アクティビティを使用する方法について説明します。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: 07e663b7b631cbc083916e48924d740255e46e37
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128638769"
---
# <a name="power-query-activity-in-azure-data-factory"></a>Azure Data Factory での Power Query アクティビティ

Power Query アクティビティを使用すると、Data Factory パイプラインで大規模なデータ ラングリングを実行するために、Power Query マッシュアップをビルドして実行できます。 新しい Power Query マッシュアップを作成するには、[New resources]\(新しいリソース\) メニュー オプションを使用するか、パイプラインに Power アクティビティを追加します。

:::image type="content" source="media/data-flow/power-query-wrangling.png" alt-text="ファクトリ リソース ペインでの Power Query を示すスクリーンショット。":::

以前は、Azure Data Factory でのデータ ラングリングは、[データ フロー] メニュー オプションから作成しました。 これは、新しい Power Query アクティビティからの作成に変更されました。 Power Query マッシュアップ エディターの内部で直接操作を行い、対話型データ探索を実行して、作業内容を保存できます。 完了後、Power Query アクティビティを取得して、パイプラインに追加できます。 Azure Data Factory は、Azure Data Factory のデータ フロー Spark 環境を使用して、これを自動的にスケールアウトし、データ ラングリングを運用可能にします。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFYn]

## <a name="translation-to-data-flow-script"></a>データ フロー スクリプトへの変換

Power Query アクティビティを使用したスケーリングを実現するために、Azure Data Factory は、```M``` スクリプトをデータ フロー スクリプトに変換します。これにより、Azure Data Factory のデータ フロー Spark 環境を使用して Power Query を大規模に実行できます。 コーディング不要のデータ準備を使用して、ラングリング データ フローを作成します。 使用できる関数の一覧については、[変換関数](wrangling-functions.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

[Azure Data Factory で Power Query](wrangling-tutorial.md) を使用するデータ ラングリングの概念について詳しく学習してください。
