---
title: "初めての Azure 関数の作成 | Microsoft Docs"
description: "初めての Azure 関数、つまりサーバー不要のアプリケーションを 2 分以内に作成します。"
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 4a1669e7-233e-4ea2-9b83-b8624f2dbe59
ms.service: functions
ms.devlang: multiple
ms.topic: hero-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/14/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: ae5837b4fce52aad4c8b39434c27c450aafc1310
ms.openlocfilehash: 6f42f79abed80df12148463e25935893a4bbdcde


---
# <a name="create-your-first-azure-function"></a>初めての Azure 関数の作成
## <a name="overview"></a>Overview
Azure Functions は、他の Azure サービス、SaaS 製品、オンプレミス システムで発生するイベントによってトリガーされるコードを実装する機能により、既存の Azure アプリケーション プラットフォームを拡張する、イベント ドリブンのオンデマンド コンピューティング エクスペリエンスを提供します。 Azure Functions を利用すると、アプリケーションが要求に応じてスケーリングされ、使用したリソースに対してのみ課金が行われます。 Azure Functions では、スケジュールやトリガーが設定されたコード ユニットを、さまざまなプログラミング言語で作成して実装できます。 Azure Functions の詳細については、「 [Azure Functions の概要](functions-overview.md)」を参照してください。

このトピックでは、ポータルで Azure Functions のクイック スタートを使用して、HTTP トリガーによって呼び出される簡単な "hello world" Node.js 関数を作成する方法について説明します。 また、ポータルでこれらの手順を実行する方法についての短いビデオも視聴できます。

## <a name="watch-the-video"></a>ビデオを見る
次のビデオでは、このチュートリアルの基本的な手順を実行する方法について説明します。 

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-your-first-Azure-Function-simple/player]
> 
> 

## <a name="create-a-function-from-the-quickstart"></a>クイック スタートから関数を作成する
関数アプリは Azure での関数の実行をホストします。 次の手順に従って、新しい関数を持つ関数アプリを作成できます。 この関数アプリは、既定の構成で作成されます。 Function App を明示的に作成する方法の例については、 [別の Azure Functions クイック スタート チュートリアル](functions-create-first-azure-function-azure-portal.md)を参照してください。

初めての関数を作成するには、アクティブな Azure アカウントを用意しておく必要があります。 Azure アカウントがない場合は、 [無料アカウントを利用できます](https://azure.microsoft.com/free/)。

1. [Azure Functions ポータル](https://functions.azure.com/signin) に移動し、Azure アカウントでサインインします。
2. 新しい関数アプリの一意の**名前**を入力するか、生成された名前をそのまま使用し、希望の**リージョン**を選択して、**[作成 + 開始]** をクリックします。 
3. **[クイック スタート]** タブで、**[WebHook + API]**、**[JavaScript]**、**[関数を作成する]** の順にクリックします。 新しい定義済みの Node.js 関数が作成されます。 
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)
4. (オプション) クイック スタートのこの時点で、ポータルの Azure Functions 機能のクイック ツアーを開始するかどうか選択することができます。 ツアーを完了するかスキップしたら、HTTP トリガーを使用して新しい関数をテストできます。

## <a name="test-the-function"></a>関数をテストする
Azure Functions クイック スタートには機能コードが含まれているため、新しい関数をすぐにテストできます。

1. **[開発]** タブで **[コード]** ウィンドウを確認すると、この Node.js コードでは、HTTP 要求で *name* 値をメッセージ本文またはクエリ文字列で渡す必要があることがわかります。 関数を実行すると、応答メッセージでこの値が返されます。
   
2. **[テスト]** をクリックすると、その関数について組み込みの HTTP テスト要求ウィンドウが表示されます。
 
    ![](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

2. **[要求本文]** ボックスで *name* プロパティの値を自分の名前に変更して、**[実行]** をクリックします。 テスト HTTP 要求によって実行がトリガーされ、ストリーミング ログに情報が書き込まれて、"hello" という応答が **[出力]**に表示されます。
 
3. 同じ関数の実行を別のブラウザー ウィンドウやタブからトリガーするには、**[開発]** タブから **[関数の URL]** の値をコピーしてブラウザーのアドレス バーに貼り付けたうえで、 そこにクエリ文字列値 `&name=yourname` を追加して Enter キーを押します。 同じ情報がログに書き込まれ、ブラウザーに "hello" という応答が前と同じように表示されます。

## <a name="next-steps"></a>次のステップ
このクイック スタートでは、基本的な HTTP トリガーによる関数を簡単に実行する方法を紹介しました。 アプリで Azure Functions を使用する方法についてさらに詳しく知りたい場合には、以下のトピックを参照してください。

* [Azure Functions のベスト プラクティス](functions-best-practices.md)
* [Azure Functions 開発者向けリファレンス](functions-reference.md)  
   関数のコーディングとトリガーおよびバインドの定義に関するプログラマ向けリファレンスです。
* [Azure Functions のテスト](functions-test-a-function.md)  
   関数をテストするための各種ツールと手法について説明します。
* [Azure Functions のスケーリング方法](functions-scale.md)  
  Azure Functions で利用できるサービス プラン (従量課金ホスティング プランを含む) と、適切なプランを選択する方法について説明します。 
* [Azure App Service とは](../app-service/app-service-value-prop-what-is.md)  
   Azure Functions では、デプロイ、環境変数、診断などの主要機能に Azure App Service プラットフォームを使用します。 

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Nov16_HO4-->


