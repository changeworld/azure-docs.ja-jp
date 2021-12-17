---
title: Synapse Studio で Apache Spark プールを監視する方法
description: Synapse Studio を使用して Apache Spark プールを監視する方法について説明します。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: c6148f2bd5d3b1555ae61d2da3e922c9cfe632cb
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2021
ms.locfileid: "123539661"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-pools"></a>Synapse Studio を使用して Apache Spark プールを監視する

Azure Synapse Analytics では、Apache Spark を使用して、ワークスペース内の Apache Spark プールでノートブック、ジョブ、およびその他の種類のアプリケーションを実行できます。

この記事では、Apache Spark プールを監視する方法について説明します。この方法により、さまざまなワークスペース ユーザーによって使用中の仮想コアの数を含め、プールの状態を監視することができます。

## <a name="access-apache-spark-pools-list"></a>Apache Spark プールの一覧にアクセスする

ワークスペースで Apache Spark プールの一覧を表示するには、最初に [Synapse Studio を開き](https://web.azuresynapse.net/)、ワークスペースを選択します。

![ワークスペースへのログイン](./media/common/login-workspace.png)

ワークスペースを開いたら、左側の **[監視]** セクションを選択します。

![[監視] ハブを選択する](./media/common/left-nav.png)

Apache Spark プールの一覧を表示するには、 **[Apache Spark プール]** を選択します。

 ![Apache Spark プールを選択します](./media/how-to-monitor-spark-pools/monitor-hub-nav-spark-pools.png)

## <a name="filter-your-apache-spark-pools"></a>Apache Spark プールをフィルター処理する

Apache Spark プールの一覧をフィルター処理して、関心のあるものに絞ることができます。 画面の上部にあるフィルターを使用して、フィルター処理を行うフィールドを指定できます。

たとえば、ビューをフィルター処理して、"dataprep" という名前を含む Apache Spark プールのみを表示することができます。

![サンプルのフィルター](./media/how-to-monitor-spark-pools/filter-example.png)

## <a name="view-details-about-a-specific-apache-spark-pool"></a>特定の Apache Spark プールに関する詳細を表示する

いずれかの Apache Spark プールに関する詳細を表示するには、その Apache Spark プールを選択して、詳細を表示します。

![Apache Spark プールの詳細](./media/how-to-monitor-spark-pools/spark-pool-details.png)

## <a name="next-steps"></a>次の手順

パイプラインの実行の監視に関する詳細については、[Synapse Studio でのパイプラインの実行の監視](how-to-monitor-pipeline-runs.md)に関する記事をご覧ください。 

Apache Spark アプリケーションの監視に関する詳細については、[Synapse Studio での Apache Spark アプリケーションの監視](how-to-monitor-spark-applications.md)に関する記事をご覧ください。
