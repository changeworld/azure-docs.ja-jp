---
title: Synapse Studio を使用して Apache Spark アプリケーションを監視する方法
description: Synapse Studio を使用して Apache Spark アプリケーションを監視する方法について説明します。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 9666b8c9424b03c34b1ac332ef7ceb2f617c96a3
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2021
ms.locfileid: "123542298"
---
# <a name="monitor-your-apache-spark-applications-in-synapse-studio"></a>Synapse Studio で Apache Spark アプリケーションを監視する

Azure Synapse Analytics では、Apache Spark を使用して、ワークスペース内の Apache Spark プールでノートブック、ジョブ、およびその他の種類のアプリケーションを実行できます。

この記事では、Apache Spark アプリケーションを監視する方法を説明し、最新の状態、問題、および進捗状況を監視できるようにします。

## <a name="access-apache-spark-applications-list"></a>Apache Spark アプリケーション リストにアクセスする

ワークスペースで Apache Spark アプリケーションの一覧を表示するには、まず [Synapse Studio を開き](https://web.azuresynapse.net/)、自分のワークスペースを選択します。

![ワークスペースへのログイン](./media/common/login-workspace.png)

ワークスペースを開いたら、左側の **[監視]** セクションを選択します。

![[監視] ハブを選択する](./media/common/left-nav.png)

**[Apache Spark applications]\(Apache Spark アプリケーション\)** を選択して、Apache Spark アプリケーションの一覧を表示します。

 ![Spark アプリケーションを選択する](./media/how-to-monitor-spark-applications/monitor-hub-nav-spark-applications.png)

## <a name="filter-your-apache-spark-applications"></a>Apache Spark アプリケーションをフィルター処理する

Apache Spark アプリケーションの一覧を関心のあるものだけにフィルター処理することができます。 画面の上部にあるフィルターを使用して、フィルター処理を行うフィールドを指定できます。

たとえば、ビューをフィルター処理して、名前に "sales" を含む Apache Spark アプリケーションのみを表示することができます。

![サンプルのフィルター](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="view-details-about-a-specific-apache-spark-application"></a>特定の Apache Spark アプリケーションに関する詳細を表示する

Apache Spark アプリケーションのいずれかの詳細を表示するには、Apache Spark アプリケーションを選択し、詳細を表示します。 Apache Spark アプリケーションがまだ実行中の場合は、進行状況を監視できます。 詳細については、[こちら](apache-spark-applications.md)を参照してください。

## <a name="next-steps"></a>次のステップ

パイプラインの実行の監視に関する詳細については、[Synapse Studio を実行するパイプラインの監視](how-to-monitor-pipeline-runs.md)に関する記事を参照してください。 

Apache Spark アプリケーションのデバッグの詳細については、[Synapse Studio での Apache Spark アプリケーションの監視](apache-spark-applications.md)に関するページを参照してください。