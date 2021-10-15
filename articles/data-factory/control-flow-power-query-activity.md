---
title: Azure Data Factory での Power Query アクティビティ
description: Data Factory パイプラインでデータ ラングリング機能のために Power Query アクティビティを使用する方法について説明します。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: be5c2cbe0d4f364abb68ad8b01f00ca2c8396806
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129358542"
---
# <a name="power-query-activity-in-azure-data-factory"></a>Azure Data Factory での Power Query アクティビティ

Power Query アクティビティを使用すると、Data Factory パイプラインで大規模なデータ ラングリングを実行するために、Power Query マッシュアップをビルドして実行できます。 新しい Power Query マッシュアップを作成するには、[New resources]\(新しいリソース\) メニュー オプションを使用するか、パイプラインに Power アクティビティを追加します。

![ファクトリ リソース ペインでの Power Query を示すスクリーンショット。](media/data-flow/power-query-activity-1.png)

Power Query マッシュアップ エディターの内部で直接操作を行い、対話型データ探索を実行して、作業内容を保存できます。 完了後、Power Query アクティビティを取得して、パイプラインに追加できます。 Azure Data Factory は、Azure Data Factory のデータ フロー Spark 環境を使用して、これを自動的にスケールアウトし、データ ラングリングを運用可能にします。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFYn]

## <a name="translation-to-data-flow-script"></a>データ フロー スクリプトへの変換

Power Query アクティビティを使用したスケーリングを実現するために、Azure Data Factory は、```M``` スクリプトをデータ フロー スクリプトに変換します。これにより、Azure Data Factory のデータ フロー Spark 環境を使用して Power Query を大規模に実行できます。 コーディング不要のデータ準備を使用して、ラングリング データ フローを作成します。 使用できる関数の一覧については、[変換関数](wrangling-functions.md)に関するページを参照してください。

## <a name="settings"></a>設定

* Power Query: 既存の Power Query を選択して実行するか、新しいものを作成します。
* Run on Azure IR (Azure IR 上で実行する): 既存の Azure Integration Runtime を選択して Power Query のコンピューティング環境を定義するか、新しいものを作成します。
* コンピューティングの種類: 既定の自動解決の統合ランタイムを選択した場合は、Power Query の実行に使用する Spark クラスターのコンピューティングに適用するコンピューティングの種類を選択できます。
* コア数: 既定の自動解決の統合ランタイムを選択した場合は、Power Query の実行に使用する Spark クラスターのコンピューティングに適用するコア数を選択できます。

## <a name="sink"></a>シンク

Spark 上で Power Query M スクリプトが実行された後、変換されたデータのランディングに使用するデータセットを選択します。 シンクの構成の詳細については、[データ フロー シンク](data-flow-sink.md)のドキュメントを参照してください。

## <a name="mapping"></a>マッピング

[マッピング] タブでは、Power Query アクティビティの出力から、選択したシンクのターゲット スキーマへの列マッピングを構成することができます。 列マッピングの詳細については、[データ フロー シンクのマッピングのドキュメント](data-flow-sink.md#field-mapping)を参照してください。

## <a name="next-steps"></a>次のステップ

[Azure Data Factory で Power Query](wrangling-tutorial.md) を使用するデータ ラングリングの概念について詳しく学習してください。
