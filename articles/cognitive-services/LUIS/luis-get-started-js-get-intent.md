---
title: Language Understanding (LUIS) の自然言語テキスト分析を JavaScript を使って行う - Cognitive Services - Azure Cognitive Services | Microsoft Docs
description: このクイック スタートでは、提供されているパブリック LUIS アプリを使って、会話形式のテキストからユーザーの意図を判断します。 JavaScript を使用して、パブリック アプリの HTTP 予測エンドポイントにユーザーの意図をテキストとして送信します。 エンドポイントでは、LUIS によってパブリック アプリのモデルが適用されます。これにより自然言語テキストの意味が分析され、全体的な意図が特定されて、アプリのサブジェクト ドメインに関連したデータが抽出されます。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: 10210c3759611a77c4430a97896a19a6b97b9fa9
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2018
ms.locfileid: "43771714"
---
# <a name="quickstart-analyze-text-using-javascript"></a>クイック スタート: JavaScript を使ってテキストを分析する

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="prerequisites"></a>前提条件

* [Visual Studio Code](https://code.visualstudio.com/)
* パブリック アプリ ID: df67dcdb-c37d-46af-88e1-8b97951ca1c2


## <a name="get-luis-key"></a>LUIS キーを取得する

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>ブラウザーでテキストを分析する

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-javascript"></a>JavaScript でテキストを分析する 

JavaScript を使用して、前の手順でブラウザー ウィンドウに表示されたものと同じ結果にアクセスできます。 

1. 以下のコードをコピーして HTML ファイルに保存します。

   [!code-html[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/javascript/call-endpoint.html)]

2. ブラウザーでファイルを開きます。 LUIS エンドポイント キーをフォームに入力し、**[Submit]\(送信\)** を選択します。

    ![ブラウザーに表示された HTML サンプル (Home Automation アプリに対する LUIS の結果)](./media/luis-get-started-js-get-intent/html-results.png)

    フォームの下に結果が表示されます。 

## <a name="luis-keys"></a>LUIS キー

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

JavaScript ファイルを削除します。

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [発話の追加](luis-get-started-javascript-add-utterance.md)
