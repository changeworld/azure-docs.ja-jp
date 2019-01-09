---
title: 別の Azure ストレージの場所にあるデータをサンプリングする - Team Data Science Process
description: Azure BLOB コンテナー、SQL Server、Hive テーブルのデータをサンプリングして、小さくても代表的で管理しやすいサイズに縮小します。
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7bc4174121a58353219e73eef86ec6c64f806647
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53133215"
---
# <a name="heading"></a>Azure BLOB コンテナー、SQL Server、Hive テーブルのデータのサンプリング

以下の記事では、3 つの異なる Azure の場所のいずれかに格納されているデータをサンプリングする方法が説明されています。

* [**Azure BLOB コンテナーのデータ**](sample-data-blob.md)は、プログラムでダウンロードした後、サンプルの Python コードを使用してサンプリングします。
* [**SQL Server のデータ**](sample-data-sql-server.md)は、SQL と Python プログラミング言語の両方を使用してサンプリングします。 
* [**Hive テーブルのデータ**](sample-data-hive.md)は、Hive クエリを使用してサンプリングします。

このサンプリング タスクは、 [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)の 1 ステップです。

**データをサンプリングする理由**

分析しようとしているデータセットが大規模な場合、データをダウンサンプリングして、小規模であっても典型的であり、管理しやすいサイズに減らすことが通常は推奨されます。 これにより、データの理解、探索、および特徴エンジニアリングが容易になります。 Cortana Analytics Process におけるダウンサンプリングの役割は、データ処理機能と機械学習モデルのプロトタイプをより迅速に作成できるようにすることです。

