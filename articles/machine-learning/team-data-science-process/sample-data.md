---
title: Azure BLOB コンテナー、SQL Server、Hive テーブルのデータのサンプリング | Microsoft Docs
description: さまざまな Azure 環境に格納されているデータを探索する方法について説明します。
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 80a9dfae-e3a6-4cfb-aecc-5701cfc7e39d
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 60431578ba8b5c740fe5e1124dcdad32c3cb2ede
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2018
ms.locfileid: "51344087"
---
# <a name="heading"></a>Azure BLOB コンテナー、SQL Server、Hive テーブルのデータのサンプリング

以下の記事では、3 つの異なる Azure の場所のいずれかに格納されているデータをサンプリングする方法が説明されています。

* [**Azure BLOB コンテナーのデータ**](sample-data-blob.md)は、プログラムでダウンロードした後、サンプルの Python コードを使用してサンプリングします。
* [**SQL Server のデータ**](sample-data-sql-server.md)は、SQL と Python プログラミング言語の両方を使用してサンプリングします。 
* [**Hive テーブルのデータ**](sample-data-hive.md)は、Hive クエリを使用してサンプリングします。

このサンプリング タスクは、 [Team Data Science Process (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)の 1 ステップです。

**データをサンプリングする理由**

分析しようとしているデータセットが大規模な場合、データをダウンサンプリングして、小規模であっても典型的であり、管理しやすいサイズに減らすことが通常は推奨されます。 これにより、データの理解、探索、および特徴エンジニアリングが容易になります。 Cortana Analytics Process におけるダウンサンプリングの役割は、データ処理機能と機械学習モデルのプロトタイプをより迅速に作成できるようにすることです。

