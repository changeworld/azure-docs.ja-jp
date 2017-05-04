---
title: "GitHub webhook でトリガーされる Azure 関数の作成 | Microsoft Docs"
description: "Azure Functions を使用して、GitHub webhook によって呼び出されるサーバーなしの関数を作成します。"
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
ms.date: 04/18/2017
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: d4354546f3342d65353a86a4cec7d02547ab92e7
ms.lasthandoff: 04/22/2017


---
# <a name="create-a-function-triggered-by-a-github-webhook"></a>GitHub webhook でトリガーされる関数の作成

GitHub webhook でトリガーされる関数を作成する方法について説明します。 

![Azure Portal での Function App の作成](./media/functions-create-github-webhook-triggered-function/function-app-in-portal-editor.png)

このトピックでは、[Azure Portal を使用した初めての関数の作成](functions-create-first-azure-function.md)に関するトピックで作成されるリソースが必要になります。

また、GitHub アカウントも必要です。 まだアカウントを持っていない場合は、[無料の GitHub アカウントにサインアップ](https://github.com/join)できます。 

このトピックの手順をすべて完了するまでにかかる時間は、5 分未満です。

## <a name="find-your-function-app"></a>Function App の検索    

1. [Azure Portal](https://portal.azure.com/) にログインします。 

2. ポータルの上部にある検索バーで Function App の名前を入力し、一覧からその関数アプリを選択します。

## <a name="create-function"></a>GitHub webhook でトリガーされる関数の作成

1. Function App で、**[関数]** の横にある **+** ボタンをクリックし、使用する言語の **GitHubWebHook** テンプレートをクリックしてから、**[作成]** をクリックします。
   
    ![GitHub webhook でトリガーされる関数を Azure Portal で作成します。](./media/functions-create-github-webhook-triggered-function/functions-create-github-webhook-trigger.png) 

2. **[</> 関数の URL の取得]** をクリックし、値をコピーして保存します。 **[</> GitHub シークレットの取得]** で同じ操作を行います。 これらの値は GitHub で webhook を構成するために使用します。 

    ![関数コードの確認](./media/functions-create-github-webhook-triggered-function/functions-copy-function-url-github-secret.png) 
         
次に、GitHub リポジトリで webhook を作成します。 

## <a name="configure-the-webhook"></a>webhook を構成する
1. GitHub で、自分が所有するリポジトリに移動します。 フォークした任意のリポジトリを使用することもできます。
 
2. **[Settings (設定)]**、**[Webhooks (webhook)]**、**[Add webhook (webhook の追加)]** の順にクリックします。
   
    ![GitHub webhook の追加](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-2.png)

3. 関数の URL とシークレットを **[Payload URL (ペイロード URL)]** と **[Secret (シークレット)]** に貼り付け、**[Content type (コンテンツの種類)]** で **[application/json]** を選択します。

4. **[Let me select individual events (個々のイベントを自分で選択する)]** をクリックし、**[Issue comment (問題に対するコメント)]** を選択して、**[Add webhook (webhook の追加)]** をクリックします。
   
    ![webhook URL とシークレットの設定](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-3.png)

これで、新しい問題のコメントが追加された場合に関数をトリガーするよう webhook が構成されました。 

## <a name="test-the-function"></a>関数をテストする
1. GitHub リポジトリの新しいブラウザー ウィンドウで、**[Issues (問題)]** タブを開きます。

2. 新しいウィンドウで **[New Issue (新しい問題)]** をクリックし、タイトルを入力して、**[Submit new issue (新しい問題の送信)]** をクリックします。 

2. 問題にコメントを入力し、 **[コメント]**をクリックします。 

3. その他の GitHub ウィンドウで新しい webhook の横の **[Edit (編集)]** をクリックし、**[Recent Deliveries (最近の配信)]** まで下へスクロールして、関数によって webhook 要求が処理されたことを確認します。 
 
    ![webhook URL とシークレットの設定](./media/functions-create-github-webhook-triggered-function/functions-github-webhook-triggered.png)

   関数からの応答には `New GitHub comment: <Your issue comment text>` が含まれています。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


