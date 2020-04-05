---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: d53c41752d57a27ebea9bd60f7e723dab1e7308a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2020
ms.locfileid: "78190865"
---
## <a name="run-the-function-locally"></a>関数をローカルで実行する

*LocalFunctionProj* フォルダーから、ローカルの Azure Functions ランタイム ホストを起動して関数を実行します。

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-javascript,programming-language-python"
```
func start
```
::: zone-end

::: zone pivot="programming-language-typescript"
```
npm install
npm start
```
::: zone-end

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
> HttpExample が以下のように表示されない場合、*HttpExample* フォルダー内からホストを起動したと考えられます。 その場合は **Ctrl** + **C** キーを使用してホストを停止し、親 *LocalFunctionProj* フォルダーに移動してから、前出のコマンドを再度実行してください。

この出力から `HttpExample` 関数の URL をブラウザーにコピーし、クエリ文字列 `?name=<your-name>` を追加して、URL 全体を `http://localhost:7071/api/HttpExample?name=Functions` のようにします。 `Hello Functions` のようなメッセージがブラウザーに表示されます。

![ブラウザーでローカルに関数を実行した結果](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

要求を送信すると、`func start` を実行したターミナルにもログ出力が表示されます。

準備が完了したら、**Ctrl** + **C** キーを押し、`y` を選択して関数ホストを停止してください。