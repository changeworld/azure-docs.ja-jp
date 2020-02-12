---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/16/2020
ms.author: glenga
ms.openlocfilehash: 916aa2552e5dd004ec767df98ce7c78f7320efd0
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964130"
---
## <a name="run-the-function-locally"></a>関数をローカルで実行する

Azure Functions プロジェクトをローカルで実行してデバッグするために、Azure Functions Core Tools は Visual Studio Code と統合されています。  

1. 関数をデバッグするには、デバッガーにアタッチする前に、[`Wait-Debugger`](/powershell/module/microsoft.powershell.utility/wait-debugger?view=powershell-6) コマンドレットへの呼び出しを関数コードに挿入し、F5 を押して関数アプリ プロジェクトを開始してからデバッガーをアタッチします。 Core Tools からの出力が**ターミナル** パネルに表示されます。

1. **ターミナル** パネルで、HTTP によってトリガーされる関数の URL エンドポイントをコピーします。

    ![Azure のローカル出力](./media/functions-run-function-test-local-vs-code-ps/functions-vscode-f5.png)

1. 次のように、この URL にクエリ文字列 `?name=<yourname>` を追加し、2 つ目の PowerShell コマンド プロンプトで `Invoke-RestMethod` を使用して要求を実行します。

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    ブラウザーから次の URL で GET 要求を実行することもできます。

    <http://localhost:7071/api/HttpExample?name=PowerShell>

    クエリ パラメーターとして、または本文で `name` パラメーターを渡さずに HttpTrigger エンドポイントを呼び出すと、関数は `BadRequest` エラーを返します。 run.ps1 でコードを確認すると、このエラーが仕様によって発生することがわかります。

1. 要求に関する情報が **[ターミナル]** パネルに表示されます。

    ![[ターミナル] パネルでの関数の実行](./media/functions-run-function-test-local-vs-code-ps/function-execution-terminal.png)

1. デバッグを停止するには、Ctrl キーを押しながら C キーを押して Core Tools を停止します。

関数がローカル コンピューター上で正常に動作することを確認したら、プロジェクトを Azure に発行します。

> [!NOTE]
> 関数を Azure に公開する前に、`Wait-Debugger` への呼び出しを必ず削除してください。 