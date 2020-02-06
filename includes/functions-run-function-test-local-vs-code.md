---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f4075b8d05c179e8115ff46c9f82751817372491
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842218"
---
## <a name="run-the-function-locally"></a>関数をローカルで実行する

Visual Studio Code を [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) と統合することで、このプロジェクトをローカルの開発用コンピューター上で実行してから、Azure に発行することができます。

1. 関数を呼び出すには、F5 キーを押して関数アプリ プロジェクトを起動します。 Core Tools からの出力が**ターミナル** パネルに表示されます。

1. Azure Functions Core Tools をまだインストールしていない場合は、プロンプトで **[インストール]** を選択します。 Core Tools がインストールされると、アプリが**ターミナル** パネルで起動します。

1. **ターミナル** パネルで、HTTP によってトリガーされる関数の URL エンドポイントをコピーします。 

    ![Azure のローカル出力](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. HTTP 要求の URL をブラウザーのアドレス バーに貼り付けます。 この URL にクエリ文字列 `?name=<yourname>` を追加して、GET 要求を実行します。 

1. 応答が返され、ブラウザーに次のように表示されます。

    ![ブラウザーでの関数 localhost の応答](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Shift + F5 キーを押して Core Tools を停止し、デバッガーの接続を解除します。
