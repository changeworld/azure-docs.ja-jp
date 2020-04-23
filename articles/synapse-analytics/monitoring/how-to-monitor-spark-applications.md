---
title: Apache Spark アプリケーションを監視する
description: Azure Synapse Studio を使用して Apache Spark アプリケーションを監視します。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: f231693fdcf3519e29eed38e47db52d92acc00fa
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427299"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>Azure Synapse Studio (プレビュー) を使用して Apache Spark アプリケーションを監視する

Azure Synapse Analytics では、Spark を使用して、ワークスペース内の Spark プールでノートブック、ジョブ、その他の種類のアプリケーションを実行できます。

この記事では、Spark アプリケーションを監視する方法を説明します。これにより、最新の状態、問題、進捗状況を監視することができます。

## <a name="accessing-the-list-of-spark-applications"></a>Spark アプリケーションの一覧にアクセスする

ワークスペースで Spark アプリケーションの一覧を表示するには、最初に [Azure Synapse Studio を開き](https://web.azuresynapse.net/)、ワークスペースを選択します。

  > [!div class="mx-imgBorder"]
  > ![ワークスペースへのログイン](./media/common/login-workspace.png)

ワークスペースを開いたら、左側の **[監視]** セクションを選択します。

  > [!div class="mx-imgBorder"]
  > ![[監視] ハブを選択する](./media/common/left-nav.png)

**[Spark アプリケーション]** を選択して、Spark アプリケーションの一覧を表示します。

  > [!div class="mx-imgBorder"]
  > ![[Spark applications]\(Spark アプリケーション\) を選択する](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-spark-applications"></a>Spark アプリケーションのフィルター処理

Spark アプリケーションの一覧をフィルター処理して、関心のあるものだけに絞り込むことができます。 画面の上部にあるフィルターを使用して、フィルター処理を行うフィールドを指定できます。

たとえば、ビューをフィルター処理して、名前に "sales" を含む Spark アプリケーションのみを表示することができます。

  > [!div class="mx-imgBorder"]
  > ![[フィルター] ボタン](./media/common/filter-button.png)

  > [!div class="mx-imgBorder"]
  > ![サンプルのフィルター](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-spark-application"></a>特定の Spark アプリケーションに関する詳細を表示する

いずれかの Spark アプリケーションの詳細を表示するには、その Spark アプリケーションを選択します。詳細が表示されます。 Spark アプリケーションがまだ実行中であれば、進捗状況を監視できます。

## <a name="next-steps"></a>次のステップ

パイプラインの実行を監視する方法の詳細については、[Azure Synapse Studio を使用してパイプラインの実行を監視する方法](how-to-monitor-pipeline-runs.md)に関する記事をご覧ください。  
