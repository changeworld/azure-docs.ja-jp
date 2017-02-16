---
title: "webhook または API Azure Function を作成する | Microsoft Docs"
description: "Azure Function を使用して WebHook または API 呼び出しによって呼び出される関数を作成します。"
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
ms.date: 08/30/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 47a89987b65b442c7b489984a4fc139eb1b82758
ms.openlocfilehash: 8efde94c5771212b4549f10882a4e55739231d61


---
# <a name="create-a-webhook-or-api-azure-function"></a>webhook または API Azure Function を作成する
Azure Functions では、イベント ドリブンでオンデマンドのコンピューティング体験により、スケジュールやトリガーが設定されたコード ユニットを、さまざまなプログラミング言語で作成できます。 Azure Functions の詳細については、「 [Azure Functions の概要](functions-overview.md)」を参照してください。

このトピックでは、GitHub webhook で呼び出される Node.js 関数を作成する方法を示します。 この新しい関数は、Azure Functions ポータルで定義済みテンプレートに基づいて作成されます。 また、ポータルでこれらの手順を実行する方法についての短いビデオも視聴できます。

## <a name="watch-the-video"></a>ビデオを見る
次のビデオでは、このチュートリアルの基本的な手順を実行する方法について説明します。 

>[!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-a-Web-Hook-or-API-Azure-Function/player]
>
>

## <a name="create-a-webhook-triggered-function-from-the-template"></a>webhook でトリガーされる関数をテンプレートから作成する
関数アプリは Azure での関数の実行をホストします。 関数を作成するには、アクティブな Azure アカウントを用意する必要があります。 Azure アカウントがない場合は、 [無料アカウントを利用できます](https://azure.microsoft.com/free/)。 

1. [Azure Functions ポータル](https://functions.azure.com/signin) に移動し、Azure アカウントでサインインします。

2. 使用する既存の関数アプリがある場合は、**[Your function apps (関数アプリ)]** から選択し、**[開く]** をクリックします。 関数アプリを作成するには、新しい関数アプリ用に一意の **[名前]** を入力するか、生成された名前をそのまま使用し、希望する **[リージョン]** を選択して **[作成 + 開始]** をクリックします。 

3. 関数アプリで、**[+ 新しい関数]** > **[GitHub Webhook - Node (GitHub Webhook - Node)]** > **[作成]** をクリックします。 この手順により、指定したテンプレートに基づく既定の名前の関数が作成されます。 
   
    ![GitHub webhook でトリガーされる関数の作成](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook.png) 

4. **[開発]** の **[コード]** ウィンドウに、サンプルの express.js 関数が表示されます。 この関数は、問題のコメント webhook から GitHub 要求を受信し、問題のテキストをログに記録し、webhook への応答を `New GitHub comment: <Your issue comment text>`として送信します。

    ![関数コードの確認](./media/functions-create-a-web-hook-or-api-function/functions-new-webhook-in-portal.png) 

1. **[関数の URL]** と **[GitHub シークレット]** の値をコピーします。 これらの値は GitHub で webhook を作成するために必要です。 

2. 下へスクロールして **[実行]** を表示し、要求本文で問題のコメントの定義済みの JSON 本文を確認して、**[実行]** をクリックします。 
   
    新しいテンプレート ベースの関数は、**[開発]** タブでいつでも直接テストできます。テストするには、必要な本文 JSON データを指定し、**[実行]** をクリックします。 ここでは、テンプレートに問題のコメントの定義済み本体が含まれています。 

次に、GitHub リポジトリ内の実際の webhook を作成します。

## <a name="configure-the-webhook"></a>webhook を構成する
1. GitHub で、自分が所有するリポジトリに移動します。 フォークした任意のリポジトリを使用することもできます。
 
2. **[設定]** > **[Webhooks & services (Webhook とサービス)]** > **[Add webhook (webhook の追加)]** をクリックします。
   
    ![GitHub webhook の追加](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook-2.png)   
3. 関数の URL とシークレットを **[Payload URL (ペイロード URL)]** と **[シークレット]** に貼り付け、**[Let me select individual events (個々のイベントを選択する)]** をクリックします。次に、**[Issue comment (問題のコメント)]** を選択し、**[Add webhook (webhook の追加)]** をクリックします。
   
    ![webhook URL とシークレットの設定](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook-3.png) 

この時点で、GitHub の webhook は、新しい問題のコメントが追加された場合に関数をトリガーするよう構成されます。  
では、テストしてみましょう。

## <a name="test-the-function"></a>関数をテストする
1. GitHub リポジトリで、**[問題]** タブを新しいブラウザー ウィンドウで開きます。**[新しい問題]** をクリックしてタイトルを入力し、**[Submit new issue (新しい問題を送信)]** をクリックします。 既存の問題を開くこともできます。

2. 問題にコメントを入力し、 **[コメント]**をクリックします。 この時点で、GitHub の新しい webhook に戻ると、**[Recent Deliveries (最近の配信)]** の下に、webhook 要求が送信され、応答の本文が `New GitHub comment: <Your issue comment text>` であることが示されています。

3. Functions ポータルに戻り、ログまで下にスクロールすると、関数がトリガーされ、値 `New GitHub comment: <Your issue comment text>` がストリーミング ログに書き込まれています。

## <a name="next-steps"></a>次のステップ
Azure Functions の詳細については、次のトピックを参照してください。

* [Azure Functions 開発者向けリファレンス](functions-reference.md)  
   関数のコーディングに関するプログラマ向けリファレンスです。
* [Azure Functions のテスト](functions-test-a-function.md)  
   関数をテストするための各種ツールと手法について説明します。
* [Azure Functions のスケーリング方法](functions-scale.md)  
  Azure Functions で利用できるサービス プラン (従量課金ホスティング プランを含む) と、適切なプランを選択する方法について説明します。  

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Nov16_HO4-->


