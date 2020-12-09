---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 55a75651b724a4fe975f655958e36fbd40e35db7
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2020
ms.locfileid: "96748262"
---
## <a name="run-the-function-in-azure"></a>Azure で関数を実行する

1. **[Azure: Functions]** 領域 (サイド バー内) に戻り、サブスクリプションの下にある新しい関数アプリを展開します。 **[Functions]** を展開し、 **[HttpExample]** を右クリック (Windows) または <kbd>Ctrl キーを押しながら</kbd>クリック (macOS) して、 **[Copy function URL]\(関数 URL のコピー\)** を選択します。

    ![新しい HTTP トリガーの関数 URL をコピーします](./media/functions-vs-code-run-remote/function-copy-endpoint-url.png)

1. HTTP 要求の URL をブラウザーのアドレス バーに貼り付け、この URL の最後に `name` クエリ文字列を `?name=Functions` として追加して、要求を実行します。 HTTP によってトリガーされる関数を呼び出す URL は、次の形式である必要があります。

    ```http
    http://<FUNCTION_APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions
    ```

    関数によって返されたリモート GET 要求に対するブラウザーでの応答を次の例に示します。

    ![ブラウザーでの関数の応答](./media/functions-vs-code-run-remote/functions-test-remote-browser.png)
