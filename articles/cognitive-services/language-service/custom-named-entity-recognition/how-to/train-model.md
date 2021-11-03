---
title: カスタム固有表現認識 (NER) モデルをトレーニングする方法
titleSuffix: Azure Cognitive Services
description: カスタム固有表現認識 (NER) 用のモデルをトレーニングする方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 6fdf5ad5ed2eb8291b9a43e6004d8b18c5879b93
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092522"
---
# <a name="train-your-custom-named-entity-recognition-ner-model"></a>カスタム固有表現認識 (NER) モデルをトレーニングする

トレーニングは、モデルが[タグ付けされたデータ](tag-data.md)から学習するプロセスです。 トレーニングが済んだら、[モデル評価メトリックを使って](../how-to/view-model-evaluation.md)、[モデルを改善する](../how-to/improve-model.md)必要があるかどうかを判断できます。

モデルのトレーニングに要する時間はデータセットによって異なり、最大で数時間かかる場合があります。 一度にトレーニングできるモデルは 1 つのみであり、トレーニングされているモデルが既にあるプロジェクトで、他のモデルを作成またはトレーニングすることはできません。 

## <a name="prerequisites"></a>前提条件

* 構成済みの Azure Blob Storage アカウントで正常に[作成されたプロジェクト](create-project.md)
    * ストレージ アカウントに[アップロードされた](create-project.md#prepare-training-data)テキスト データ。
* [タグ付けされたデータ](tag-data.md)

詳しくは、[アプリケーション開発ライフサイクル](../overview.md#application-development-lifecycle)に関する記事をご覧ください。

## <a name="data-split"></a>データの分割

トレーニング プロセスを始める前に、データセット内のファイルは 2 つのグループにランダムに分割されます。

* **トレーニング セット** には、データセット内のファイルの 80% が含まれます。 これは、モデルのトレーニングに使われるメイン セットです。

* **テスト セット** には、データセットで利用できるファイルの 20% が含まれます。 このセットは、モデルの偏りのない[評価](../how-to/view-model-evaluation.md)を提供するために使われます。 このセットは、トレーニング中にはモデルに取り込まれません。

## <a name="train-model-in-language-studio"></a>Language Studio でモデルをトレーニングする

1. [Language Studio](https://aka.ms/LanguageStudio) でプロジェクトのページに移動します。

2. 左側のメニューから **[トレーニング]** を選びます。

3. 新しいモデルをトレーニングするには、 **[Train a new model]\(新しいモデルをトレーニングする\)** を選び、下のテキスト ボックスにモデル名を入力します。 このオプションを選び、下のドロップダウンから対象のモデルを選ぶと、**既存のモデルを上書きする** ことができます。

    :::image type="content" source="../media/train-model.png" alt-text="新しいモデルを作成する" lightbox="../media/train-model.png":::

4. ページの下部にある **[トレーニング]** ボタンを選びます。

## <a name="next-steps"></a>次のステップ

トレーニングが済んだら、[モデル評価メトリックを使い](view-model-evaluation.md)、必要に応じて[モデルを改善する](improve-model.md)ことができます。 モデルに問題がなければ、それをデプロイし、テキストから[エンティティを抽出する](call-api.md)ために使うことができます。
