---
title: "Azure Portal で初めての関数を作成する | Microsoft Docs"
description: "Azure Portal を使用して、サーバーレス実行のための最初の Azure 関数を作成する方法について説明します。"
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.service: functions
ms.devlang: multiple
ms.topic: hero-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 06/08/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: f00ca3b8a35c0c49277457bd42fe8a314520d5a5
ms.contentlocale: ja-jp
ms.lasthandoff: 06/09/2017


---
# <a name="create-your-first-function-in-the-azure-portal"></a>Azure Portal で初めての関数を作成する

Azure Functions を使用すると、最初に VM を作成したり Web アプリケーションを発行したりしなくても、サーバーレス環境でコードを実行できます。 このトピックでは、Functions を使用して Azure Portal で "hello world" 関数を作成する方法について説明します。

![Azure Portal での Function App の作成](./media/functions-create-first-azure-function/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Azure へのログイン

[Azure Portal](https://portal.azure.com/) にログインします。

## <a name="create-a-function-app"></a>Function App を作成する

関数の実行をホストするための Function App が存在する必要があります。 Function App を使用すると、リソースの管理、デプロイ、および共有を容易にするためのロジック ユニットとして関数をグループ化できます。 

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Function App が正常に作成されました。](./media/functions-create-first-azure-function/function-app-create-success.png)

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

次に、新しい Function App で関数を作成します。

## <a name="create-function"></a>HTTP によってトリガーされる関数の作成

1. 新しい Function App を展開し、**[関数]** の横にある **+** ボタンをクリックします。

2.  **[関数への早道]** ページで、**[webhook + API]** を選択し、関数の言語を選択して、**[この関数を作成する]** をクリックします。 
   
    ![Azure Portal での関数のクイック スタート。](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

HTTP によってトリガーされる関数のテンプレートを使用して、選択した言語で関数が作成されます。 HTTP 要求を送信することによって、新しい関数を実行できます。

## <a name="test-the-function"></a>関数をテストする

1. 新しい関数で、**[</> 関数の URL の取得]** をクリックし、**[default (Function key)\(既定 (関数キー)\)]** を選択して、**[コピー]** をクリックします。 

    ![Azure Portal からの関数 URL のコピー](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

2. HTTP 要求の URL をブラウザーのアドレス バーに貼り付けます。 この URL にクエリ文字列 `&name=<yourname>` を追加して、要求を実行します。 関数によって返された GET 要求へのブラウザーでの応答を次に示します。

    ![ブラウザーでの関数の応答。](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    要求 URL には、既定では HTTP 経由で関数にアクセスするために必要なキーが含まれています。   

## <a name="view-the-function-logs"></a>関数のログを表示する 

関数が実行されると、ログにトレース情報が書き込まれます。 前の実行からのトレース出力を表示するには、ポータルで関数に戻り、画面の下部にある上矢印をクリックして **[ログ]** を展開します。 

![Azure Portal の関数ログ ビューアー。](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [Clean up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>次のステップ

HTTP によってトリガーされる単純な関数を含む Function App を作成しました。  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

詳細については、「[Azure Functions における HTTP と Webhook のバインド](functions-bindings-http-webhook.md)」を参照してください。




