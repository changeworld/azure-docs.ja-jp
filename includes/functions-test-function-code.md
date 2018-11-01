---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: f7e023bcfeaa07a4ee9a80ccf4ec17120605c1ba
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133180"
---
## <a name="test"></a>関数をテストする

cURL を使用して、Mac または Linux コンピューターで、または Windows で Bash を使用して、デプロイした関数をテストします。 次の cURL コマンドを実行します。`<app_name>` プレースホルダーを Function App の名前に置き換えます。 URL にクエリ文字列 `&name=<yourname>` を追加します。

```bash
curl https://<app_name>.azurewebsites.net/api/HttpTrigger?name=<yourname>
```  

![ブラウザーに表示された関数の応答。](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

コマンド ラインで使用可能な cURL がない場合は、Web ブラウザーのアドレスに同じ URL を入力します。 再度、`<app_name>` プレースホルダーを関数アプリの名前に置き換え、URL にクエリ文字列 `&name=<yourname>` を追加して、要求を実行します。

    https://<app_name>.azurewebsites.net/api/HttpTrigger?name=<yourname>
   
![ブラウザーに表示された関数の応答。](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
