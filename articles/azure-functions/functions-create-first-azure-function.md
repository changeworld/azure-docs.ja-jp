---
title: "初めての Azure 関数の作成 | Microsoft Docs"
description: "初めての Azure 関数、つまりサーバー不要のアプリケーションを&2; 分以内に作成します。"
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
ms.date: 02/09/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 7743bfb98552f7fa2334d8daa6a6f6935969f393
ms.openlocfilehash: d76630e0be4a021720d88e6d7e64cf2258f84266
ms.lasthandoff: 02/10/2017


---
# <a name="create-your-first-azure-function"></a>初めての Azure 関数の作成
## <a name="overview"></a>概要
Azure Functions は、他の Azure サービス、SaaS 製品、オンプレミス システムで発生するイベントによってトリガーされるコードを実装する機能により、既存の Azure アプリケーション プラットフォームを拡張する、イベント ドリブンのオンデマンド コンピューティング エクスペリエンスを提供します。 Azure Functions を利用すると、アプリケーションが要求に応じてスケーリングされ、使用したリソースに対してのみ課金が行われます。 Azure Functions では、スケジュールやトリガーが設定されたコード ユニットを、さまざまなプログラミング言語で作成して実装できます。 Azure Functions の詳細については、「 [Azure Functions の概要](functions-overview.md)」を参照してください。

このトピックでは、ポータルで Azure Functions のクイックスタートを使用して、HTTP トリガーによって呼び出される簡単な "hello world" JavaScript 関数を作成する方法について説明します。 また、ポータルでこれらの手順を実行する方法についての短いビデオも視聴できます。

## <a name="watch-the-video"></a>ビデオを見る
次のビデオでは、このチュートリアルの基本的な手順を実行する方法について説明します。 

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-your-first-Azure-Function-simple/player]
> 
> 

## <a name="create-a-function-from-the-quickstart"></a>クイック スタートから関数を作成する
関数アプリは Azure での関数の実行をホストします。 次の手順に従って、新しい関数を持つ関数アプリを作成できます。 この関数アプリは、既定の構成で作成されます。 Function App を明示的に作成する方法の例については、 [別の Azure Functions クイック スタート チュートリアル](functions-create-first-azure-function-azure-portal.md)を参照してください。

初めての関数を作成するには、アクティブな Azure アカウントを用意しておく必要があります。 Azure アカウントがない場合は、 [無料アカウントを利用できます](https://azure.microsoft.com/free/)。

1. [Azure Functions ポータル](https://functions.azure.com/signin) に移動し、Azure アカウントでサインインします。
2. 新しい関数アプリの一意の**名前**を入力するか、自動生成された名前をそのまま使用し、希望の**リージョン**を選んで、**[作成 + 開始]** をクリックします。 有効な名前 (文字、数字、およびハイフンのみを含めることができます) を入力する必要があることに注意してください。 アンダースコア (**_**) は、許可されている文字ではありません。
3. **[クイック スタート]** タブで、**[WebHook + API]**、**[JavaScript]**、**[関数を作成する]** の順にクリックします。 新しい定義済みの JavaScript 関数が作成されます。 
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)
4. (オプション) クイック スタートのこの時点で、ポータルの Azure Functions 機能のクイック ツアーを開始するかどうか選択することができます。 ツアーを完了するかスキップしたら、HTTP トリガーを使用して新しい関数をテストできます。

## <a name="test-the-function"></a>関数をテストする
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="next-steps"></a>次のステップ
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


