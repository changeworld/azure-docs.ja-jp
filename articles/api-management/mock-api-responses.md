---
title: Azure Portal で API の応答の模擬テストを実行する | Microsoft Docs
description: このチュートリアルでは、API Management (APIM) を使用して、モック応答を返すように API でポリシーを設定する方法について説明します。 この方法を使用すると、実際の応答を送信するためにバックエンドを使用できない場合に、開発者が、API Management インスタンスの実装とテストを進めることができます。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 4383ce3788f6fade5299d69ef99b80221c58d9e7
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2018
ms.locfileid: "33936985"
---
# <a name="mock-api-responses"></a>API の応答の模擬テストを実行する

バックエンド API は APIM API にインポートするか、手動で作成して管理できます。 このチュートリアルの手順では、APIM を使用して手動で空の API を作成し、管理する方法を示します。 このチュートリアルで説明するのは、モック応答を返すように API でポリシーを設定する方法です。 この方法を使用すると、実際の応答を送信するためにバックエンドを使用できない場合でも、開発者が、APIM インスタンスの実装とテストを勧めることができます。 応答を模擬表示する機能は、さまざまなシナリオで役に立ちます。

+ まず API ファサードを設計し、後でバックエンド実装を行う場合。 または、バックエンドを並列で開発している場合。
+ バックエンドが一時的に動作していないか、拡大縮小できない場合。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * テスト API を作成する 
> * 操作をテスト API に追加する
> * 応答のモック作成を有効にする
> * モック API をテストする

![モック操作の応答](./media/mock-api-responses/mock-api-responses01.png)

## <a name="prerequisites"></a>前提条件

[Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関するクイック スタートを完了します。

## <a name="create-a-test-api"></a>テスト API を作成する 

このセクションの手順では、バックエンドがない空の API を作成する方法について説明します。 また、API に操作を追加する方法についても説明します。 このセクションの手順を完了した後、操作を呼び出すと、エラーが発生します。 「応答のモック作成を有効にする」セクションの手順の完了後は、エラーは発生しません。

1. **[API Management]** で **[API]** を選びます。
2. 左側のメニューで、**[+ API の追加]** を選びます。
3. 一覧から **[空の API]** を選択します。
4. **[表示名]** に「*テスト API*」と入力します。
5. **[製品]** に「*無制限*」と入力します。
6. **[作成]** を選択します。

## <a name="add-an-operation-to-the-test-api"></a>操作をテスト API に追加する

1. 前の手順で作成した API を選びます。
2. **[+ 操作の追加]** を選択します。

    ![モック操作の応答](./media/mock-api-responses/mock-api-responses02.png)

    |Setting|値|[説明]|
    |---|---|---|
    |**URL** (HTTP 動詞)|GET|定義済み HTTP 動詞のいずれかを選択できます。|
    |**URL** |*/test*|API の URL パス。 |
    |**[表示名]**|"*テスト呼び出し*"|**開発者ポータル**に表示される名前。|
    |**説明**||**開発者ポータル**でこの API を使用する開発者にドキュメントを提供するために使用される操作の説明を指定します。|
    |**[クエリ]** タブ||クエリ パラメーターを追加できます。 名前と説明を提供するだけでなく、このパラメーターに割り当て可能な値を指定することもできます。 値の 1 つを既定値としてマークすることができます (オプション)。|
    |**[要求]** タブ||要求のコンテンツの種類、例、およびスキーマを定義できます。 |
    |**[応答]** タブ|この表に続く手順を参照してください。|応答の状態コード、コンテンツの種類、例、およびスキーマを定義します。|

3. URL、表示名、および説明の各フィールドの下にある **[応答]** タブを選択します。
4. **[+ 応答の追加]** をクリックします。
5. 一覧から **[200 OK]** を選択します。
6. 右側の **[表現]** 見出しで、**[+ 表現の追加]** を選択します。
7. 検索ボックスに「*application/json*」と入力し、コンテンツの種類として **application/json** を選択します。
8. **[サンプル]** テキスト ボックスに、「*{ 'sampleField' : 'test' }*」と入力します。
9. **[保存]** を選択します。

## <a name="enable-response-mocking"></a>応答のモック作成を有効にする

1. 「テスト API を作成する」の手順で作成した API を選択します。
2. 追加したテスト操作を選択します。
2. 右側のウィンドウで **[デザイン]** タブをクリックします。
3. **[受信処理]** ウィンドウで、鉛筆のアイコンをクリックします。
4. **[モックの作成]** タブで、**[モック作成の動作]** として **[静的応答]** を選択します。
5. **[API Management returns the following response:]\(API Management により、次の応答が返されます\)** テキスト ボックスに、「**200 OK、application/json**」と入力します。 この選択は、API が、前のセクションで定義した応答のサンプルを返すことを示します。
6. **[保存]** を選択します。

## <a name="test-the-mocked-api"></a>モック API をテストする

1. 「テスト API を作成する」の手順で作成した API を選択します。
2. **[テスト]** タブを開きます。
3. **[テスト呼び出し]** API が選択されていることを確認します。

    > [!TIP]
    > "**モック作成が有効になっています**" というテキストを含む黄色のバーは、API Management から返される応答によってモック作成ポリシーが送信されるが、実際のバックエンド応答ではないことを示します。

3. **[送信]** を選択して、テスト呼び出しを実行します。
4. **[HTTP 応答]** に、チュートリアルの最初のセクションでサンプルとして指定した JSON が表示されます。

## <a name="video"></a>ビデオ

> [!VIDEO https://www.youtube.com/embed/i9PjUAvw7DQ]
> 
> 

## <a name="next-steps"></a>次の手順
このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * テスト API を作成する
> * 操作をテスト API に追加する
> * 応答のモック作成を有効にする
> * モック API をテストする

次のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [公開された API の変換と保護](transform-api.md)
