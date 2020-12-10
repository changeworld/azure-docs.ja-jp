---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/19/2020
ms.author: glenga
ms.openlocfilehash: a5c113849296275432acf1f5603377a1909a2c04
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/07/2020
ms.locfileid: "96842260"
---
## <a name="run-the-function-locally"></a>関数をローカルで実行する

Azure Functions プロジェクトをローカルで実行してデバッグするために、Azure Functions Core Tools は Visual Studio Code と統合されています。 Visual Studio Code をデバッグする方法の詳細については、「[PowerShell Azure Functions をローカル環境でデバッグする](../articles/azure-functions/functions-debug-powershell-local.md)」を参照してください。 
1. 関数を呼び出すには、<kbd>F5</kbd> キーを押して関数アプリ プロジェクトを起動します。 Core Tools からの出力が **ターミナル** パネルに表示されます。 Windows での実行に問題がある場合、Visual Studio Code の既定のターミナルが **WSL Bash** に設定されていないことをご確認ください。

1. **ターミナル** パネルで、HTTP によってトリガーされる関数の URL エンドポイントをコピーします。

    ![Azure のローカル出力](./media/functions-run-function-test-local-vs-code-ps/functions-vscode-f5.png)

1. 次のように、この URL にクエリ文字列 `?name=<yourname>` を追加し、2 つ目の PowerShell コマンド プロンプトで `Invoke-RestMethod` を使用して要求を実行します。

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    ブラウザーから次の URL で GET 要求を実行することもできます。

    `http://localhost:7071/api/HttpExample?name=PowerShell`

    クエリ パラメーターとして、または本文で `name` パラメーターを渡さずに HttpTrigger エンドポイントを呼び出すと、関数は `BadRequest` エラーを返します。 run.ps1 でコードを確認すると、このエラーが仕様によって発生することがわかります。

1. 要求に関する情報が **[ターミナル]** パネルに表示されます。

    ![[ターミナル] パネルでの関数の実行](./media/functions-run-function-test-local-vs-code-ps/function-execution-terminal.png)

1. 完了したら、**Ctrl + C** キーを押して Core Tools を停止します。

関数がローカル コンピューター上で正常に動作することを確認したら、プロジェクトを Azure に発行します。
