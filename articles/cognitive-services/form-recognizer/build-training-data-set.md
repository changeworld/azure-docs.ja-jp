---
title: カスタム モデルのトレーニング データ セットを作成する方法 - Form Recognizer
titleSuffix: Azure Cognitive Services
description: お使いのトレーニング データ セットが Form Recognizer モデルのトレーニングに最適化されていることを確認する方法を学習します。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 71ad7c5dd3ad74082da552cd3c45142bc0c2d624
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75380628"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>カスタム モデルのトレーニング データ セットを作成する

Form Recognizer のカスタム モデルを使用する場合は、モデルを業界固有のフォームに合わせてトレーニングできるように、独自のトレーニング データを提供します。 モデルは、5 つの入力フォームか、空のフォーム (ファイル名に "empty" という単語を含める必要があります) と 2 つの入力フォームを使用して、トレーニングできます。 トレーニングに使用する十分な数の入力フォームがある場合でも、トレーニング データ セットに空のフォームを追加することで、モデルの精度を高めることができます。

手動でラベル付けされたトレーニングデータを使用する場合は、同じタイプの少なくとも5つのフォームから開始する必要があります。 同じデータセット内で、ラベル付けされていないフォームと空のフォームを引き続き使用することもできます。

## <a name="training-data-tips"></a>トレーニング データのヒント

トレーニング用に最適化されたデータ セットを使用することが重要です。 次のヒントを使用して、[カスタムモデルのトレーニング](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) 操作で最良の結果が得られるようにしてください：

* 可能であれば、画像ベースのドキュメントではなく、テキストベースの PDF ドキュメントを使用します。 スキャンした PDF は画像として処理されます。
* 入力フォームの場合は、すべてのフィールドに入力されている例を使用します。
* 各フィールドに異なる値が含まれたフォームを使用します。
* フォームの画像の品質が低い場合は、より大きなデータ セット (たとえば 10 から 15 の画像) を使用します。
* トレーニングデータセットの合計サイズは、最大500ページまでにすることができます。

## <a name="general-input-requirements"></a>一般的な入力の要件

トレーニング データ セットが、すべての Form Recognizer コンテンツの入力の要件にも従っていることを確認します。 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>トレーニング データをアップロードする

トレーニングに使用するフォーム ドキュメントのセットをまとめたら、それを Azure Blob Storage コンテナーにアップロードする必要があります。 コンテナーを含む Azure Storage アカウントを作成する方法がわからない場合は、[Azure portal の Azure Storage に関するクイックスタート](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)に従ってください。

### <a name="organize-your-data-in-subfolders-optional"></a>データをサブフォルダーに整理する (オプション)

既定では、[カスタムモデルのトレーニング](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) API は、ストレージ コンテナーのルートにあるフォーム ドキュメントのみが使用されます。 ただし、API 呼び出しで指定した場合は、サブフォルダー内のデータを使用してトレーニングすることができます。 [カスタムモデルのトレーニング](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) の呼び出しの本文は、通常、次の形式で、`<SAS URL>` はコンテナーの共有アクセス署名 URL です：

```json
{
  "source":"<SAS URL>"
}
```

次の内容を要求本文に追加すると、API によってサブフォルダーにあるドキュメントがトレーニングされます。 `"prefix"` フィールドはオプションであり、トレーニング データ セットを、指定された文字列で始まるパスのファイルに制限します。 そのため、たとえば、値 `"Test"` の場合、API は、"Test" という単語で始まるファイルまたはフォルダーのみを調べます。

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  },
  "useLabelFile": false
}
```

## <a name="next-steps"></a>次のステップ

トレーニング データ セットの作成方法を習得したので、クイックスタートに従って、カスタム Form Recognizer モデルをトレーニングし、お使いのフォームでの使用を開始してください。

* [クイック スタート:cURL を使用してモデルをトレーニングし、フォーム データを抽出する](./quickstarts/curl-train-extract.md)
* [クイック スタート:Python で REST API を使用してモデルをトレーニングし、フォーム データを抽出する](./quickstarts/python-train-extract.md)
* [REST API と Python を使用したラベルのトレーニング](./quickstarts/python-labeled-data.md)