---
title: GitHub webhook でトリガーされる Azure 関数の作成 | Microsoft Docs
description: Azure Functions を使用して、GitHub webhook によって呼び出されるサーバーレスの関数を作成します。
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: 36ef34b8-3729-4940-86d2-cb8e176fcc06
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/28/2018
ms.author: glenga
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 05ad567e407a6506222acdb66ab38c4cfab76e4b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38602554"
---
# <a name="create-a-function-triggered-by-a-github-webhook"></a>GitHub webhook でトリガーされる関数の作成

GitHub 固有のペイロードを含む HTTP webhook 要求によってトリガーされる関数を作成する方法について説明します。

![Azure Portal での Github Webhook によってトリガーされる関数](./media/functions-create-github-webhook-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>前提条件

+ 1 つ以上のプロジェクトを含む GitHub アカウント。
+ Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="create-an-azure-function-app"></a>Azure Function App の作成

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Function App が正常に作成されました。](./media/functions-create-first-azure-function/function-app-create-success.png)

次に、新しい Function App で関数を作成します。

<a name="create-function"></a>

## <a name="create-a-github-webhook-triggered-function"></a>GitHub webhook でトリガーされる関数の作成

1. Function App を展開し、**[関数]** の横にある **[+]** ボタンをクリックします。 これが Function App で初めての関数の場合、**[カスタム関数]** を選びます。 関数テンプレートの完全なセットが表示されます。

    ![Azure Portal での関数のクイック スタート ページ](./media/functions-create-github-webhook-triggered-function/add-first-function.png)

2. 検索フィールドに、「`github`」と入力し、GitHub webhook トリガー テンプレート用の目的の言語を選択します。 

     ![GitHub webhook トリガー テンプレートを選択します。](./media/functions-create-github-webhook-triggered-function/functions-create-github-webhook-trigger.png) 

2. 関数の**名前**を入力し、**[作成]** を選択します。 

     ![Azure Portal で GitHub webhook でトリガーされる関数を構成する](./media/functions-create-github-webhook-triggered-function/functions-create-github-webhook-trigger-2.png) 

3. 新しい関数で、**[</> 関数の URL の取得]** をクリックし、値をコピーして保存します。 **[</> GitHub シークレットの取得]** で同じ操作を行います。 これらの値は GitHub で webhook を構成するために使用します。

    ![関数コードの確認](./media/functions-create-github-webhook-triggered-function/functions-copy-function-url-github-secret.png)

次に、GitHub リポジトリで webhook を作成します。

## <a name="configure-the-webhook"></a>webhook を構成する

1. GitHub で、自分が所有するリポジトリに移動します。 フォークした任意のリポジトリを使用することもできます。 リポジトリをフォークする必要がある場合は、<https://github.com/Azure-Samples/functions-quickstart> を使用してください。

2. **[Settings]\(設定\)** > **[Options]\(オプション\)** を選択し、**[Features]\(機能\)** の **[Issues]\(問題\)** が有効になっていることを確認します。

   ![[Issues]\(問題\) を有効にする](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook.png)

1. **[Settings]\(設定\)** で、**[Webhooks]\(webhook\)** > **[Add webhook]\(Webhook の追加\)** を選択します。

    ![GitHub webhook の追加](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-2.png)

1. 次のテーブルに指定されている設定を使用し、**[Add webhook]\(Webhook の追加\)** をクリックします。

    ![webhook URL とシークレットの設定](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-3.png)

| Setting | 推奨値 | 説明 |
|---|---|---|
| **Payload URL (ペイロード URL)** | コピーされた値 | **[</> 関数の URL の取得]** によって返された値を使用します。 |
| **コンテンツの種類** | application/json | この関数は、JSON ペイロードを予測します。 |
| **シークレット**   | コピーされた値 | **[</> GitHub シークレットの取得]** によって返された値を使用します。 |
| Event triggers (イベント トリガー) | Let me select individual events (個々のイベントを選択させてください) | 問題コメント イベントに対してのみトリガーします。  |
| | Issue comment (問題コメント) |  |

これで、新しい問題のコメントが追加された場合に関数をトリガーするよう webhook が構成されました。

## <a name="test-the-function"></a>関数をテストする

1. GitHub リポジトリの新しいブラウザー ウィンドウで、**[Issues (問題)]** タブを開きます。

1. 新しいウィンドウで、**[New Issue (新しい問題)]** をクリックし、タイトルを入力して、**[Submit new issue (新しい問題の送信)]** をクリックします。

1. 問題にコメントを入力し、 **[コメント]** をクリックします。

    ![GitHub の問題コメントを追加します。](./media/functions-create-github-webhook-triggered-function/functions-github-webhook-add-comment.png)

1. ポータルに戻り、ログを表示します。 新しいコメント テキストを含むトレース エントリが表示されます。

     ![ログ内のコメント テキストを表示します。](./media/functions-create-github-webhook-triggered-function/function-app-view-logs.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>次の手順

GitHub webhook から要求が受信されときにトリガーされる関数を作成しました。

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

webhook トリガーの詳細については、「[Azure Functions における HTTP と Webhook のバインド](functions-bindings-http-webhook.md)」を参照してください。
