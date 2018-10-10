---
title: PHP クイック スタート - 意図を予測する - LUIS
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、提供されているパブリック LUIS アプリを使って、会話形式のテキストからユーザーの意図を判断します。 PHP を使用して、パブリック アプリの HTTP 予測エンドポイントにユーザーの意図をテキストとして送信します。 エンドポイントでは、LUIS によってパブリック アプリのモデルが適用されます。これにより自然言語テキストの意味が分析され、全体的な意図が特定されて、アプリのサブジェクト ドメインに関連したデータが抽出されます。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 87c37de611056c5672ed0910afdd344325a0c6f7
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47036897"
---
# <a name="quickstart-get-intent-using-php"></a>クイック スタート: PHP を使用して意図を取得する

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>前提条件

* [PHP](http://php.net/) プログラミング言語
* [Visual Studio Code](https://code.visualstudio.com/)
* パブリック アプリ ID: df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>LUIS キーを取得する

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>ブラウザーで意図を取得する

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>プログラムで意図を取得する 

PHP を使用して、前の手順でブラウザー ウィンドウに表示されたものと同じ結果にアクセスできます。 

1. 以下のコードをコピーして `endpoint-call.php` というファイル名で保存します。

   [!code-php[PHP code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/php/endpoint-call.php)]

2. `"YOUR-KEY"` を実際のエンドポイント キーで置き換えます。

3. `php endpoint-call.php` で PHP アプリケーションを実行します。 前の手順でブラウザー ウィンドウに表示されたものと同じ JSON が表示されます。

## <a name="luis-keys"></a>LUIS キー

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

PHP ファイルを削除します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [発話の追加](luis-get-started-php-add-utterance.md)