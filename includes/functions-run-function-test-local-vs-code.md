---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/28/2020
ms.author: glenga
ms.openlocfilehash: a3cb0986637d5ce238930fb87aef71fed684097a
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/07/2020
ms.locfileid: "96842286"
---
## <a name="run-the-function-locally"></a>関数をローカルで実行する

Visual Studio Code を [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) と統合することで、このプロジェクトをローカルの開発用コンピューター上で実行してから、Azure に発行することができます。

1. 関数を呼び出すには、<kbd>F5</kbd> キーを押して関数アプリ プロジェクトを起動します。 Core Tools からの出力が **ターミナル** パネルに表示されます。 Windows での実行に問題がある場合、Visual Studio Code の既定のターミナルが **WSL Bash** に設定されていないことをご確認ください。

1. Azure Functions Core Tools をまだインストールしていない場合は、プロンプトで **[インストール]** を選択します。 Core Tools がインストールされると、アプリが **ターミナル** パネルで起動します。 HTTP によってトリガーされる関数の URL エンドポイントがローカルで実行されていることを確認できます。

    ![ローカル関数の VS Code の出力](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Core Tools が実行されている状態で、次の URL に移動して、`?name=Functions` クエリ文字列を含む GET 要求を実行します。

    `http://localhost:7071/api/HttpExample?name=Functions`

1. 応答が返され、ブラウザーに次のように表示されます。

    ![ブラウザー - localhost の出力例](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. 要求に関する情報が **[ターミナル]** パネルに表示されます。

    ![タスク ホストの開始 - VS Code のターミナル出力](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. <kbd>Ctrl + C</kbd> キーを押して Core Tools を停止し、デバッガーの接続を解除します。
