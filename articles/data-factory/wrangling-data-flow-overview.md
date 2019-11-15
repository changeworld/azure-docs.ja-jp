---
title: Azure Data Factory のラングリング データ フロー
description: Azure Data Factory のラングリング データ フローの概要
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 7b46b1108246f0b83fcfce69844d19d01b1994c4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73665644"
---
# <a name="what-are-wrangling-data-flows"></a>ラングリング データ フローとは

組織では、日々増え続ける複雑なデータを正確に分析するために、データの準備とラングリングを行う必要があります。 さまざまなビジネス プロセスのデータを使用して、価値創造までの時間を短縮できるようにするには、データの準備が不可欠です。

Azure Data Factory のラングリング データ フローを使用すると、コードなしのデータ準備をクラウド規模で繰り返し実行できます。 ラングリング データ フローは [Power Query Online](https://docs.microsoft.com/power-query/) と統合されているので、Power Query M の関数をデータ ファクトリ ユーザー向けに提供することができます。

ラングリング データ フローを使用すれば、Power Query Online Mashup Editor によって生成された M を、クラウド規模で実行するための Spark コードに変換できます。

ラングリング データ フローは、データ エンジニアや "シティズン データ インテグレーター" にとっては特に便利です。

## <a name="use-cases"></a>ユース ケース

### <a name="fast-interactive-data-exploration-and-preparation"></a>迅速な対話型のデータ探索と準備

複数のデータ エンジニアやシティズン データ インテグレーターが、データセットをクラウド規模で対話的に探索し、準備することができます。 データ レイク内のデータの量、種類、速さが増大するなか、ユーザーは、データ セットを探索して準備するための効果的な方法を必要としています。 たとえば、2017 年以降の新規顧客を対象に、すべての顧客人口統計情報を含んだデータセットを作成するようなケースもあるでしょう。 既知のターゲットにマッピングするのでなければ、 レイクに公開する前に、データセットの探索、ラングリング、準備を行って、要件を満たすことになります。 ラングリング データ フローは、それほど厳格ではない分析シナリオで多く使用されます。 準備されたデータセットは、変換や機械学習のために使用できます。

### <a name="code-free-agile-data-preparation"></a>コードフリーのアジャイルなデータ準備

シティズン データ インテグレーターは、データを探して準備するのに、業務時間の 60% 以上を費やしています。 業務の生産性を向上させるため、彼らはこれらの作業をコーディングなし行う方法を探しています。 シティズン データ インテグレーターが Power Query Online などの既知のツールを使用して、データをスケーラブルに強化、整形、公開できるようになれば、生産性は大幅に向上します。 Azure Data Factory のラングリング データ フローを使用すれば、シティズン データ インテグレーターは使い慣れた Power Query Online マッシュアップ エディターを使用して、エラーを迅速に修正し、データを標準化し、高品質なデータを生成して、ビジネス上の意思決定をサポートできるようになります。

### <a name="data-validation"></a>データ検証

コードを使用せずにデータを視覚的にスキャンして、外れ値や異常を除外し、データを高速分析用に整形することができます。

## <a name="next-steps"></a>次の手順

[ラングリング データ フローの作成](wrangling-data-flow-tutorial.md)方法について確認します。