---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 0bb0c22227946cba6790b536b3cb8db24af3ccbc
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422865"
---
## <a name="run-the-function-locally"></a>関数をローカルで実行する

1. *LocalFunctionProj* フォルダーから、ローカルの Azure Functions ランタイム ホストを起動して関数を実行します。

    ```
    func start
    ```

    出力の最後の方に、次の行があります。 
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    >[!NOTE]  
    > HttpExample が以下のように表示されない場合、プロジェクトのルート フォルダー以外からホストを起動したと考えられます。 その場合は **Ctrl** + **C** キーを使用してホストを停止し、プロジェクトのルート フォルダーに移動して、前出のコマンドを再度実行してください。

1. この出力から `HttpExample` 関数の URL をブラウザーにコピーし、クエリ文字列 `?name=<YOUR_NAME>` を追加して、URL 全体を `http://localhost:7071/api/HttpExample?name=Functions` のようにします。 `Hello Functions` のようなメッセージがブラウザーに表示されます。

    ![ブラウザーでローカルに関数を実行した結果](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

1. 要求を行うと、プロジェクトを起動したターミナルにもログ出力が表示されます。

1. 完了したら、**Ctrl** + **C** キーを押し、`y` を選択して関数ホストを停止してください。
