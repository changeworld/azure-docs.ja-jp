---
title: クイック スタート:Azure REST API と Python を使用して画像から顔を検出する
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Azure Face REST API と Python を使用して、画像内の顔を検出します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 11/09/2018
ms.author: pafarley
ms.openlocfilehash: e0d810e9b0cfd9bd53239308cfd0b6238b988ecc
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55870573"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-python"></a>クイック スタート:Face REST API と Python を使用して画像から顔を検出する

このクイック スタートでは、Azure Face REST API と Python を使用して、画像内の人の顔を検出します。 このスクリプトでは、顔を囲むフレームを描き、画像の上に性別と年齢の情報をスーパーインポーズします。

![男性と女性。それぞれの顔を囲む四角形が描かれ、画像内に年齢と性別が表示されている](../images/labelled-faces-python.png)

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 


## <a name="prerequisites"></a>前提条件

- Face API サブスクリプション キー。 無料試用版のサブスクリプション キーは「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/?api=face-api)」から取得できます。 または、[Cognitive Services アカウントの作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)に関するページの手順に従って、Face API サービスをサブスクライブし、キーを取得します。

## <a name="run-the-jupyter-notebook"></a>Jupyter Notebook を実行する

このクイック スタートは、[MyBinder](https://mybinder.org) の Jupyter ノートブックとして実行できます。 Binder を起動するには、次のボタンを選択します。 次に、ノートブック内の手順に従います。

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=FaceAPI.ipynb)

## <a name="next-steps"></a>次の手順

この後は、サポートされているシナリオについて、Face API のリファレンス ドキュメントでさらに理解を深めましょう。

> [!div class="nextstepaction"]
> [Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
