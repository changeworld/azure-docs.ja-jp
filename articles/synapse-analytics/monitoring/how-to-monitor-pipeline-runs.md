---
title: Azure Synapse Studio (プレビュー) を使用してパイプラインの実行を監視する
description: Azure Synapse Studio を使用してワークスペースのパイプラインの実行を監視します。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 712dc62e29229f03dec12afdf18edbf55667dbdf
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427311"
---
# <a name="use-azure-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>Azure Synapse Studio を使用してワークスペースのパイプラインの実行を監視する

Azure Synapse Analytics では、ソリューション内のデータ移動、データ変換、コンピューティングのアクティビティを自動化および調整する複雑なパイプラインを作成できます。 これらのパイプラインを作成して監視するには、Azure Synapse Studio (プレビュー) を使用します。

この記事では、パイプラインの実行を監視する方法を説明します。これにより、パイプラインの最新の状態、問題、進捗状況を監視することができます。

## <a name="access-the-list-of-pipeline-runs"></a>パイプラインの実行の一覧にアクセスする

ワークスペースでパイプラインの実行の一覧を表示するには、最初に [Azure Synapse Studio を開き](https://web.azuresynapse.net/)、ワークスペースを選択します。

![ワークスペースへのログイン](./media/common/login-workspace.png)

ワークスペースを開いたら、左側の **[監視]** セクションを選択します。

![[監視] ハブを選択する](./media/common/left-nav.png)

**[パイプラインの実行]** を選択して、パイプラインの実行の一覧を表示します。

![[パイプラインの実行] を選択する](./media/how-to-monitor-pipeline-runs/monitor-hub-nav-pipelineruns.png)

## <a name="filtering-your-pipeline-runs"></a>パイプラインの実行のフィルター処理

パイプラインの実行の一覧をフィルター処理して、関心のあるものだけに絞り込むことができます。 画面の上部にあるフィルターを使用して、フィルター処理を行うフィールドを指定できます。

たとえば、ビューをフィルター処理して、"holiday" という名前のパイプラインのパイプラインの実行のみを表示できます。

![[フィルター] ボタン](./media/common/filter-button.png)

![サンプルのフィルター](./media/how-to-monitor-pipeline-runs/filter-example.png)

## <a name="viewing-details-about-a-specific-pipeline-run"></a>特定のパイプラインの実行の詳細を表示する

パイプラインの実行の詳細を表示するには、そのパイプラインの実行を選択します。 パイプラインの実行に関連付けられたアクティビティの実行が表示されます。 パイプラインがまだ実行中であれば、進捗状況を監視することができます。 
  
## <a name="next-steps"></a>次のステップ

アプリケーションの監視の詳細については、[Apache Spark アプリケーションの監視](how-to-monitor-spark-applications.md)に関する記事をご覧ください。 
