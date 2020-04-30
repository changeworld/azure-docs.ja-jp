---
title: クイック スタート:LUIS キーの作成
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、LUIS アプリケーションを作成してキーを取得する方法について説明します。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: trbye
ms.openlocfilehash: df12b51020083489d431d0ebcd7eb506ef97caa2
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400844"
---
# <a name="quickstart-getting-a-luis-endpoint-key"></a>クイック スタート:LUIS エンドポイント キーを取得する

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、次の項目を用意する必要があります。

* LUIS アカウント。 [LUIS ポータル](https://www.luis.ai/home)から無料で取得できます。

## <a name="luis-and-speech"></a>LUIS および音声認識

LUIS は音声から意図を認識するために Speech サービスと統合しています。 Speech サービスのサブスクリプションは不要で、LUIS だけでかまいません。

LUIS は、次の 3 種類のキーを使用します。

|キーの種類|目的|
|--------|-------|
|Authoring|LUIS アプリをプログラムで作成および変更できる|
|スターター|テキストのみを使用して LUIS アプリケーションをテストできる|
|エンドポイント |特定の LUIS アプリへのアクセスを承認する|

このチュートリアルには、エンドポイント タイプのキーが必要です。 このチュートリアルでは、[事前構築済みホーム オートメーション アプリの使用](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app)に関するクイックスタートに従って作成できるホーム オートメーション LUIS アプリのサンプルを使用します。 独自の LUIS アプリを作成した場合は、代わりにそれを使用することができます。

LUIS アプリを作成すると、テキスト クエリを使用してアプリをテストできるようにスターター キーが自動的に生成されます。 このキーでは Speech サービスとの統合が有効にならないため、このチュートリアルでこれを使用することはできません。 Azure ダッシュボードで LUIS リソースを作成して LUIS アプリに割り当ててください。 このチュートリアルでは無料のサブスクリプション階層を使用することができます。

Azure ダッシュ ボードで LUIS のリソースを作成した後、[LUIS ポータル](https://www.luis.ai/home)にログインし、 **[マイ アプリ]** ページで自分のアプリケーションを選択し、アプリの **[Manage]\(管理\)** ページに切り替えます。 最後に、サイド バーの **[Keys and endpoints]\(キーとエンドポイント\)** を選択します。

![LUIS のポータル キーとエンドポイントの設定](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-endpoints-page.png)

**[Keys and Endpoint]\(キーとエンドポイント\) の設定**ページで:

1. **[Resources and Keys]\(リソースとキー\)** セクションまでスクロールし、 **[Assign resource]\(リソースの割り当て\)** を選択します。
1. **[Assign a key to your app]\(アプリへのキーの割り当て\)** ダイアログ ボックスに次の変更を加えます。

   * **[Tenant]\(テナント\)** で **[Microsoft]** を選択します。
   * **[Subscription Name]\(サブスクリプション名\)** には、使用したい LUIS リソースが含まれている Azure サブスクリプションを選択します。
   * **[Key]\(キー\)** には、アプリで使用したい LUIS リソースを選択します。

   まもなく、ページの下部にあるテーブルに新しいサブスクリプションが表示されます。

1. キーの横にあるアイコンを選択して、それをクリップボードにコピーします。 (どちらのキーを使用してもかまいません。)

![LUIS アプリのサブスクリプション キー](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-assigned.png)


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [意図を認識する](~/articles/cognitive-services/Speech-Service/quickstarts/intent-recognition.md)
