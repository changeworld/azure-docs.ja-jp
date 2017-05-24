---
title: "GitHub webhook でトリガーされる Azure 関数の作成 | Microsoft Docs"
description: "Azure Functions を使用して、GitHub webhook によって呼び出されるサーバーレスの関数を作成します。"
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 36ef34b8-3729-4940-86d2-cb8e176fcc06
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 34988ef05a27062ca109a1640e39695b52b8773f
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---
# <a name="create-a-function-triggered-by-a-github-webhook"></a>GitHub webhook でトリガーされる関数の作成

GitHub 固有のペイロードを含む HTTP webhook 要求によってトリガーされる関数を作成する方法について説明します。 

![Azure Portal での Github Webhook によってトリガーされる関数](./media/functions-create-github-webhook-triggered-function/function-app-in-portal-editor.png)

このトピックの手順をすべて完了するまでにかかる時間は、5 分未満です。

## <a name="prerequisites"></a>前提条件 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

少なくとも 1 つのプロジェクトを持つ GitHub アカウントも必要です。 まだアカウントを持っていない場合は、[無料の GitHub アカウントにサインアップ](https://github.com/join)できます。

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)] 

## <a name="create-function"></a>GitHub webhook でトリガーされる関数の作成

1. Function App を展開し、**[関数]** の横にある **+** ボタンをクリックし、目的の言語の **GitHubWebHook** テンプレートをクリックします。 **関数に名前を付け**てから、**[作成]** をクリックします。 

2. 新しい関数で、**[</> 関数の URL の取得]** をクリックし、値をコピーして保存します。 **[</> GitHub シークレットの取得]** で同じ操作を行います。 これらの値は GitHub で webhook を構成するために使用します。 

    ![関数コードの確認](./media/functions-create-github-webhook-triggered-function/functions-copy-function-url-github-secret.png) 
         
次に、GitHub リポジトリで webhook を作成します。 

## <a name="configure-the-webhook"></a>webhook を構成する
1. GitHub で、自分が所有するリポジトリに移動します。 フォークした任意のリポジトリを使用することもできます。 リポジトリをフォークする必要がある場合は、<https://github.com/Azure-Samples/functions-quickstart> を使用します。 
 
2. **[Settings (設定)]**、**[Webhooks (webhook)]**、**[Add webhook (webhook の追加)]** の順にクリックします。
   
    ![GitHub webhook の追加](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-2.png)

3. テーブルに指定されている設定を使用し、**[Add webhook (webhook の追加)]** をクリックします。
 
    ![webhook URL とシークレットの設定](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-3.png)

    | 設定      |  推奨値   | 説明                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Payload URL (ペイロード URL)** | コピーされた値 | **[</> 関数の URL の取得]** によって返された値を使用します。 |
    | **シークレット**   | コピーされた値 | **[</> GitHub シークレットの取得]** によって返された値を使用します。 |
    | **コンテンツの種類** | application/json | この関数は、JSON ペイロードを予測します。 |
    | Event triggers (イベント トリガー) | Let me select individual events (個々のイベントを選択させてください) | 問題コメント イベントに対してのみトリガーします。  |
    |                | Issue comment (問題コメント)                    |  |

これで、新しい問題のコメントが追加された場合に関数をトリガーするよう webhook が構成されました。 

## <a name="test-the-function"></a>関数をテストする
1. GitHub リポジトリの新しいブラウザー ウィンドウで、**[Issues (問題)]** タブを開きます。

2. 新しいウィンドウで、**[New Issue (新しい問題)]** をクリックし、タイトルを入力して、**[Submit new issue (新しい問題の送信)]** をクリックします。 

2. 問題にコメントを入力し、 **[コメント]**をクリックします。 

    ![GitHub の問題コメントを追加します。](./media/functions-create-github-webhook-triggered-function/functions-github-webhook-add-comment.png) 

3. ポータルに戻り、ログを表示します。 新しいコメント テキストを含むトレース エントリが表示されます。 
    
     ![ログ内のコメント テキストを表示します。](./media/functions-create-github-webhook-triggered-function/function-app-view-logs.png)
 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>次のステップ

GitHub webhook から要求が受信されときに実行される関数を作成しました。 
[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)] webhook トリガーの詳細については、「[Azure Functions における HTTP と Webhook のバインド](functions-bindings-http-webhook.md)」を参照してください。 




