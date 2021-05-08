---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: d400533039ea74a878cb8e543c22de02ee77e4f5
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2021
ms.locfileid: "106283147"
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
    > HttpExample が上記のように表示されない場合、プロジェクトのルート フォルダー以外からホストを起動したと考えられます。 その場合は **Ctrl** + **C** キーを使用してホストを停止し、プロジェクトのルート フォルダーに移動して、前出のコマンドを再度実行してください。

1. この出力から `HttpExample` 関数の URL をブラウザーにコピーし、クエリ文字列 `?name=<YOUR_NAME>` を追加して、URL 全体を `http://localhost:7071/api/HttpExample?name=Functions` のようにします。 ブラウザーには、クエリ文字列値をエコー バックする応答メッセージが表示されるはずです。 要求を行うと、プロジェクトを起動したターミナルにもログ出力が表示されます。

1. 完了したら、**Ctrl** + **C** キーを押し、`y` を選択して関数ホストを停止してください。
