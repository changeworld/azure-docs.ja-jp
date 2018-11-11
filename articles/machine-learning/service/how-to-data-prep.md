---
title: Machine Learning Data Prep SDK for Python でデータを用意する - Azure
description: Azure Machine Learning Data Prep SDK for Python を使用してさまざまな形式のデータを読み込み、それを変換してさらに便利にしたり、モデルでアクセスする場所にデータを書き込んだりする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: a315394ab394e7f4dfe528cf765c9ac5a65c80c4
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277003"
---
# <a name="prepare-data-for-modeling-with-azure-machine-learning"></a>Azure Machine Learning を使用したモデリング用にデータを準備する
 
データ準備は機械学習ワークフローの重要な部分です。 使いやすい形式のクリーン データにアクセスできれば、モデルは一層精確かつ効率的になります。 

[Azure Machine Learning Data Prep SDK](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py) を使用して、Python でデータを準備することができます。 

## <a name="data-preparation-pipeline"></a>データ準備パイプライン

主なデータ準備手順は次のとおりです。

1. [データを読み込む](how-to-load-data.md): さまざまな形式で読み込むことができます
2. [変換する](how-to-transform-data.md): より使いやすい構造に変換します
3. [書き込む](how-to-write-data.md): モデルからアクセスできる場所にデータを書き込みます

![データ準備プロセス](./media/concept-data-preparation/data-prep-process.png)

## <a name="next-steps"></a>次の手順
Azure Machine Learning Data Prep SDK によるデータ準備の[サンプル ノートブック](https://github.com/Microsoft/AMLDataPrepDocs/tree/master/tutorials/getting-started/getting-started.ipynb)を確認します。
