---
title: "Azure Portal で初めての関数を作成する | Microsoft Docs"
description: "Azure へようこそ。 Azure Portal で初めての Azure 関数を作成しましょう。"
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/10/2017
ms.author: glenga
ms.custom: welcome-email
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 7542280ca6dbe1a8d110155e521228d675c0d994
ms.lasthandoff: 04/12/2017


---
# <a name="create-your-first-function-in-the-azure-portal"></a>Azure Portal で初めての関数を作成する

このトピックでは、Azure 関数を使用して、HTTP 要求によって呼び出される "hello world" 関数を作成する方法について説明します。 Azure Portal で関数を作成するには、関数のサーバーなしの実行をホストする関数アプリを作成しておく必要があります。

このクイック スタートを完了するには、Azure アカウントが必要です。 [無料アカウント](https://azure.microsoft.com/free/)を入手できます。 また、Azure に登録せずに、[Azure Functions を試す](https://azure.microsoft.com/try/app-service/functions/)こともできます。

## <a name="log-in-to-azure"></a>Azure へのログイン

[Azure ポータル](https://portal.azure.com/)にログインします。

## <a name="create-a-function-app"></a>Function App を作成する

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

詳しくは、「[Azure Portal から関数アプリを作成する](functions-create-function-app-portal.md)」をご覧ください。

## <a name="create-a-function"></a>関数を作成する
次の手順では、Azure Functions クイック スタートを使用して、新しい Function App で関数を作成します。

1. **[新規作成]** ボタンをクリックしてから **[webhook + API]** をクリックし、関数の言語を選択して、**[関数を作成する]** をクリックします。 HTTP トリガー関数テンプレートを使用して、選択した言語で関数が作成されます。  
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

関数が作成されたら、HTTP 要求を送信してその関数をテストできます。

## <a name="test-the-function"></a>関数をテストする

関数テンプレートには動作するコードが含まれているため、新しい関数をポータルですぐにテストできます。

1. Function App で、新しい関数をクリックし、テンプレートのコードを確認します。 この関数は、メッセージ本文またはクエリ文字列で *name* 値が渡される HTTP 要求を予期していることがわかります。 関数を実行すると、応答メッセージでこの値が返されます。 この例は、JavaScript 関数です。
   
2. **[実行]** をクリックして、関数を実行します。 テスト HTTP 要求によって実行がトリガーされ、情報がログに書き込まれて、"hello..." という応答が **[テスト]** タブの **[出力]** に表示されます。
 
    ![](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

3. **[要求本文]** テキスト ボックスで、*name* プロパティの値を自分の名前に変更して、**[実行]** を再びクリックします。 今度は、**[出力]** の応答に自分の名前が含まれています。   

4. 同じ関数の実行を HTTP テスト ツールまたは別のブラウザー ウィンドウからトリガーするには、**[関数の URL の取得]** をクリックして、要求 URL をコピーし、ツールまたはブラウザーのアドレス バーに貼り付けます。 その URL にクエリ文字列値 `&name=yourname` を追加して、要求を実行します。 同じ情報がログに書き込まれ、同じ文字列が応答メッセージの本文に含まれます。

    ![](./media/functions-create-first-azure-function/function-app-browser-testing.png)


## <a name="next-steps"></a>次のステップ
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


