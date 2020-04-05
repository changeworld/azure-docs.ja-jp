---
title: Azure Portal で初めての関数を作成する
description: Azure Portal を使用して、サーバーレス実行のための最初の Azure 関数を作成する方法について説明します。
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.topic: quickstart
ms.date: 03/06/2020
ms.custom: mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: e7bb5e7b387c3ab1140a3fe475911bd0e428e2a5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2020
ms.locfileid: "80057059"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Azure Portal で初めての関数を作成する

Azure Functions を使用すると、最初に仮想マシン (VM) を作成したり、Web アプリケーションを発行したりしなくても、サーバーレス環境でコードを実行できます。 この記事では、Azure Functions を使用して Azure portal で "hello world" の HTTP によってトリガーされる関数を作成する方法について学習します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

C# の開発者は、ポータルではなく、[Visual Studio 2019 で最初の関数を作成する](functions-create-your-first-function-visual-studio.md)ことを検討してください。 

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure アカウントで [Azure Portal](https://portal.azure.com) にサインインします。

## <a name="create-a-function-app"></a>Function App を作成する

関数の実行をホストするための Function App が存在する必要があります。 関数アプリを使用すると、リソースの管理、デプロイ、スケーリング、および共有を容易にするための論理ユニットとして関数をグループ化できます。

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

次に、新しい関数アプリで関数を作成します。

## <a name="create-an-http-triggered-function"></a><a name="create-function"></a>HTTP によってトリガーされる関数の作成

1. 新しい関数アプリを展開し、 **[関数]** の横にある **[+]** ボタン、 **[ポータル内]** 、 **[続行]** の順に選択します。

    ![プラットフォームを選択するための Functions のクイック スタート。](./media/functions-create-first-azure-function/function-app-quickstart-choose-portal.png)

1. **[webhook + API]** を選択し、 **[作成]** を選択します。

    ![Azure Portal での関数のクイック スタート。](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

   HTTP によってトリガーされる関数の言語固有のテンプレートを使用して、関数が作成されます。

ここで、HTTP 要求を送信することで、新しい関数を実行できます。

## <a name="test-the-function"></a>関数をテストする

1. 新しい関数で、右上にある **[</> 関数の URL の取得]** を選択します。 

1. **[関数の URL の取得]** ダイアログ ボックスで、ドロップダウン リストから **[default (Function key)]\(既定 (関数キー)\)** を選択し、 **[コピー]** を選択します。 

    ![Azure Portal からの関数 URL のコピー](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. 関数 URL をブラウザーのアドレス バーに貼り付けます。 この URL の末尾にクエリ文字列の値 `&name=<your_name>` を追加し、Enter キーを押して要求を実行します。 

    次の例は、ブラウザーでの応答を示しています。

    ![ブラウザーでの関数の応答。](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    要求 URL には、既定では HTTP 経由で関数にアクセスするために必要なキーが含まれています。

1. 関数が実行されると、ログにトレース情報が書き込まれます。 前の実行からのトレース出力を表示するには、ポータルで関数に戻り、画面の下部にある矢印を選択して **[ログ]** を展開します。

   ![Azure Portal の関数ログ ビューアー。](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>次のステップ

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

