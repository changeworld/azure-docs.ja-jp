---
title: Python クイック スタート - 意図の予測 - LUIS
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Python を使用して LUIS アプリを呼び出す方法について説明します。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: e560aeffecf63f63966a49053e0f79d012b4a0a3
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038274"
---
# <a name="quickstart-get-intent-using-python"></a>クイック スタート: Python による意図の取得
このクイック スタートでは、LUIS エンドポイントに発話を渡して、意図とエンティティを取得します。

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>前提条件

* [Python 3.6](https://www.python.org/downloads/) 以降。
* [Visual Studio Code](https://code.visualstudio.com/)

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>LUIS キーを取得する

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>ブラウザーで意図を取得する

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent--programmatically"></a>プログラムで意図を取得する

Python を使用して、前の手順でブラウザー ウィンドウに表示されたものと同じ結果にアクセスできます。

1. 次のコード スニペットのいずれかを、`quickstart-call-endpoint.py` というファイルにコピーします。

   [!code-python[Console app code that calls a LUIS endpoint for Python 2.7](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/2.x/quickstart-call-endpoint-2-7.py)]

   [!code-python[Console app code that calls a LUIS endpoint for Python 3.6](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/3.x/quickstart-call-endpoint-3-6.py)]

2. `Ocp-Apim-Subscription-Key` フィールドの値を実際の LUIS エンドポイント キーに置き換えます。

3. `pip install requests` で依存関係をインストールします。

4. `python ./quickstart-call-endpoint.py` でスクリプトを実行します。 前の手順でブラウザー ウィンドウに表示されたものと同じ JSON が表示されます。

## <a name="luis-keys"></a>LUIS キー

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ
python ファイルを削除します。 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [発話の追加](luis-get-started-python-add-utterance.md)