---
title: 分析用の Azure Storage 環境にデータを読み込む | Microsoft Docs
description: Azure Blob ストレージとの間のデータの移動
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: deguhath
ms.openlocfilehash: a5db14b99a81c373fbc72f523798e1f3bbdf9285
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2018
ms.locfileid: "51344503"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>分析用のストレージ環境にデータを読み込む

Team Data Science Process では、さまざまなストレージ環境に取り込まれるデータまたは読み込まれるデータを、プロセスの各段階において最も適切な方法で処理または分析する必要があります。 処理に通常使用されるデータの取り込み先として、Azure Blob Storage、SQL Azure データベース、Azure VM 上の SQL Server、HDInsight (Hadoop)、Azure Machine Learning などがあります。 

以下の記事では、データが格納および処理されるさまざまなターゲット環境にデータを取り込む方法について説明されています。

* [Azure BLOB Storage](move-azure-blob.md) へ/から
* [Azure VM 上の SQL Server](move-sql-server-virtual-machine.md) へ
* [Azure SQL データベース](move-sql-azure.md)へ
* [Hive テーブル](move-hive-tables.md)へ
* [SQL パーティション テーブル](parallel-load-sql-partitioned-tables.md)へ
* [オンプレミスの SQL Server](move-sql-azure-adf.md) から

分析の目的を達成するためにデータを取り込む必要があるターゲット環境は、技術上およびビジネス上のニーズと、データの最初の保存場所、形式、およびサイズによって決まります。 予測モデルを構築するために必要なさまざまなタスクを実行するために、複数の環境間でデータを移動する必要があるシナリオも珍しくありません。 この一連のタスクには、データ探索、前処理、クリーニング、サンプリング、モデル トレーニングなどが含まれます。
